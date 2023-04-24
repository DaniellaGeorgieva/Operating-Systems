#### Задачи от сборник:

#### 18)

````shell
#!/bin/bash

comp1="${1}"
comp2="${2}"

if [[ "#{#}" -ne 2 ]]; then
  echo "Invalid number of args"
  exit 1
fi

mkdir a b c

for FILE in $(find . -mindepth 1 -maxdepth 1 -type f); do
  linesInFile=$(cat "${FILE}" | wc -l)
  if [[ "${linesInFile}" -lt "${comp1}" ]]; then
      mv "${FILE}" a
  elif [[ "${linesInFile}" -gt "${comp1}" && "${linesInFile}" -lt "${comp2}" ]]; then
      mv "${FILE}" b
  else
      mv "${FILE}" c
  fi
done
exit 0

````

#### 19)

````shell
#!/bin/bash

fileOne="${1}"
fileTwo="${2}"

if [[ "${#}" -ne 2 ]]; then
        echo "Invalid number of arguments"
        exit 1
fi

numLines1=$(cat "${fileOne}" | grep "${fileOne}" | wc -l )
numLines2=$(cat "${fileTwo}" | grep "${fileTwo}"| wc -l)

if [[ "${numLines1}" -gt "${numLines2}" ]]; then
        cat "${fileOne}" | cut -d '-' -f2- | sort >> ""${fileOne}".songs"
else
        cat "${fileTwo}" | cut -d '-' -f2- | sort >> ""${fileTwo}".songs"
fi

exit 0

````

#### 20)

````shell
#!/bin/bash

fileOne="${1}"

if [[ "${#}" -ne 1 ]]; then
        echo "Invalid number of args"
        exit 1
fi
numLines=$(cat "${fileOne}" | wc -l)
sed -i 's/ /_/g' $fileOne

for Line in $(cat "${fileOne}" | sort -r | awk '{print $0}'); do
        newLine=$(echo "${Line}" | cut -d '_' -f4- | sed 's/_/ /g')
            if [[ "${i}" -eq 1 ]]; then
                    echo " $newLine" > "${fileOne}"
            else
                   echo " $newLine" > "${fileOne}"
            fi
done
exit 0
````

#### 21)

````shell
 1 #!/bin/bash
  2
  3 fileName="${1}"
  4 str1="${2}"
  5 str2="${3}"
  6
  7 if [[ "${#}" -ne 3 ]]; then
  8   echo "Invalid number of args"
  9 fi
 10
 11 line=""
 12 b=""
 13 for sb1 in $(cat $fileName | grep -E "^$str1=" | cut -d '=' -f2 | cut -d ' '     -f1-); do
 14     for sb2 in $(cat $fileName | grep -E "^$str2=" | cut -d '=' -f2 | cut -d     ' ' -f1-); do
 15         if [[ $sb1 == $sb2 ]]; then
 16             b=1
 17             break
 18         fi
 19     done
 20     if [[ $b -ne 1 ]]; then
 21         line+="${sb1} "
 22     else
 23         b=""
 24     fi
 25 done
````

#### 23)

````shell
 1 #!/bin/bash
  2
  3 if [[ "${#}" -ne 0 ]]; then
  4     echo "Invalid number of arguments"
  5 fi
  6
  7 toPrint=$(cat /etc/passwd | cut -d ':' -f6 | grep -E "home" | xargs -I {} fi    nd {} -type f -printf "%AT %f %u\n" 2>/dev/null | sort -nr -k1 | head -n 1 |     cut -d ' ' -f2,3)
  8 echo "${toPrint}"
  9 exit 0

````

#### 24)

````shell
1 #!/bin/bash
  2
  3 if [[ "${#}" -eq 0 ]]; then
  4     echo "Not valid number of args"
  5 fi
  6
  7 if [[ "${#}" -eq 1 ]]; then
  8     find $1 -type l 2>/dev/null -exec [ ! -e {} ] \; -printf "%f\n"
  9 elif [[ "${#}" -eq 2 ]]; then
 10     find $1 -type f -printf "%n %f\n" 2>/dev/null | awk -v n="${2}" 'n<=$1{p    rint $2}'
 11
 12 else
 13     echo "Invalid number of args"
 14 fi
 15
 16 exit 0

````

#### 25)

````shell
1 #!/bin/bash
  2
  3 SRC="${1}"
  4 DEST="${2}"
  5 STR="${3}"
  6
  7 if [[ "${#}" -ne 3 ]]; then
  8     echo "Invalid number of args"
  9     exit 1
 10 fi
 11
 12 if [[ $(whoami) != "s0600078" ]]; then
 13     echo "You are not root. Sorry cannot run it"
 14     exit 1
 15 fi
 16
 17 find "${SRC}" -mindepth 1 -name "*$STR*" -printf "%p\n" -exec mv {} $DEST \;
 18
 19 exit 0
````

#### 26)

