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
#include "fcntl.h"
#include "stdlib.h"
#include "err.h"
#include "unistd.h"
#include "stdbool.h"
#include "string.h"
#include "stdio.h"

int counter = 1;

void writeToStdout (int fd, bool flag);
int openSafe (char* argument);

void writeToStdout (int fd, bool flag) {
        bool endRow = true;
        char c;
        int bytes_read;
        if (flag == true) {
        while((bytes_read = read(fd, &c, sizeof(c))) >0) {
                if (endRow) {
                if((dprintf(1, "%d ", counter)) < 0){
                   errx(5, "Error while formating stdout");
                }
                endRow = false;
                }
            if ((write(1, &c, sizeof(c))) < 0) {
                err(4, "Error while reading to stdout");

            }
            if ( c == '\n'){
                endRow = true;
                counter++;
            }
        }
        if (bytes_read < 0) {
           err(2, "Error while reading from file");
        }
        }
        else {
                while((bytes_read = read(fd, &c, sizeof(c))) > 0) {
                        if((write(1, &c, sizeof(c))) < 0) {
                err(3, "Error while writing to stdout");
                        }

                }
                if (bytes_read < 0) {
           err(2, "Error while reading from file");
                }
        }
}

int openSafe (char* argument){
        int fd;
        if (strcmp(argument, "-") == 0) {
                fd = 0;
                return fd;
        }
        else {
                fd = open(argument, O_RDONLY);
                if(fd == -1) {
           err(1, "Error while opening file %s", argument);
                }
                else {
            return fd;
                }
        }
        return -1;
}

int main (int argc, char **argv) {

        int fd;

        if (argc == 1) {
         writeToStdout(0, false);
        }
        if (argc == 2 && strcmp(argv[1], "-n") == 0){
                writeToStdout(0, true);
        }
        else{
                if(strcmp(argv[1], "-n") == 0){
                        for(int i = 2; i < argc; i++) {
               fd = openSafe(argv[i]);
               writeToStdout(fd, true);
                        }
                }
                else {
                        for(int i = 1; i < argc; i++){
               fd = openSafe(argv[i]);
               writeToStdout(fd, false);
                        }
                }
        }
    exit(0);
}

````


### 80) 2016-SE-01 
````C
#include "fcntl.h"
#include "stdlib.h"
#include "unistd.h"
#include "err.h"
#include "sys/wait.h"
int main (int argc, char **argv) {
    if (argc != 2) {
       errx(1, "Invalid number of arguments");
    }

    int pipefd[2];
    if ((pipe(pipefd)) == -1) {
        err(2, "Error trying to create a pipe");
    }
    pid_t ch = fork();
    if (ch == -1) {
        err(3, "Error trying to fork process");
    }

    if (ch == 0) {
       close(pipefd[0]);
       if ((dup2(pipefd[1], 1)) == -1) {
          err(4, "Error trying to dup2, to replace stdout by write end of pipe");
          close(pipefd[1]);
       }
       if((execlp("cat", "cat", argv[1], (char*)NULL)) == -1) {
          err(5, "Error while trying to exec cat command");
       }
    }

    int status;
    close(pipefd[1]); // close write end
    if ((wait(&status)) == -1) {
       close(pipefd[0]);
       err(6, "Ërror trying to wait for a child process");
    }

    if (!WIFEXITED(status)){
       close(pipefd[0]);
       errx(7, "Error, child process did not exit normally");
    }

    if (WEXITSTATUS(status) != 0) {
       close(pipefd[0]);
       errx(8, "Error, child process did not exit with status 0");
    }

    if ((dup2(pipefd[0], 0)) == -1) {
       err(4, "Error while trying to dup, to replace stdin by read end of pipe");
    }

    if ((execlp("sort", "sort", (char*)NULL)) == -1) {
       err(5, "Error trying to perform exec");
    }
        exit(0);
}

````

### 82) 2017-IN-01 
````C
#include "fcntl.h"
#include "unistd.h"
#include "stdlib.h"
#include "err.h"
#include "sys/wait.h"

