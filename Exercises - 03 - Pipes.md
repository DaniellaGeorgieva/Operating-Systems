#### Сортирайте /etc/passwd лексикографски по поле UserID.
```shell
`~$ cat /etc/passwd | sort -t ':' -k3`
```

#### Сортирайте /etc/passwd числово по поле UserID
```shell
`~$ cat /etc/passwd | sort -t ':' -n -k3`
```

#### Изведете само 1-ва и 5-та колона на файла /etc/passwd спрямо разделител ":".
```shell
`~$ cat /etc/passwd | cut -d ':' -f 1,5`
```

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```

#### Отпечатайте потребителските имена и техните home директории от /etc/passwd.
```shell
`~$ cat /etc/passwd | cut -d ':' -f 1,6`
```

#### Отпечатайте втората колона на /etc/passwd, разделена спрямо символ '/'.
```shell
`cat /etc/passwd | cut -d '/' -f 2`
```

#### Изведете броя на байтовете в /etc/passwd 
```shell
`~$ cat /etc/passwd | wc -c`
```
#### Изведете броя на символите в /etc/passwd.
```shell
`~$ cat /etc/passwd | wc -m`
```
#### Изведете броя на редовете  в /etc/passwd.
```shell
`~$ cat /etc/passwd | wc -l`
```

#### С отделни команди, извадете от файл /etc/passwd:
- първите 12 реда 
```shell
`~$ cat /etc/passwd | head -n 12`
```
- първите 26 символа 
```shell
`~$ cat /etc/passwd | head -c 26`
```
- всички редове, освен последните 4 
```shell
`~$ cat /etc/passwd | head -n -4`
```
- последните 17 реда 
```shell
`~$ cat /etc/passwd | tail -n 17`
```
- 151-я ред (или друг произволен, ако нямате достатъчно редове)
```shell
`~$ cat /etc/passwd | head -n 151 | tail -n 1`
```
- последните 4 символа от 13-ти ред (символът за нов ред не е част от реда) 
```shell
`~$ cat /etc/passwd | head -n 13 | tail -n 1 | tail -c 4`
```

#### Запаметете във файл в своята home директория резултатът от командата `df -P`.
#### Напишете команда, която извежда на екрана съдържанието на този файл, без първия ред (хедъра), сортирано по второ поле (numeric).
```shell
`~$ df -P >> file.txt`
`~$ cat file.txt | head -n -1 | sort -n -k 2`
```

#### Запазете само потребителските имена от /etc/passwd във файл users във вашата home директория.
```shell
`~$ cat /etc/passwd | cut -d ':' -f 1 >> users`
```

#### Изпишете всички usernames от /etc/passwd с главни букви.
```shell
`~$ cat users | tr a-z A-Z`
```

#### Изведете колко потребители не изпозват /bin/bash за login shell според /etc/passwd
(hint: 'man 5 passwd' за информация какъв е форматът на /etc/passwd).
```shell
`~$ cat /etc/passwd | cut -d ':' -f6| grep -r bin/bash | wc -l`
`~$ cat /etc/passwd | grep -r /bin/bash | wc -l`
```

#### Изведете само имената на хората с второ име по-дълго от 6 (>6) символа според /etc/passwd
```shell
`:~$ cat /etc/passwd | cut -d ':' -f5 | cut -d ',' -f1 | awk '/^.{6,}$/ {print $0}'`
```

#### Изведете имената на хората с второ име по-късо от 8 (<=7) символа според /etc/passwd // !(>7) = ?
```shell
`~$ cat /etc/passwd | cut -d ':' -f5 | cut -d ' ' -f2 | cut -d ',' -f1 | awk '/^.{,7}$/ {print $0}'`
```

#### Изведете целите редове от /etc/passwd за хората от предходната задача.
```shell
` egrep '\s[^, ]{1,7},' `
- means match anything that: starts with withspace, continues not with ',' or withspace and is between 1 and 7 characters
```

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
- всеки ред, който има поне едно поле `~$ cat emp.data | awk ' {if(NF>=1) {print $0}}'`
- всеки ред, който има повече от 17 знака `~$ cat emp.data | awk '/^.{18,}$/ {print $0}'`
- броя на полетата във всеки ред и самият ред `~$ cat emp.data | awk 'BEGIN{fields = 0; line = ""} {fields=NF; line= $0} {print fields " " line}'`
- първите две полета от всеки ред, с разменени места  `~$ cat emp.data | awk 'BEGIN{fone = " "; ftwo =" "} {fone = $1; ftwo = $2} {print ftwo " " fone}'`
- всеки ред така, че първите две полета да са с разменени места   `~$ cat emp.data | awk 'BEGIN{fone = " "; ftwo =" "} {fone = $1; ftwo = $2} {print ftwo " " fone " " $3}`  `~$ cat emp.data | awk 'BEGIN{temp = ""} {temp = $1; $1 = $2; $2 = temp} {print $0}'`
- всеки ред така, че на мястото на първото поле да има номер на реда `~$ cat emp.data | awk '{$1 = NR;print $0}'`
- всеки ред без второто поле `~$ cat emp.data | awk '{print $1 " " $3}'`
- за всеки ред, сумата от второ и трето поле `~$ cat emp.data | awk 'BEGIN{sum=sum+0} {sum += $2 + $3} END{print sum}'`
- сумата на второ и трето поле от всеки ред  `~$ cat emp.data | awk 'BEGIN{sum=0} {sum = $2 + $3} {print sum}'`


