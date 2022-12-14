# Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`.  

    #### Решение:
    С помощью команды: `strace /bin/bash -c 'cd /tmp'` удалось найти системный вызов наиболее подходящий по смыслу: `chdir("/tmp")`. Описание команды `chdir` по `man 2 chdir`:
    ```
    NAME
       chdir, fchdir - change working directory

    SYNOPSIS
       #include <unistd.h>

       int chdir(const char *path);
       int fchdir(int fd);

    DESCRIPTION
       chdir() changes the current working directory of the calling process to the directory specified in path.
    ```
    То есть данная функция изменяет текущую директорию вызываемого процесса.
---
2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:
    ```bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.  

    #### Решение:
    Изучение вывода `strace` показывает, что команда `file` пытается читать различные magic конфигурационные файлы, например: `/etc/magic`, `/etc/magic.mgc`, `~/.magic`, `~/.magic.mgc` и `/usr/share/misc/magic.mgc` - большинство из которых отсутствуют. Файл же `/usr/share/misc/magic.mgc` является символьной ссылкой указывающей на `/lib/file/magic.mgc` и он имеет размер 7Мб, что наводит на мысль, что он берет данные оттуда. При выполнении команды: `file /lib/file/magic.mgc`, я получил вывод: `/lib/file/magic.mgc: magic binary file for file(1) cmd (version 16) (little endian)`, который говорит о том, что это скомпилированный бинарный файл с определениями.
---
3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

    #### Решение:
    ```bash
    vagrant@vagrant~$ ping localhost > ping.log &
    [1] 10462
    vagrant@vagrant:~$ rm ping.log
    vagrant@vagrant:~$ sudo lsof -p 10462 | grep ping.log
    ping    10462 wowpetr    1w   REG    8,2     7325 12464194 /home/wowpetr/ping.log (deleted)
    vagrant@vagrant:~$ sudo truncate -s 0 /proc/10462/fd/1 # или echo -n | sudo tee /proc/10462/fd/1
    ```
    После выполнения `truncate` файл оказался обнуленным, как и требовалось (если его прочитать через `cat /proc/10462/fd/1`), но размер в lsof в колонке SIZE не уменьшился. В файл продолжалась запись.  

    Также добился удаления файла, воспользовавшись утилитой `reredirect` (https://github.com/jerome-pouiller/reredirect), и командой:
    ```bash
    vagrant@vagrant:~$ sudo reredirect -N -o /dev/null 10462
    ```
    Альтернативно, также закрывал дескриптор при помощи `gdb -p 10462` и команды в его интерпретаторе: `p close(1)`. 
---
4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

    #### Решение:
    Зомби — дочерний процесс в Unix-системе, завершивший своё выполнение, но ещё присутствующий в списке процессов операционной системы, чтобы дать родительскому процессу считать код завершения. Зомби не занимают память (как процессы-сироты), но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом.
---
5. В iovisor BCC есть утилита `opensnoop`:
    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).

    #### Решение:
    За первую секунду не вижу никаких файлов:
    ```bash
    vagrant@vagrant:~$ sudo timeout 1 opensnoop-bpfcc
    PID    COMM               FD ERR PATH
    vagrant@vagrant:~$
    ```
    Но если дать секунды 3, то кое-что появляется:
    ```bash
    vagrant@vagrant:~$ sudo timeout 3 opensnoop-bpfcc
    PID    COMM               FD ERR PATH
    860    vminfo              6   0 /var/run/utmp
    642    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
    642    dbus-daemon        21   0 /usr/share/dbus-1/system-services
    642    dbus-daemon        -1   2 /lib/dbus-1/system-services
    642    dbus-daemon        21   0 /var/lib/snapd/dbus-1/system-services/
    1      systemd            12   0 /proc/624/cgroup
    vagrant@vagrant:~$   
    ```
---
6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.

    #### Решение:
    Программа `uname` использует одноименный системный вызов `uname`. Согласно `man 2 uname`: `Part  of  the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version,
       domainname}.` То есть можно использовать `/proc/sys/kernel/{ostype,hostname,osrelease,version,domainname}` файлы для получения этой информации:
    ```bash
    vagrant@vagrant:~$ cat /proc/sys/kernel/{ostype,hostname,osrelease,version,domainname}
    Linux
    vagrant
    5.4.0-110-generic
    #124-Ubuntu SMP Thu Apr 14 19:46:19 UTC 2022
   (none)
    ```
---
7. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:
    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?

    #### Решение:
    Если разделить команды через `;`, то все команды будут выполнены. Если же разделить команды через `&&` (логическое И), то следующая команда после `&&` будет выполняться только тогда, если код предыдущей равен нулю (успешное завершение).  
    Директиву `set -e` имеет смысл использовать для команд разделенных `&&`, так как выход из скрипта произойдет в этом случае по последней команде, если она завершилась с кодом ошибки.
---
8. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?

    #### Решение:
    Режим bash `set -euxo pipefail` состоит из следующих опций:
    * set -e - выход из скрипта при появлении ненулевого когда возврата
    * set -u - выход из скрипта при использовании переменной, если ей ранее не было присвоено значение
    * set -x - вывод в терминал команд в том виде в котором они выполняются для целей отладки
    * set -o pipefail - данная опция включает режим в котором любая команда в pipeline возвращающая ненулевой код, будет являться кодом ошибки всего pipeline  
   
   Некоторые из вышеперечисленных опций хороши для отладки (`set -e`, `set -x`). Другие же, более подходят для скриптов, обеспечивая поведение подобное другим языкам программирования.
---
9. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

    #### Решение:
    ```bash
    vagrant@vagrant:~$ ps -eo stat --no-headers | sort | uniq -c | sort -nr -k1
         39 I<
         25 S
         13 Ss
          8 I
          7 Ssl
          6 S<
          3 S+
          2 SN
          1 Ss+
          1 S<s
          1 SLsl
          1 Sl
          1 R+ 
    ```
    Если не учитывать дополнительные буквы статуса процессов, то S (interruptible sleep) - самый часто используемый.
    Дополнительные опции состояний процессов согласно man:  
    * <    high-priority (not nice to other users)
    * N    low-priority (nice to other users)
    * L    has pages locked into memory (for real-time and custom IO)
    * s    is a session leader
    * l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
    * \+    is in the foreground process group
