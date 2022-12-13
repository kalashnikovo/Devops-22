Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"
Задание 1:
Есть скрипт:

a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
Переменная	Значение	Обоснование
с	a + b	выражение a+b было воспринято как строка
d	1 + 2	переменные $a и $b были подставлены в текстовую строку
e	3	в круглых скобках арифметические операции
Задание 2:
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным. В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:

while ((1==1)
do
    curl https://localhost:4757
    if (($? != 0))
    then
	date >> curl.log
    fi
done
Решение:

while ((1==1))
do
   curl https://localhost:4757
   if (($? != 0))
   then
      date >> curl.log
else
    # сервис работает, выходим
    exit 0
    #break # можно использовать в данном случае вместо exit 0
fi
done
Задание 3:
Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла.

Решение:

#!/usr/bin/env bash
hosts=(192.168.0.1 173.194.222.113 87.250.250.242)
attempts=5

for ip in "${hosts[@]}"
   do
    for (( n=1; n<="${attempts}"; n++ ))
    do
        curl -s -o /dev/null --connect-timeout 5 "http://${ip}:80"
        res=$?
        echo "$(date "+%D %T") - host: ${ip} - attempt: ${n} - exit code: ${res}"  >> curl.log
        if ((res==0)); then break; fi
   done
done

vagrant@server1:~$ cat curl.log
12/13/22 03:57:09 - host: 192.168.0.1 - attempt: 1 - exit code: 28
12/13/22 03:57:14 - host: 192.168.0.1 - attempt: 2 - exit code: 28
12/13/22 03:57:19 - host: 192.168.0.1 - attempt: 3 - exit code: 28
12/13/22 03:57:24 - host: 192.168.0.1 - attempt: 4 - exit code: 28
12/13/22 03:57:29 - host: 192.168.0.1 - attempt: 5 - exit code: 28
12/13/22 03:57:29 - host: 173.194.222.113 - attempt: 1 - exit code: 0
12/13/22 03:57:29 - host: 87.250.250.242 - attempt: 1 - exit code: 0
Задание 4:
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

Решение

#!/usr/bin/env bash
hosts=(192.168.0.1 173.194.222.113 87.250.250.242)
attempts=5

for ip in "${hosts[@]}"
do
    res=0
    for (( n=1; n<="${attempts}"; n++ ))
    do
       curl -s -o /dev/null --connect-timeout 5 "http://${ip}:80"
       res=$?
       echo "$(date "+%D %T") - host: ${ip} - attempt: ${n} - exit code: ${res}"  >> curl.log
       if ((res==0)); then break; fi
    done
    if ((res!=0))
    then
      echo "${ip}" > error
      exit $res
    fi
done

vagrant@server1:~$ cat curl.log

12/13/22 04:04:02 - host: 192.168.0.1 - attempt: 1 - exit code: 28
12/13/22 04:04:07 - host: 192.168.0.1 - attempt: 2 - exit code: 28
12/13/22 04:04:12 - host: 192.168.0.1 - attempt: 3 - exit code: 28
12/13/22 04:04:17 - host: 192.168.0.1 - attempt: 4 - exit code: 28
12/13/22 04:04:22 - host: 192.168.0.1 - attempt: 5 - exit code: 28

vagrant@server1:~$ cat error
192.168.0.1