int main (void) {
   int a[2];

   if ((pipe(a)) == -1){
      err(1, "Error with first pipe");
   }
   pid_t pd = fork();
   if (pd == -1) {
      err(2, "Error trying to fork");
   }

   if (pd == 0) {
      close(a[0]);
      if ((dup2(a[1], 1)) == -1) {
         err(3, "Error trying to dup");
      }
      if((execlp("cut", "cut", "-d:", "-f7", "/etc/passwd", (char*)NULL)) == -1){
        err(4, "Error trying to exec cut command");
      }
   }
   else {
      close(a[1]);
   }

   int b[2];
   if ((pipe(b)) == -1) {
      err(1, "Error with second pipe");
   }
   if ((pd = fork()) == -1 ) {
      err(2, "Error with second fork");
   }
   if(pd == 0) {
      close(b[0]);
      if ((dup2(a[0], 0)) == -1) {
         err(3, "Error trying to dup");
      }
      if ((dup2(b[1], 1)) == -1) {
         err(3, "Error trying to dup");
      }
      if (execlp("sort", "sort", (char*)NULL) == -1) {
         err(4, "Error trying to exec sort");
      }
   }
   else{
      close(b[1]);
   }

   int c[2];

   if ((pipe(c)) == -1){
      err(1, "Error with third pipe");
   }

   if ((pd = fork()) == -1) {
      err(2, "Error with fork");
   }
   if (pd == 0) {
      close(c[0]);
      if ((dup2(b[0], 0)) == -1){
        err(3, "Error with dup");
      }
      if ((dup2(c[1], 1)) == -1) {
        err(3, "Error with dup");
      }

      if (execlp("uniq", "uniq", "-c", (char*)NULL) == -1) {
        err(4, "Error trying to exec uniq");
      }
   }
   else {
      close(c[1]);
   }
   close(a[0]);
   close(b[0]);

   while(wait(NULL) > 0);
   if((dup2(c[0], 0)) == -1) {
      err(3, "Error with dup");
   }
   if (execlp("sort", "sort", "-n", "-k1", (char*)NULL) == -1) {
      err(4, "Error trying to exec second sort");
   }
   close(c[0]);

   exit(0);
}

````
### 78) 2022-SE-01
````C
#include "err.h"
#include "stdlib.h"
#include "unistd.h"
#include "fcntl.h"
#include "sys/stat.h"
#include "stdint.h"
int open_safe(char* fname, int flags, mode_t mode);
struct compHeader {
    uint32_t magic1;
    uint16_t magic2;
    uint16_t reserved;
    uint64_t count;
};

struct compData {
    uint16_t type;
    uint16_t reserved;
    uint16_t reserved1;
    uint16_t reserved2;
    uint32_t offset1;
    uint32_t offset2;
};

struct dataHeader {
    uint32_t magic;
    uint32_t count;
};