````shell
  1 #!/bin/bash
  2
  3 if [[ "${#}" -ne 0 ]]; then
  4     echo "Invalid number of arguments"
  5     exit 1
  6 fi
  7
  8 if [[ $(whoami) != "s0600078" ]]; then
  9     echo "Sorry, you are not root. You cannot run it"
 10     exit 1
 11 fi
 12
 13 ps aux --sort=user | head -n -2 | awk 'BEGIN{curr="";name=$1;sum=0} {curr=$1    } name==curr{sum+=$6} name!=curr{print name" "sum; sum=0; name=curr}'
 14 exit 0
````
#### 27)

````shell
 1 #!/bin/bash
  2
  3 if [[ "${#}" -gt 2  ]]; then
  4     echo "Too much args"
  5     exit 1
  6 fi
  7
  8 if [[ "${#}" -lt 0 ]]; then
  9     echo "Not enough args"
 10     exit 1
 11 fi
 12
 13 if [[ ! -d "${1}" ]]; then
 14     echo "Not a directory"
 15     exit 1
 16 fi
 17
 18
 19 NUMBROKEN=$(find "${1}" -mindepth 1 -type l 2>/dev/null -exec [ ! -e {} ] \;     | wc -l)
 20 OUTPUT=$(find "${1}" -mindepth 1 -type l 2>/dev/null -exec [ -e {} ] \; -pri    ntf "%f %l\n" | sed 's/ / -> /g')
 21
 22 if [[ "${#}" -eq 2 ]]; then
 23     echo "${OUTPUT}" >> "${2}"
 24     echo "Num of broken symlinks: ${NUMBROKEN}" >> "${2}"
 25 else
 26     echo "${OUTPUT}"
 27     echo "Num of broken symlinks: ${NUMBROKEN}"
 28 fi
 29
 30 exit 0

````

#### 28)

````shell
  1 #!/bin/bash
  2
  3 if [[ "${#}" -ne 2 ]]; then
  4     echo "Not right number of args"
  5     exit 1
  6 fi
  7
  8 if [[ ! -d "${1}" ]]; then
  9     echo "Not a directory"
 10     exit 1
 11 fi
 12
 13 CONSTSTR="vmlinuz"
 14 echo $(find "${1}" -mindepth 1 -maxdepth 1 -type f | grep -E "${CONSTSTR}-[0    -9]+.[0-9]+.[0-9]+-${2}$" | awk -F '.' 'BEGIN{max=0;line=""} max<$2{max=$2;     line=$0} END{print line}')
 15
 16
 17 exit 0
````

#### 29)

````shell
  1 #!/bin/bash
  2
  3 if [[ $(whoami) != "s0600078" ]]; then
  4     echo "Sorry, you are not roor. Cannot run it"
  5     exit 1
  6 fi
  7
  8 rootProcesses=$(ps aux | tail -n +2 | grep "root" | awk 'BEGIN{sum=0} {sum+=    $6} END{print sum}')
  9
 10 usersNotRoot=$(ps aux --sort=user | tail -n +2 | cut -d ' ' -f1 | grep -v 'r    oot' | sort -d | uniq)
 11
 12 for USER in $usersNotRoot; do
 13      userDir=$(cat /etc/passwd | grep "${USER}" | cut -d ':' -f6)
 14      if [[ ! -d $userDir  ||  $(stat -c "%U" "${userDir}" 2>/dev/null) != "$    {USER}" ||  $(stat -c "%A" "${userDir}" 2>/dev/null | head -c 3 | tail -c 1)     != 'w' ]]; then
 15          echo "${USER}"
 16          userRSS=$(ps aux | grep "${USER}" | awk 'BEGIN{sum=0} {sum+=$6} END    {print sum}')
 17          if [[ "${userRSS}" -gt "${rootProcesses}" ]]; then
 18                   echo "Yes, it is greater than root's"
 19             ps -e -u $USER -o pid= | xargs -I {} echo "These are for killing {}"
 20          fi
 21     fi
 22 done
 23
 24 exit 0
````

#### 30)

````shell
  1 #!/bin/bash
  2
  3 if [[ "${#}" -ne 1 ]]; then
  4     echo "Sorry, invalid number of args"
  5     exit 1
  6 fi:
  7
  8 if [[ ! -d "${1}" ]]; then
  9     echo "Not directory"
 10     exit 1
 11 fi
 12
 13 dirsToSearch=$(find "${1}" -mindepth 1 -type f -printf "%p\n" | cut -d '/' -    f5 | sort | uniq)
 14
 15 OUTPUT=""
 16 touch out.txt
 17 for fr in $dirsToSearch; do
 18     numLines=$(find "${1}" -mindepth 1 -type f -printf "%p\n" | grep $fr | x    args -I {} cat {} | wc -l)
 19     echo "${fr} ${numLines} " >> out.txt
 20 done
 21
 22 echo "${OUTPUT}"
 23 cat out.txt | sort -nr -t' ' -k2 | head
 24
 25
 26 exit 0
 27
````

#### 31)

````shell

````
