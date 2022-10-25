### Домашнее задание к занятию "3.2. Работа в терминале, лекция 2" 
****
###### Окружение: 
###### ОС: Windows 10 Версия 21H2 (сборка ОС 19044.2130)
###### Vagrant 2.3.1
###### VirtualBox 6.1 (ставил версию VirtualBox 7.0, конфликтует с Vagrant 2.3.1)

1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа: опишите ход своих мыслей, если считаете, что она могла бы быть другого типа.

        type cd
        cd is a shell builtin

cd, в DOS/Windows также доступная как chdir (англ. change directory — изменить каталог) — команда командной строки для изменения рабочего каталога в Unix, DOS и других операционных системах. Она также доступна для использования в скриптах командного интерпретатора или в пакетных файлах. cd обычно встроена в оболочки, такие как Bourne shell, csh, tcsh, bash (где вызывается POSIX-функция языка Си chdir()) и в DOS COMMAND.COM.
Все логично, иначе не понятно, как переходить по директория с файлами.

2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l?

        vagrant@vagrant:~$ grep test1 exampl.txt|wc -l
        1
        vagrant@vagrant:~$ grep test1 exampl.txt -c
        1
3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

        pstree -a -p  | head -n 1 где -a, --arguments     show command line arguments; -p, --show-pids     show PIDs; implies -c; head вывод первую строчку в файле

4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?
        
        ls -l exampl.txt 2> /dev/pts/1

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

        echo 'test1' > exampl.txt|sed 's/test1/test2/g' < exampl.txt > exampl2.txt

6. Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

        echo 'message' > /dev/tty1 Надо авторизоваться или от root запускать.

7.  Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?

        bash 5>&1 запустит bash с fd 5 и перенаправит его на fd 1 (stdout). echo netology > /proc/$$/fd/5 выведет в терминал слово "netology". Это произойдёт потому что echo отправляет netology в fd 5 текущего шелла (подсистема /proc содержит информацию о запущенных процессах по их PID, $$ - подставит PID текущего шелла)

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty?

        Да, cat ~/.bashrc dsfsfsdcfsdvsv 2>&1 1>/dev/pts/0 | sed 's/cat/test/g' > test

9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?

        Набор переменных окружения. Как вариант env

10. Используя man, опишите что доступно по адресам /proc//cmdline, /proc//exe.

        /proc/<PID>/cmdline выведет команду, к которой относится , со всеми агрументами, разделёнными специальными символом '\x0' (это не пробел, cat файла выведёт всё "слипнувшимся")
        /proc/<PID>/exe это симлинк на полный путь к исполняемому файлоу, из которого вызвана программа с этим пидом

11.  Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.
        
        cat /proc/cpuinfo|grep -o 'sse[0-9_]*'|sort -h|uniq
        sse
        sse2
        sse3
        sse4_1
        sse4_2
12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Однако ... not a tty ... Почитайте, почему так происходит, и как изменить поведение.

        Это для работы со скриптами.
        vagrant@vagrant:~$ ssh -t localhost 'tty'
        The authenticity of host 'localhost (127.0.0.1)' can't be established.
        ECDSA key fingerprint is SHA256:8Lb/tKqeyNQllMhaDRbn8d8aX/ef6T2IkQ5EWnKa01s.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        Please type 'yes', 'no' or the fingerprint: yes
        Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
        vagrant@localhost's password:
        /dev/pts/1
        Connection to localhost closed.

13.  Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr.

        https://github.com/nelhage/reptyr#typical-usage-pattern - проект;

14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте? что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.
        Команда tee делает вывод одновременно и в файл, указанный в качестве параметра, и в stdout. В данном примере команда получает вывод из stdin, перенаправленный через pipe от stdout команды echo и т.к. команда запущена от sudo, соответственно имеет повышенные права на запись.
        


        


        

        