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

#### Изведете имената на хората с второ име по-късо от 8 (<=7) символа според /etc/passwd // !(>7) = ?

`~$ cat /etc/passwd | cut -d ':' -f5 | cut -d ' ' -f2 | cut -d ',' -f1 | awk '/^.{,7}$/ {print $0}'`

#### Изведете целите редове от /etc/passwd за хората от предходната задача.

` egrep '\s[^, ]{1,7},' `
- means match anything that: starts with withspace, continues not with ',' or withspace and is between 1 and 7 characters

#### Копирайте <РЕПО>/exercises/data/emp.data във вашата home директория.
#### Посредством awk, използвайки копирания файл за входнни данни, изведете:

- общия брой редове  `~$ cat emp.data | awk '{count+=1} END {print count}'`
- третия ред `~$ cat emp.data | awk '{count+=1} {if(count==3) print $0}'`
- последното поле от всеки ред  `~$ cat emp.data | awk '{print $3}'`
- последното поле на последния ред  `~$ cat emp.data | awk 'END{print $3}'`  `cat emp.data | awk 'END{print $NF}'`
- всеки ред, който има повече от 4 полета `~$ cat emp.data | awk '{if(NF>4) {print $0}}'`
- всеки ред, чието последно поле е по-голямо от 4 `~$ cat emp.data | awk '{if($NF>4) {print $0}}'`
- общия брой полета във всички редове  `~$ cat emp.data | awk 'BEGIN{count = 0} {count+=NF} END{print count}'`
- броя редове, в които се среща низът Beth `~$ cat emp.data | awk 'BEGIN {count=0} /Beth/ {count+=1} END{print count}'`
- най-голямото трето поле и редът, който го съдържа  `~$ cat emp.data | awk 'BEGIN{max=0; line=" "} {if($NF>max) {max = $NF; line = $0}} END{print max " " line}'`
- всеки ред, който има поне едно поле
- всеки ред, който има повече от 17 знака
- броя на полетата във всеки ред и самият ред
- първите две полета от всеки ред, с разменени места
- всеки ред така, че първите две полета да са с разменени места
- всеки ред така, че на мястото на първото поле да има номер на реда
- всеки ред без второто поле
- за всеки ред, сумата от второ и трето поле
- сумата на второ и трето поле от всеки ред


#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.

`~$ cat /etc/passwd | cut -c 2-6`