#### Намерете само Group ID-то си от файлa /etc/passwd.
```shell
`~$ cat /etc/passwd | grep s0600078 | cut -d ':' -f4`
```

#### Колко коментара има във файла /etc/services ? Коментарите се маркират със символа #, след който всеки символ на реда се счита за коментар..
```shell
`:~$ cat /etc/services | awk 'BEGIN{count = 0} /^.*#.*$/ {count+=1} END {print count}'`
```

#### Колко файлове в /bin са 'shell script'-oве? (Колко файлове в дадена директория са ASCII text?).
```shell
`~$ find /bin/ -type f | xargs -I {} file {} | grep -E "shell script" | awk 'END{print NR}'`
```

#### Направете списък с директориите на вашата файлова система, до които нямате достъп. Понеже файловата система може да е много голяма, търсете до 3 нива на дълбочина.
```shell
`~$ find / -maxdepth 3 -type d -perm "a="`
```

#### Създайте следната файлова йерархия в home директорията ви:
dir5/file1
dir5/file2
dir5/file3

#### Посредством vi въведете следното съдържание:
file1:
1
2
3

file2:
s
a
d
f

file3:
3
2
1
45
42
14
1
52

  #### Изведете на екрана:
	* статистика за броя редове, думи и символи за всеки един файл 
	```shell
	`~$ wc -lwc dir5/file{1,2,3}`
	```
	* статистика за броя редове и символи за всички файлове 
	```shell
	`~$ cat dir5/file{1,2,3} | wc -l -c`
	```
	* общия брой редове на трите файла 
	```shell
	`~$ cat dir5/file{1,2,3} | wc -l`
	```

#### Във file2 (inplace) подменете всички малки букви с главни.
```shell
`~$ cat dir5/file2 | tr a-z A-Z`  - does not change it in file, only prints it differently
`~$ cat dir5/file2 | tr a-z A-Z >> temp | mv temp dir5/file2`
```

#### Във file3 (inplace) изтрийте всички "1"-ци.
```shell
`~$ cat dir5/file3 | tr -d 1 >> temp | mv temp dir5/file3`
- if we want to delete the whole row containing '1':
```shell
`~$ sed -i '/1/d' dir5/file3`
```

#### Изведете статистика за най-често срещаните символи в трите файла.
```shell
`~$ cat dir5/file{1,2,3} | uniq -c | sort -n -r`
```
- prints the occurrence of each character in each file, then sort it numerically, then in an ascending order

#### Направете нов файл с име по ваш избор, чието съдържание е конкатенирани съдържанията на file{1,2,3}.
```shell
`~$ cat dir5/file{1,2,3} >> newFileFromDir5.txt`
```

#### Прочетете текстов файл file1 и направете всички главни букви малки като запишете резултата във file2.
```shell
`~$ cat dir5/file1 | tr A-Z a-z >> dir5/file2`
```

#### Намерете броя на символите, различни от буквата 'а' във файла /etc/passwd.
```shell
`~$ cat /etc/passwd | sed 's/a//g' | wc -c`
```

#### Намерете броя на уникалните символи, използвани в имената на потребителите от /etc/passwd..
```shell
``
```

#### Отпечатайте всички редове на файла /etc/passwd, които не съдържат символния низ 'ов'..
```shell
`~$ cat /etc/passwd | grep -E -v "^.*ов.*$"`
```

#### Отпечатайте последната цифра на UID на всички редове между 28-ми и 46-ред в /etc/passwd.
```shell
`:~$ cat /etc/passwd | head -n 46 | tail -n 28 | cut -d ':' -f 3 |rev | cut -c 1 | rev`
```

#### Отпечатайте правата (permissions) и имената на всички файлове, до които имате read достъп, намиращи се в директорията /tmp. (hint: 'man find', вижте -readable).
```shell
`~$ find /tmp -type f -readable -printf "%M\t%p"`
- %M - prints file's permissions and %p prints file's name
```

#### Намерете имената на 10-те файла във вашата home директория, чието съдържание е редактирано най-скоро. На първо място трябва да бъде най-скоро редактираният файл. Намерете 10-те най-скоро достъпени файлове.
```shell
`~$ find . -type f -printf "%T@ %p\n" | sort -n -r | tail`
```

#### да приемем, че файловете, които съдържат C код, завършват на `.c` или `.h`. Колко на брой са те в директорията `/usr/include`? Колко реда C код има в тези файлове?.
```shell
`~$ find /usr/include/ -type f -name '*.[c\|h]' | wc -l`
```

#### Използвайки файл population.csv, намерете колко е общото население на света през 2008 година. А през 2016?.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```

#### Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
`~$ cat /etc/passwd | cut -c 2-6`
```


