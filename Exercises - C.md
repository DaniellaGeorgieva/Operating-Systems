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
