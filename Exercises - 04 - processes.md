#### Намерете командите на 10-те най-стари процеси в системата.
```shell
~$ ps aux --sort=start | tail -n +2 | head -n 10 | awk '{print $NF}'
```

#### Намерете PID и командата на процеса, който заема най-много виртуална памет в системата.
```shell
ps aux --sort=-vsz | tail -n +2 | head -n 1 | awk '{print $2" " $11}'
```

#### Изведете командата на най-стария процес
```shell
~$ ps aux --sort=start | tail -n +2 | head -n 1 | awk '{print $11 " " $12}'
```

#### Намерете колко физическа памет заемат всички процеси на потребителската група root.
```shell
~$ ps aux | grep root | awk 'BEGIN{sum=0} {sum+=$6} END{print "Physical size of all 'root' processes: " sum}'
```

#### Изведете имената на потребителите, които имат поне 2 процеса, чиято команда е vim (независимо какви са аргументите й).
```shell
~$ps aux --sort=user | grep vim | awk 'BEGIN{name=""; sum=1; curr=""} {curr=$1} name==curr{sum+=1; print name} {name=curr; sum=1}'
```

#### Изведете имената на потребителите, които не са логнати в момента, но имат живи процеси
```shell
~$ ps -e -o user= | grep -v -F -f <(who | cut -d ' ' -f1) | sort| uniq
~$ comm -13 <(who | cut -d ' ' -f1| sort -u) <(ps -e -o user= | sort -u)
```

#### Намерете колко физическа памет заемат осреднено всички процеси на потребителската група root. Внимавайте, когато групата няма нито един процес.
```shell
~$ ps aux | grep root | awk 'BEGIN{sum=0;res=0} {sum+=$6} END{if(NR > 0)print sum/NR; else print "Zero processes"}'
```

#### Намерете всички PID и техните команди (без аргументите), които нямат tty, което ги управлява. Изведете списък само с командите без повторения..
```shell
~$ ps aux --sort=tty | awk 'BEGIN{name=""} $7="?"{print $2 " " $11 }' | uniq -f1
```

#### Да се отпечатат PID на всички процеси, които имат повече деца от родителския си процес.
```shell

```