int open_safe (char* fname, int flags, mode_t mode) {
    int fd;
    if((fd = open(fname, flags, mode)) == -1) {
       err(2, "Error while opening file %s", fname);
    }
    return fd;
}
int main (int argc, char **argv) {
    if (argc != 3) {
       errx(1, "Invalid number of arguments");
    }
    struct stat st1;

    int fd1 = open_safe(argv[1], O_RDWR, S_IRWXU);
    int fd2 = open_safe(argv[2], O_RDONLY, S_IRWXU);

        if ((fstat(fd1, &st1)) < 0) {
        err(8, "Error while fstat in file %s", argv[1]);
        }

        if (st1.st_size % 8 != 0) {
       errx(9, "Invalid first file size %s", argv[1]);
        }

        struct stat st2;
        if ((fstat(fd2, &st2)) < 0) {
        err(8, "Error while fstat in file %s", argv[2]);
        }

        if (st2.st_size % 16 != 0) {
        errx(9, "Invalid second file size %s", argv[1]);
        }

        int bytes_count;

        struct dataHeader dH;
        if ((read(fd1, &dH, sizeof(dH))) < 0 ) {
        err(2, "Error while reading header from file %s", argv[1]);
        }

        struct compHeader cH;
        if ((read(fd2, &cH, sizeof(cH))) < 0) {
        err(3, "Error while reading header from file %s", argv[2]);
        }

        if ((st1.st_size - 8)/8 != dH.count ) {
        errx(11, "Count of numbers in header does not match the actual number of elements in the file %s", argv[1]);
        }

        if ((st2.st_size - 16)/8 != cH.count ) {
        errx(11, "Count of numbers in header does not match the actual number of elements in the file %s", argv[1]);
        }
        struct compData cD;
        while ((bytes_count = read(fd2, &cD, sizeof(cD))) > 0) {
        if ((lseek(fd1,8*(2 + cD.offset1),SEEK_SET)) < 0) {
            err(6, "Error while lseek");
        }
        uint64_t num1;
        if ((read(fd1, &num1, sizeof(num1))) < 0) {
           err(7, "Error reading number one");
        }
        if ((lseek(fd1, 8*(cD.offset2 - cD.offset1), SEEK_CUR)) < 0) {
            err(6, "Error while lseek");
        }
        uint64_t num2;
        if ((read(fd1, &num2, sizeof(num2))) < 0) {
            err(7, "Error while reading number two");
        }
        if (cD.type == 0) {
            if (num1 <= num2) {
                continue;
            }
            else {
                if((write(fd1, &num1, sizeof(num1))) < 0) {
                   err(10, "Error while writing to file %s", argv[1]);
                }
                if((lseek(fd1, -8*(cD.offset2 - cD.offset1), SEEK_CUR)) < 0) {
                   err(6, "Error while lseek");
                }
                if((write(fd1, &num2, sizeof(num2))) < 0) {
                   err(10, "Error while writing to file %s", argv[1]);
                }
            }
        }
        else if (cD.type == 1) {
            if (num1 >= num2) {
               continue;
            }
            else {
                if((write(fd1, &num1, sizeof(num1))) < 0) {
                   err(10, "Error while writing to file %s", argv[1]);
                }
                if((lseek(fd1, -8*(cD.offset2 - cD.offset1), SEEK_CUR)) < 0) {
                   err(6, "Error while lseek");
                }
                if((write(fd1, &num2, sizeof(num2))) < 0) {
                   err(10, "Error while writing to file %s", argv[1]);
                }
                        }
        }
        else {
            errx(5, "Invalid type value");
        }
    }

        if (bytes_count < 0) {
        err(4, "Error while reading data from file %s", argv[2]);
        }


    close(fd1);
    close(fd2);
        exit(0);
}

````

### 62) 2017-SE-01
````C
#include "fcntl.h"
#include "err.h"
#include "unistd.h"
#include "sys/stat.h"
#include "stdlib.h"
#include "stdint.h"
struct patch {
    uint16_t offset;
    uint8_t pos1;
    uint8_t pos2;
};

int main (int argc, char** argv) {

        if(argc != 4) {
       errx(1, "Invalid number of arguments");
        }

        int fd1 = open (argv[1], O_RDONLY);
        if (fd1 < 0) {
       err(2, "Error opening file %s", argv[1]);
        }
        int fd2 = open (argv[2], O_RDONLY);
        if (fd2 < 0) {
       err(2, "Error opening file %s", argv[2]);
        }

        int fd3 = open (argv[3], O_WRONLY | O_CREAT | O_TRUNC, S_IRWXU);
        if (fd3 < 0) {
       err(3, "Error while opening file %s", argv[3]);
        }

    struct stat st1;
    struct stat st2;

    if ((fstat(fd1, &st1)) < 0 ) {
       err(6, "Error while fstat");
    }
    if ((fstat(fd2, &st2)) < 0) {
       err(6, "Error while fstat");
    }
    if (st1.st_size != st2.st_size) {
       errx(7, "Size of files does not match");
    }
        uint8_t original;
        int bytes_count;
        uint16_t offset = 0;
        while((bytes_count = read(fd1, &original, sizeof(original))) > 0) {
         uint8_t newOne;
         if((read(fd2, &newOne, sizeof(newOne))) < 0) {
            err(5, "Error while reading from file %s", argv[2]);
         }

         if(original != newOne){
            struct patch ph;
            ph.offset = offset;
            ph.pos1 = original;
            ph.pos2 = newOne;
            if ((write(fd3, &ph, sizeof(ph))) < 0) {
               err(8, "Error while writing to file %s", argv[3]);
            }
         }
        }
        if (bytes_count < 0) {
       err(4, "Error while reading from file %s", argv[1]);
        }

close(fd1);
close(fd2);
close(fd3);

exit(0);

}

