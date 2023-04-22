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
