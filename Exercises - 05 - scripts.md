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
  3 read -p "Enter 3 file names: " f1 f2 f3
  4 paste -d'\n' $f1 $f2 | sort -d -i >> $f3
```

#### Да се напише shell скрипт, който чете от стандартния вход име на файл и символен низ, проверява дали низа се съдържа във файла и извежда на стандартния изход кода на завършване на командата с която сте проверили наличието на низа. NB! Символният низ може да съдържа интервал (' ') в себе си.
```shell
  1 #!/bin/bash
  2
  3 read -p "Enter file name and string: " fname str
  4 cat $fname | grep -q $str
  5 echo $?
```
#### Имате компилируем (a.k.a няма синтактични грешки) source file на езика C. Напишете shell script, който да покaзва колко е дълбоко най-дълбокото nest-ване (влагане).
Примерен .c файл:
#include <stdio.h>

int main(int argc, char *argv[]) {

  if (argc == 1) {
		printf("There is only 1 argument");
	} else {
		printf("There are more than 1 arguments");
	}

	return 0;
}
Тук влагането е 2, понеже имаме main блок, а вътре в него if блок.

#### Примерно извикване на скрипта: ./count_nesting sum_c_code.c

#### Изход: The deepest nesting is 2 levels

```shell
 1 #!/bin/bash
  2
  3 grep -o '[{}]' "${1}" | uniq -c | awk 'BEGIN{max=0; curr=0} $2=="{" {curr = curr+$1} $2=="}" {curr = curr-$1} curr>max {max=curr} END{print max}'
```

#### Напишете shell script, който автоматично да попълва файла указател от предната задача по подадени аргументи: име на файла указател, пълно име на човека (това, което очакваме да е в /etc/passwd) и избран за него nickname.
Файлът указател нека да е във формат:
<nickname, който лесно да запомните> <username в os-server>
// може да сложите и друг delimiter вместо интервал

Примерно извикване:
./pupulate_address_book myAddressBook "Ben Dover" uncleBen

#### Добавя към myAddressBook entry-то:
uncleBen <username на Ben Dover в os-server>

```shell

```

#### Напишете shell script, който да приема параметър име на директория, от която взимаме файлове, и опционално експлицитно име на директория, в която ще копираме файлове. Скриптът да копира файловете със съдържание, променено преди по-малко от 45 мин, от първата директория във втората директория. Ако втората директория не е подадена по име, нека да получи такова от днешната дата във формат, който ви е удобен. При желание новосъздадената директория да се архивира.

```shell
  1 #!/bin/bash
  2
  3 sourceD="${1}"
  4 destD="${2}"
  5
  6 if [[ -z "${destD}" ]]; then
  7     destD="$(date +'%Y-%m-%d')"
  8     mkdir -p "${destD}"
  9 fi
 10
 11 if [[ ! -d "${sourceD}" ]]; then
 12     echo "Invalid source name!"
 13 fi
 14
 15 for FILE in $(find "${sourceD}" -maxdepth 1 -mindepth 1 -type f -mmin -45);     do
 16     cp "${FILE}" "${destD}"
 17 done

```

#### Да се напише shell скрипт, който получава при стартиране като параметър в командния ред идентификатор на потребител. Скриптът периодично (sleep(1)) да проверява дали потребителят е log-нат, и ако да - да прекратява изпълнението си, извеждайки на стандартния изход подходящо съобщение. NB! Можете да тествате по същият начин като в 05-b-4300.txt
```shell
  1 #!/bin/bash
  2
  3 if echo "${1}" | grep -Eq '^[a-z0-9]*$'; then
  4     echo "true"
  5 else
  6     echo "false"
  7 fi
