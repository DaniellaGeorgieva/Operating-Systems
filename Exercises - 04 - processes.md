#### Сортирайте /etc/passwd числово по поле UserID
```shell
`~$ cat /etc/passwd | sort -t ':' -n -k3`
```