````

### 64) 2017-SE-03 
````C
#include "err.h"
#include "unistd.h"
#include "stdlib.h"
#include "stdint.h"
#include "fcntl.h"
#include "sys/stat.h"


struct patch {
   uint16_t offset;
   uint8_t b1;
   uint8_t b2;
}__attribute__((packed));

int main (int argc, char** argv) {

        if(argc != 4)
        {
                errx(1, "Invalid number of arguments");
        }

        int fd1;
        int fd2;
        int fd3;

        if ((fd1 = open(argv[1], O_RDONLY )) < 0) {
       err(2, "Error while opening file %s", argv[1]);
        }
        if ((fd2 = open(argv[2], O_RDONLY)) < 0) {
       err(2, "Error while opening file %s", argv[2]);
        }
        if ((fd3 = open(argv[3], O_WRONLY | O_TRUNC | O_CREAT, S_IRWXU)) < 0) {
       err(2, "Error while opening file %s", argv[3]);
        }

  struct stat stf1;
  if((fstat(fd2, &stf1)) < 0) {
       err(3, "Error while fstat");
  }

  int size = stf1.st_size;
  int bytes_count;
  uint8_t curr;
  while((bytes_count = read(fd2, &curr, sizeof(curr))) > 0) {
        if((write(fd3, &curr, sizeof(curr))) < 0) {
           err(5, "Error while writing to file %s", argv[3]);
        }
  }

        struct patch ph;

        uint16_t offset = 0;
  while ((bytes_count = read(fd1, &ph, sizeof(ph))) > 0) {
       if (ph.offset > size) {
           errx(4, "Invalid offset");
       }
       uint8_t newOne;
       if (ph.offset == offset) {
           newOne = ph.b2;
           uint8_t oldOne;

           if((lseek(fd2, offset, SEEK_SET)) < 0) {
              err(7, "Error while lseek");
           }
           if((read(fd2, &oldOne, sizeof(oldOne))) < 0) {
              err(9, "Error while reading from file %s", argv[2]);
           }

           if(oldOne != ph.b1) {
              errx(10, "Non-existing original byte of patch in file");
           }

           if((lseek(fd3, offset, SEEK_SET)) < 0) {
              err(7, "Error while lseek");
           }
           if((write(fd3, &newOne, sizeof(newOne))) < 0) {
              err(8, "Error while writing to file %s", argv[3]);
           }
        }
           offset++;

     }
        close(fd1);
        close(fd2);
        close(fd3);
        exit(0);
}

````

### 66) 2018-SE-01
````C
#include "err.h"
#include "fcntl.h"
#include "stdlib.h"
#include "unistd.h"
#include "string.h"
#include "stdbool.h"
int main (int argc, char** argv) {

        if(argc != 3)
        {
                errx(1, "Invalid number of arguments");
        }

        char input[1024];

        char set1[1024];
        char set2[1024];


        if((read(0, &input, sizeof(input))) < 0) {
        err(2, "Error while reading from stdin");
        }
        bool buff[256];
        int len = strlen(input);
        if(strcmp(argv[1], "-d") == 0)
        {
                strcpy(set1, argv[2]);
                int lenSet = strlen(set1);

                for(int i = 0; i < lenSet; i++){
           buff[(int)set1[i]] = 1;
                }

                for(int i = 0; i < len; i++){
           if(buff[(int)input[i]] == 0){
             if((write(1, &input[i], sizeof(input[i]))) < 0) {
                err(3,"Error writing to stdout");
             }
           }
                }
        }
        else if (strcmp(argv[1], "-s") == 0)
        {
                strcpy(set1, argv[2]);
                int lenSet = strlen(set1);
                for(int i = 0; i < lenSet; i++){
                        buff[(int)set1[i]] = 1;
                }
                for(int i = 0; i < len; i++){
            if(buff[(int)input[i]] == 1 && input[i] == input[i+1]){
                 continue;
            }

                    else{
                                if((write(1, &input[i], sizeof(input[i]))) < 0) {
                     err(3, "Error while writing to stdout");
                }
            }
        }
        }
        else {
                strcpy(set1, argv[1]);
                strcpy(set2, argv[2]);

                int lenS1 = strlen(set1);
                int lenS2 = strlen(set2);

                if(lenS1 != lenS2) {
            errx(4, "Strings should be the same length");
                }

                for(int i = 0; i < len; i++){
            for(int j = 0; j < lenS1; j++) {
               if (input[i] == set1[j]){
                  if((write(1, &set2[j], sizeof(set2[j]))) < 0) {
                     err(3, "Error while writing to stdout");
                  }
               }
            }
        }
        }
        exit(0);
}