```

#### Да се напише shell скрипт, който валидира дали дадено цяло число попада в целочислен интервал. Скриптът приема 3 аргумента: числото, което трябва да се провери; лява граница на интервала; дясна граница на интервала. Скриптът да връща exit status:
- 3, когато поне един от трите аргумента не е цяло число
- 2, когато границите на интервала са обърнати
- 1, когато числото не попада в интервала
- 0, когато числото попада в интервала

#### Примери:
`$ ./validint.sh -42 0 102; echo $?`
1

```shell
1 #!/bin/bash
  2
  3 num="${1}"
  4 leftB="${2}"
  5 rightB="${3}"
  6
  7 if [[ "${#}" -ne 3 ]]; then
  8     echo "Invalid input! Not enough arguments!"
  9 fi
 10
 11 for symb in "${@}"; do
 12     if echo "${symb}" | grep -E -q -v '^[+-]?[0-9]+$'; then
 13         exit 3
 14     fi
 15 done
 16
 17 if [[ "${leftB}" -gt "${rightB}" ]]; then
 18     exit 2
 19 fi
 20
 21 if [[ "${num}" -ge "${leftB}" ]] && [[ "${num}" -le "${rightB}" ]]; then
 22     exit 0
 23 fi
 24 
 25 exit 1

```


#### Да се напише shell скрипт, който форматира големи числа, за да са по-лесни за четене. Като пръв аргумент на скрипта се подава цяло число. Като втори незадължителен аргумент се подава разделител. По подразбиране цифрите се разделят с празен интервал.

#### Примери:
$ ./nicenumber.sh 1889734853
1 889 734 853

$ ./nicenumber.sh 7632223 ,
7,632,223

```shell
  1 #!/bin/bash
  2
  3 number="${1}"
  4 delum="${2}"
  5
  6 if [[ -z "${2}" ]]; then
  7     delum=' '
  8 fi
  9
 10 if echo "${number}" | grep -q "^[0-9]+$"; then
 11     echo "Number is expected"
 12     exit 1
 13 fi
 14
 15 if [[ "${number}" -lt 1000 ]]; then
 16     echo "${number}"
 17     exit 0
 18 fi
 19
 20 while [[ "${number}" -gt 999 ]]; do
 21     outPut=$(echo "${number}" | tail -c 4)$delum$outPut
 22     number=$(("${number}"/1000))
 23 done
 24
 25 outPut=$number$delum$outPut
 26
 27 if [[ "${delum}" != ' ' ]]; then
 28     outPut=$(echo "${outPut}"| head -c -2)
 29 fi
 30
 31 echo "${outPut}"
```


#### Да се напише shell скрипт, който приема файл и директория. Скриптът проверява в подадената директория и нейните под-директории дали съществува копие на подадения файл и отпечатва имената на намерените копия, ако съществуват такива. NB! Под 'копие' разбираме файл със същото съдържание.
```shell
  1 #!/bin/bash
  2
  3 fname="${1}"
  4 dirName="${2}"
  5
  6 if [[ "${#}" -ne 2 ]]; then
  7     echo "More args expected"
  8     exit 1
  9 fi
 10
 11 if [[ ! -d "${dirName}" ]]; then
 12     echo "Dir name is expected"
 13     exit 1
 14 fi
 15
 16 if [[ ! -f "${fname}" ]]; then
 17     echo "File name is expected"
 18     exit 1
 19 fi
 20
 21 for FILE in $(find "${dirName}" -type f); do
 22     if $(test "$(cat "${FILE}")" = "$(cat "${fname}")"); then
 23         echo "${FILE}"
 25 done
 26
 27 exit 0
```



#### Да се напише shell script, който генерира HTML таблица съдържаща описание на потребителите във виртуалката ви. Таблицата трябва да има:
- заглавен ред с имената нa колоните
- колони за username, group, login shell, GECOS field (https://en.wikipedia.org/wiki/Gecos_field)

#### Пример:
$ ./passwd-to-html.sh > table.html
$ cat table.html
<table>
  <tr>
    <th>Username</th>
    <th>group</th>
    <th>login shell</th>
    <th>GECOS</th>
  </tr>
  <tr>
    <td>root</td>
    <td>root</td>
    <td>/bin/bash</td>
    <td>GECOS here</td>
  </tr>
  <tr>
    <td>ubuntu</td>
    <td>ubuntu</td>
    <td>/bin/dash</td>
    <td>GECOS 2</td>
  </tr>
</table>
```shell
  1 #!/bin/bash
  2
  3 if echo "${1}" | grep -Eq '^[a-z0-9]*$'; then
  4     echo "true"
  5 else
  6     echo "false"
  7 fi
