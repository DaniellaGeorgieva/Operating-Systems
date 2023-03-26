#### Сортирайте /etc/passwd лексикографски по поле UserID.

`~$ cat /etc/passwd | sort -t ':' -k3`

#### Сортирайте /etc/passwd числово по поле UserID

`~$ cat /etc/passwd | sort -t ':' -n -k3`

#### Изведете само 1-ва и 5-та колона на файла /etc/passwd спрямо разделител ":".

`~$ cat /etc/passwd | cut -d ':' -f 1,5`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

#### Отпечатайте потребителските имена и техните home директории от /etc/passwd.

`~$ cat /etc/passwd | cut -d ':' -f 1,6`

#### Отпечатайте втората колона на /etc/passwd, разделена спрямо символ '/'.

`cat /etc/passwd | cut -d '/' -f 2`

#### Изведете броя на байтовете в /etc/passwd 
`~$ cat /etc/passwd | wc -c`
#### Изведете броя на символите в /etc/passwd.
`~$ cat /etc/passwd | wc -m`
#### Изведете броя на редовете  в /etc/passwd.
`~$ cat /etc/passwd | wc -l`

#### С отделни команди, извадете от файл /etc/passwd:
- първите 12 реда  `~$ cat /etc/passwd | head -n 12`
- първите 26 символа  `~$ cat /etc/passwd | head -c 26`
- всички редове, освен последните 4  `~$ cat /etc/passwd | head -n -4`
- последните 17 реда `~$ cat /etc/passwd | tail -n 17`
- 151-я ред (или друг произволен, ако нямате достатъчно редове)  `~$ cat /etc/passwd | head -n 151 | tail -n 1`
- последните 4 символа от 13-ти ред (символът за нов ред не е част от реда)  `~$ cat /etc/passwd | head -n 13 | tail -n 1 | tail -c 4`

#### Запаметете във файл в своята home директория резултатът от командата `df -P`.
#### Напишете команда, която извежда на екрана съдържанието на този файл, без първия ред (хедъра), сортирано по второ поле (numeric).

`~$ df -P >> file.txt`
`~$ cat file.txt | head -n -1 | sort -n -k 2`

#### Запазете само потребителските имена от /etc/passwd във файл users във вашата home директория.

`~$ cat /etc/passwd | cut -d ':' -f 1 >> users`

#### Изпишете всички usernames от /etc/passwd с главни букви.

`~$ cat users | tr a-z A-Z`

#### Изведете колко потребители не изпозват /bin/bash за login shell според /etc/passwd
(hint: 'man 5 passwd' за информация какъв е форматът на /etc/passwd).

`~$ cat /etc/passwd | cut -d ':' -f6| grep -r bin/bash | wc -l`
`~$ cat /etc/passwd | grep -r /bin/bash | wc -l`

#### Изведете само имената на хората с второ име по-дълго от 6 (>6) символа според /etc/passwd

`:~$ cat /etc/passwd | cut -d ':' -f5 | cut -d ',' -f1 | awk '/^.{6,}$/ {print $0}'`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