````
### 82) 2017-IN-02
````C
#include "err.h"
#include "stdlib.h"
#include "unistd.h"
#include "fcntl.h"
#include "string.h"
#include "sys/wait.h"
int main (int argc, char** argv) {

        if (argc > 2) {
       errx(1, "Invalid number of arguments");
        }

        char command[100];

        if(argc == 1) {
       stpcpy(command, "echo");
        }
        else if (argc == 2) {
       stpcpy(command, argv[1]);
        }

        if(strlen(command) > 4) {
       errx(2, "Invalid argument str size");
        }

        char buff[100];
        char c;
        int bytes_read = 0;
        int index = 0;
        while((bytes_read = read(0, &c, sizeof(c))) > 0) {
       if(c == '\n' || c == ' ' || c == '\t'){
           buff[index] = '\0';
           index = 0;
           if (strlen(buff) > 4 || strlen(buff) != 0) {
              errx(4, "Invalid size of arguments");
           }

           pid_t pid = fork();
           if (pid < 0) {
              err(5, "Error with fork");
           }
           if (pid == 0) {
                   if((execlp(command, buff, buff, (char*)NULL)) < 0) {
                  err(6, "Error trying to execute command");
                   }
           }
           int status;
           if ((wait(&status)) < 0) {
              err(7, "Error with wait system call");
           }

       }
           else {
          buff[index] = c;
          index++;
           }
        }
        if (bytes_read < 0) {
       err(3, "Error while reading from stdin");
        }

        exit(0);
}

````
### 81) 2016-SE-02
````C
#include "stdlib.h"
#include "fcntl.h"
#include "unistd.h"
#include "err.h"
#include "sys/wait.h"
#include "string.h"
int main (void) {

        char prompt[16] = "Enter command: ";

        if((write(1, &prompt, sizeof(prompt))) < 0) {
        err(1, "Error while writing to stdout");
        }

        char command[20];
        char c;
        int bytes_read;
        int idx = 0;
        while((bytes_read = read(0, &c, sizeof(c))) > 0) {
        command[idx] = c;
        idx++;
        }
        if (bytes_read < 0) {
       err(2,"Error while reading from stdout");
        }
        if(strcmp(command, "exit") == 0){
        exit(0);
        }

        pid_t pid = fork();
        if (pid == -1) {
        err(3, "Error while forking");
        }
        if (pid == 0) {
       if((execlp(command, command, (char*)NULL)) < 0) {
          err(4, "Error while executing");
       }
        }
        int status;
        if((wait(&status)) < 0) {
        err(5, "Error waiting for child to finish");
        }
        if (!WIFEXITED(status)){
        errx(6, "Child did not terminate normally");
        }
        if (WEXITSTATUS(status) != 0) {
       errx(7, "Child finished with exit status not 0");
        }
        exit(0);
}
````
### 84) 2018-SE-01

