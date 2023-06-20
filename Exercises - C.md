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
#include "err.h"
#include "unistd.h"
#include "stdint.h"
#include "stdlib.h"
#include "string.h"

void readAndCat(int fd, uint8_t flag);
void readAndCat(int fd, uint8_t flag) {
        int bytes_count = 0;
        uint8_t lines = 0;

        uint8_t symbol;
        if (flag == 0) {
                while ((bytes_count = read(fd, &symbol, sizeof(symbol))) > 0){
             if((write(1, &symbol, sizeof(symbol))) < 0) {
                err(1, "Error trying to write on the stdout");
             }
                }
                if(bytes_count < 0) {
            err(2, "Error trying to read from file with descriptor %d",fd);
                }
        }
        else if (flag == 1) {
                lines++;
                if ((write(1, &lines, sizeof(lines))) < 0) {
           err(1, "Error trying to write on the stdout");
                }
       while ((bytes_count = read (fd, &symbol, sizeof(symbol))) > 0) {
           if ((write(1, &symbol, sizeof(symbol))) < 0) {
               err(1, "Error trying to write on the stdout");
           }
           if (symbol == '\n'){
              if ((write(1, &lines, sizeof(lines))) < 0) { //does not write the nums on the stdout, if dprintf used - it works
                  err(1, "Error trying to write on the stdout");
              }
               lines += 1;
           }
       }
       if (bytes_count < 0) {
          err(2, "Error trying to read from a file with descriptor %d", fd);
       }
        }
}
int main (int argc, char **argv) {
   uint8_t flag = 0;
   if (argc == 1) {
      readAndCat(0, 0);
   }
   else if (argc > 1) {
     if (strcmp(argv[1], "-n") == 0) {
        flag = 1;
        if (argc == 2) {
           readAndCat(0, 1);
        }
     }
     int fd = 0;
     for (int i = 1 + flag; i < argc; i++) {
         if (strcmp(argv[i], "-") != 0) {
            if ((fd = open(argv[i], O_RDONLY)) < 0) {
               err(3, "Error trying to open file %s", argv[i]);
            }
         }
                 readAndCat(fd, flag);
     }
    close(fd);
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