```

#### Да се напише shell скрипт, който получава единствен аргумент директория и отпечатва списък с всички файлове и директории в нея (без скритите). До името на всеки файл да седи размера му в байтове, а до името на всяка директория да седи броят на елементите в нея (общ брой на файловете и директориите, без скритите).

a) Добавете параметър -a, който указва на скрипта да проверява и скритите файлове и директории.

Пример:
$ ./list.sh .
asdf.txt (250 bytes)
Documents (15 entries)
empty (0 entries)
junk (1 entry)
karh-pishtov.txt (8995979 bytes)
scripts (10 entries)

```shell
  1 #!/bin/bash
  2
  3 fname="${1}"
  4
  5 if [[ "${#}" -ne 1 ]]; then
  6     echo "Too much args"
  7     exit 1
  8 fi
  9
 10 if [[ ! -d "${fname}" ]]; then
 11     echo "That is not a directory"
 12     exit 1
 13 fi
 14
 15 for FILE in $(find "${fname}"); do
 16     if [[ -d "${FILE}" ]]; then
 17         numFiles=$(find "${FILE}" -mindepth 1 | wc -l)
 18         echo "${FILE} has "${numFiles}" entries in it"
 19     else
 20         numBytes=$(echo "${FILE}" | wc -m)
 21         echo "${FILE} is "${numBytes}" bytes"
 22     fi
 23 done
 24
 25 exit

```

#### 7000 Да се напише shell скрипт, който приема произволен брой аргументи - имена на файлове. Скриптът да прочита от стандартния вход символен низ и за всеки от зададените файлове извежда по подходящ начин на стандартния изход броя на редовете, които съдържат низа. NB! Низът може да съдържа интервал.

```shell
  1 #!/bin/bash
  2
  3 read -p "Input string to search for: " str
  4
  5 if [[ "${#}" -lt 1 ]]; then
  6     echo "You have not written down any files to search in!"
  7     exit 1
  8 fi
  9
 10 for FILE in "${@}"; do
 11     if [[ ! -f "${FILE}" ]]; then
 12         echo "That is not a normal file!"
 13         exit 1
 14     else
 15         numLines=$(grep "${str}" "${FILE}" | wc -l)
 16         echo "Num row containing the str: "${numLines}""
 17     fi
 18 done
 19 exit 0

```

#### 7200 Да се напише shell скрипт, който приема произволен брой аргументи - имена на файлове или директории. Скриптът да извежда за всеки аргумент подходящо съобщение: - дали е файл, който може да прочетем; - ако е директория - имената на файловете в нея, които имат размер, по-малък от броя на файловете в директорията.
```shell
  1 #!/bin/bash
  2
  3 if [[ "${#}" -lt 1 ]]; then
  4     echo "You have not written down any files!"
  5     exit 1
  6 fi
  7
  8 for FILE in "${@}"; do
  9     if [[ -d "${FILE}" ]]; then
 10         numFiles=$(find "${FILE}" -maxdepth 1 | wc -l)
 11         #echo "Num files in dir: "${numFiles}""
 12         for ddFile in $(find "${FILE}" -mindepth 1 -type f); do
 13             fileSize=$(stat -c '%s' "${ddFile}")
 14             #echo "File size is: "${fileSize}""
 15             if [[ "${numFiles}" -gt "${fileSize}" ]]; then
 16                 echo "${ddFile}"
 17             fi
 18         done
 19     else
 20         if [[ -r "${FILE}" ]]; then
 21             echo " "${FILE}" - Yep, it is a file you can read"
 22         fi
 23     fi
 24 done
 25 exit 0

```