````C
#include "fcntl.h"
#include "unistd.h"
#include "stdlib.h"
#include "stdlib.h"
#include "err.h"
#include "string.h"
#include "sys/wait.h"
int main (int argc, char** argv) {

        if(argc != 2){
       errx(1, "Invalid number of arguments");
        }
        char dir[50];
        stpcpy(dir, argv[1]);

        int a[2];
        if((pipe(a)) < 0) {
        err(2, "Error while pipe one");
        }

        pid_t pid = fork();
        if(pid == -1) {
        err(3, "Error while first fork");
        }
        if(pid == 0) {
        close(a[0]);
        if ((dup2(a[1], 1)) < 0) {
           err(4, "Error while dup2");
        }
        if ((execlp("find","find", dir, "-mindepth", "1", "-type", "f", "-printf", "%T@ %f\n", (char*)NULL)) < 0) {
            err(5, "Error executing execlp");
        }
        }
        close(a[1]);

        int b[2];
        if ((pipe(b)) < 0) {
        err(2, "Error with pipe");
        }
        pid = fork();
        if(pid == -1) {
       err(3, "Error with second fork");
        }
        if(pid == 0) {
                close(b[0]);
       if((dup2(a[0], 0)) < 0){
          err(4, "Error with dup2");
       }
       if ((dup2(b[1], 1)) < 0) {
          err(4, "Error with dup2");
       }
       if ((execlp("sort", "sort", "-n", "-r", (char*)NULL)) < 0) {
          err(5, "Error with exec");
       }
        }
        close(a[0]);
        close(b[1]);

        int c[2];
        if((pipe(c)) < 0) {
        err(2, "Error with pipe three");
        }

        pid = fork();
        if(pid == -1) {
        err(3, "Error with fork three");
        }

        if (pid == 0) {
       close(c[0]);
       if ((dup2(b[0],0)) < 0){
          err(4, "Error with dup2");
       }
       if ((dup2(c[1], 1)) < 0) {
          err(4, "Error with dup2");
       }
       if ((execlp("head", "head", "-n1", (char*)NULL)) < 0) {
          err(5, "Error with executing head");
       }
        }
        close(b[0]);
        close(c[1]);

        int status;
        if ((wait(&status)) < 0) {
        err(6, "Error with wait system call");
        }
        if(!WIFEXITED(status)) {
        errx(7, "Child process did not terminate nirmally");
        }
        if(WEXITSTATUS(status) != 0) {
        errx(8, "Child process did not exit with 0");
        }

        if((dup2(c[0], 0)) < 0) {
       err(4, "Error with dup2");
        }
        if ((execlp("cut", "cut", "-d", "' '", "-f2", (char*)NULL)) < 0) {
       err(5, "Error with executing cut command");
        }

        close(c[0]);
        exit(0);
}

````
### 88) 2020-SE-03
````C
#include "err.h"
#include "fcntl.h"
#include "unistd.h"
#include "stdlib.h"
#include "sys/wait.h"
#include "stdint.h"
#include "string.h"
#include "sys/stat.h"
#include "stdio.h"
struct t {
        char name[8];
        uint32_t offset;
        uint32_t length;
}__attribute__((packed));

int main(int argc, char** argv) {

        if(argc != 2) {
       errx(1, "Invalid number of arguments");
        }

        int fd = open(argv[1], O_RDONLY);
        if (fd < 0) {
       err(2, "Error while opening file %s",argv[1]);
        }

        struct stat s;
        if ((fstat(fd, &s)) < 0) {
       err(3, "Error while fstat");
        }

        if (s.st_size % sizeof(struct t) != 0) {
       errx(4, "Invalid file size");
        }
        struct t tt;
        int p[2];
        if ((pipe(p)) == -1) {
         err(6, "Error while piping");
        }
        int bytes_read;
        while ((bytes_read = read(fd, &tt, sizeof(tt))) > 0) {
                int fdt;
                if((fdt = open(tt.name, O_RDONLY)) < 0) {
           err(8, "Error while opening file %s", tt.name);
                }
                uint16_t a = 0x00;
                uint16_t buff;
        pid_t pid = fork();
                if (pid == -1) {
                        err(7, "Error while forking");
                }
                if (pid == 0) {
                        close(p[0]);
                                if ((lseek(fdt, tt.offset*2, SEEK_SET)) > 0) {
                                        err(9, "Error while lseek");
                                }
                        for (uint32_t i = 0; i < tt.length && sizeof(buff) > 0; i++) {
                                if ((read(fdt, &buff, sizeof(buff))) < 0) {
                                        err(10, "Error while trying to read from file %s", tt.name);
                                }
                                a = a^buff;
                        }
                        if((write(p[1], &a, sizeof(a))) <0) {
                                err(11, "Error while writing to pipe");
                        }
                        close(p[1]);
                }
        }

        uint16_t aa = 0x00;
        uint16_t buff2;
                close(p[1]);
                int bytes;
                while((bytes = read(p[0], &buff2, sizeof(buff2))) > 0) {
                        aa = aa ^ buff2;
                }
                if (bytes < 0){
                        err(12, "Error while reaing from pipe");
                }
                close(p[0]);

        if (bytes_read < 0 ) {
                err(5, "Error while reading from file %s", argv[1]);
        }

        dprintf(1, "%d", aa);
        exit(0);
}
````

