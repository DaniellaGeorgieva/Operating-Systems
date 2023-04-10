#### Да се напише shell скрипт, който приканва потребителя да въведе низ (име) и изпечатва "Hello, низ"
```shell
  1 #!/bin/bash
  2
  3 read -p "What is your naem: " name
  4 echo "Hello, ${name}"
```

#### Да се напише shell скрипт, който приема точно един параметър и проверява дали подаденият му параметър се състои само от букви и цифри.
```shell
  1 #!/bin/bash
  2
  3 if echo "${1}" | grep -Eq '^[a-z0-9]*$'; then
  4     echo "true"
  5 else
  6     echo "false"
  7 fi
```

#### Да се напише shell скрипт, който приканва потребителя да въведе низ - потребителско име на потребител от системата - след което извежда на стандартния изход колко активни сесии има потребителят в момента.

```shell
  1 #!/bin/bash
  2
  3 read -p "Username: " username
  4 echo "Active sessions: " $(who | grep "^${username}" | wc -l)
```

#### Да се напише shell скрипт, който приканва потребителя да въведе пълното име на директория и извежда на стандартния изход подходящо съобщение за броя на всички файлове и всички директории в нея.

```shell
  1 #!/bin/bash
  2
  3 read -p "Enter directory: " dirname
  4 if [ ! -d $dirname ]
  5 then
  6     echo " $dirname is not a dir"
  7     exit
  8 fi
  9
 10 num_dirs=$(find $dirname -mindepth 1 2>/dev/null -type d | wc -l)
 11 num_files=$(find $dirname -type f | wc -l)
 12 echo "Num of files: ${num_files}"
 13 echo "Num of dirs: ${num_dirs}"

```

#### Да се напише shell скрипт, който чете от стандартния вход имената на 3 файла, обединява редовете на първите два (man paste), подрежда ги по азбучен ред и резултата записва в третия файл.
```shell
  1 #!/bin/bash
  2
  3 if echo "${1}" | grep -Eq '^[a-z0-9]*$'; then
  4     echo "true"
  5 else
  6     echo "false"
  7 fi
```

#### Да се напише shell скрипт, който приема точно един параметър и проверява дали подаденият му параметър се състои само от букви и цифри.
```shell
  1 #!/bin/bash
  2
  3 if echo "${1}" | grep -Eq '^[a-z0-9]*$'; then
  4     echo "true"
  5 else
  6     echo "false"
  7 fi
```

