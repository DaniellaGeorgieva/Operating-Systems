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