### 60) 2016-SE-03
````C
#include "fcntl.h"
#include "err.h"
#include "stdlib.h"
#include "unistd.h"
#include "sys/stat.h"
#include "stdint.h"

int main (int argc, char** argv) {

        if(argc != 2) {
                errx(1, "Invalid number of arguments");
        }
        int fd = open(argv[1], O_RDWR);
        if (fd < 0) {
                err(2, "Error while opening file %s", argv[1]);
        }

        struct stat s;
        if ((fstat(fd, &s)) < 0) {
       err(3, "Error while fstat");
        }
        if (s.st_size % 4 != 0) {
                errx(4, "Invalid file size");
        }

        int count = s.st_size / 4;

        uint32_t curr;
        uint32_t min;
        int min_idx;

        for (int i = 0; i < count -1; i++){
                min_idx = i;
                if ((lseek(fd, i, SEEK_SET)) < 0){
                        err(5, "Error while lseek");
                }
                if ((read(fd, &min, sizeof(min))) < 0) {
                        err(6, "Error while reading from file %s", argv[1]);
                }
                for(int j = i+1; j < count; j++){
                        if((lseek(fd, j, SEEK_SET)) < 0) {
               err(5, "Error while lseek");
                        }
                        if ((read(fd, &curr, sizeof(curr))) < 0) {
               err(6, "Error while reading from file %s", argv[1]);
                        }
                        if (curr < min) {
               min_idx = j;
                        }
                }
                if (min_idx != i) {
            if ((lseek(fd, min_idx, SEEK_SET)) < 0) {
                err(5, "Error while lseek");
            }
            if((write(fd, &min, sizeof(min))) < 0) {
                err(7, "Error while writing to file");
            }
            if ((lseek(fd, i, SEEK_SET)) < 0) {
                err(5, "Error while lseek");
            }
            if ((write(fd, &curr, sizeof(curr))) < 0) {
                err(7, "Error while writing to file");
            }
                }
        }
        close(fd);
        exit(0);
}

````

### 87) 2020-SE-02
````C
#include "err.h"
#include "fcntl.h"
#include "stdlib.h"
#include "unistd.h"

int main (int argc, char** argv) {

        if (argc != 3) {
                errx(1, "Invalid number of arguments");
        }
        int p[2];
        if ((pipe(p)) == -1 ) {
                err(2, "Error while pipe");
        }
        pid_t pid = fork();
        if (pid == -1) {
                err(3, "Error while forking");
        }
        if (pid == 0) {
                close(p[0]);
                if ((dup2(p[1], 1)) < 0) {
                        err(4, "Error while dup2");
                }
                if ((execlp("xxd", "xxd", argv[1], (char*)NULL)) < 0) {
                        err(5, "Error while executing command");
                }
        }

        close(p[1]);

        int fd = open(argv[2], O_RDWR | O_CREAT | O_TRUNC, S_IRWXU);
        if (fd < 0) {
                err(6, "Error while opening file");
        }

        int bytes_read;
        char current;
        while((bytes_read = read(p[0], &current, sizeof(current))) > 0) {
                if (current == 0x55) {
                        continue;
                }
                if (current == 0x7D) {
                        char toPrint;
                        if((read(p[0], &toPrint, sizeof(toPrint))) < 0) {
                                err(7, "Error while reading from pipe");
                        }
                        toPrint = toPrint ^ 0x20;
                        //check if byte is one of the following in the task
                        if ((write(fd, &toPrint, sizeof(toPrint))) < 0) {
                                err(8, "Error while writing to file %s", argv[2]);
                        }
                }
        }
        if (bytes_read < 0) {
                err(7, "Error while reading from pipe");
        }
        close(p[0]);
        close(fd);

        exit(0);
}

````
