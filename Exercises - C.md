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
