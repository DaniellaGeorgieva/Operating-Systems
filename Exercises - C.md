### 58) 2016-SE-01
````C
#include "err.h"
#include "stdlib.h"
#include "fcntl.h"
#include "unistd.h"
#include "stdint.h"
int main (int argc, char** argv) {
  if (argc != 2){
    errx(1,"Invalid arg num");
  }

  int fd = open(argv[1], O_RDONLY);
  if (fd == -1) {
     err(2, "Error while opening file %s", argv[1]);
  }

  uint32_t bytes[256];
  for (int i = 0; i < 256; i++){
     bytes[i] = 0;
  }

  uint8_t buff;
  int bytes_count = 0;
  while ((bytes_count = read(fd,&buff, sizeof(buff))) > 0){
     bytes[buff] += 1;
  }
  if (bytes_count < 0) {
     err(3, "Error while reading file %s", argv[1]);
  }
  if (lseek(fd, 0, SEEK_SET) < 0) {
     err(4, "Error while trying to seek");
  }
  for (int temp  = 0; temp < 256; temp++){
     uint8_t c = temp;
     for (int j = 0; j < bytes[temp]; j++){
        if (write(fd, &c, sizeof(c)) < 0){
           err(5, "Error while trying to write to file %s", argv[1]);
        }
     }
  }
  close(fd);
  return 0;
}

````

### 59) 2016-SE-02
````C
#include "stdlib.h"
#include "unistd.h"
#include "err.h"
#include "fcntl.h"
#include "stdint.h"
#include "sys/stat.h"
int main(int argc, char **argv)
{
    if (argc != 4){
       errx(1, "Invalid number of arguments");
    }

    int fd1;
    if ((fd1 = open(argv[1], O_RDONLY)) < 0){
       err(2, "Error while trying to open file %s", argv[1]);
    }

    struct stat info;
    int res;
    if ((res = fstat(fd1, &info)) < 0) {
       err(3, "Error while trying to stat %s", argv[1]);
    }
    if (info.st_size % 8 != 0){
       errx(4, "Invalid file size, %s must be divible by 8", argv[1]);
    }

    int fd2;
    if ((fd2 = open (argv[2], O_RDONLY)) < 0) {
       err(2, "Error trying to open file %s", argv[2]);
    }

    uint32_t numsf1[2];
    int bytes_read;
    int fd3;

    if ((fd3 = open (argv[3], O_RDWR | O_CREAT, S_IRWXU)) < 0) {
       err(6, "Error while trying to open file %s", argv[3]);
    }
    while ((bytes_read = read(fd1, &numsf1, sizeof(numsf1))) > 0) {
       if ((lseek(fd2, numsf1[0]*4, SEEK_SET)) < 0) {
          err(5, "Error while trying to lseek in file %s", argv[2]);
       }

       uint32_t c;
       for (uint32_t i = 0; i < numsf1[1]; i++) {
          if ((read(fd2, &c, sizeof(c))) < 0) {
             err(7, "Error trying to read from file %s", argv[2]);
          }
          if((write(fd3, &c, sizeof(c))) < 0) {
             err(8, "Error trying to write from file");
          }
       }
    }

    if (bytes_read < 0) {
      err(6, "Error trying to read from file %s", argv[1]);
    }
    close(fd1);
    close(fd2);
    close(fd3);

    exit(0);
}
````

### 61) 2017-IN-01
````C

#include "err.h"
#include "fcntl.h"
#include "stdint.h"
#include "stdlib.h"
#include "unistd.h"
#include "sys/stat.h"

int openFileRead(const char* name);

struct pos {
   uint16_t offset;
   uint8_t len;
   uint8_t nouse;
};

int openFileRead(const char* name) {
   int fd;
   if ((fd = open(name, O_RDONLY)) < 0) {
      err(2, "Error trying to open file %s", name);
   }
   return fd;
}

int main (int argc, char **argv) {
   if (argc != 5) {
      errx(1, "Invalid number of arguments");
   }

    int fd1 = openFileRead(argv[1]);
    int fd11 = openFileRead(argv[2]);

    struct stat s;
    if ((fstat(fd11, &s)) < 0){
       err(3, "Error trying to obtain stats about file %s", argv[2]);
    }

    if (s.st_size % 4 != 0) {
       errx(4, "Invalid second file size! It must be devisable by 4");
    }

    struct stat s2;
    if((fstat(fd1, &s2)) < 0) {
       err(3, "Error trying to obtain stats about file %s", argv[2]);
    }

    int fd2;
    int fd22;

    if ((fd2 = open(argv[3], O_WRONLY | O_CREAT, S_IRWXU)) < 0) {
        err(2, "Error trying to open file %s", argv[3]);
    }

    if ((fd22 = open(argv[4], O_WRONLY | O_CREAT, S_IRWXU)) < 0) {
        err(2, "Error trying to open file %s", argv[4]);
    }


   struct pos infoF11;
   int bytes_read;
   uint16_t offc = 0;
   while ((bytes_read = read(fd11, &infoF11, sizeof(infoF11))) == sizeof(infoF11)) {
     if (infoF11.offset + infoF11.len > s2.st_size) {
        errx(4, "Invalid size of file %s", argv[1]);
     }
     if ((lseek (fd1, infoF11.offset, SEEK_SET)) < 0) {
       err(5, "Error trying to lseek in file %s", argv[1]);
     }
     uint8_t buff;

     if((read(fd1, &buff, sizeof(buff))) < 0) {
        err(6, "Error trying to read from file %s", argv[1]);
     }
     if (buff < 0x41 || buff > 0x5A) {
        continue;
     }
     struct pos out = {offc, infoF11.len, 0};
     if ((write(fd22, &out, sizeof(out))) < 0) {
       err(7, "Error trying to write in a file %s", argv[4]);
     }

     for (int i = 1; i < infoF11.len - 1; i++){
         if((read(fd1, &buff, sizeof(buff))) < 0) {
           err(6, "Error trying to read from file %s", argv[1]);
         }

         if ((write(fd2, &buff, sizeof(buff))) < 0) {
            err(7, "Error trying to write in file %s", argv[3]);
         }
     }
     offc += infoF11.len;
   }

   if (bytes_read < 0) {
      err(6, "Error trying to read from file %s", argv[2]);
   }
   close(fd1);
   close(fd11);
   close(fd2);
   close(fd22);
   exit(0);
}
````

### 63) 2017-SE-02
````C

````
