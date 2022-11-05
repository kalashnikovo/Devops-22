##### Домашнее задание к занятию "2. Применение принципов IaaC в работе с виртуальными машинами"
###### Окружение: Windows 10 (сборка 19044.2130); VirtualBox 6.1.38 r153438; Vagrant 2.3.1; включена подсистема Linix для Windows
###### Задача 1
###### Опишите своими словами основные преимущества применения на практике IaaC паттернов.

Паттерны — это способ построения (структуризации) программного кода специальным образом. На практике они используются программистами для того, чтобы решить какую-нибудь проблему, устранить определенную «боль» разработчика. В этом случае предполагается, что существует некоторый перечень общих формализованных проблем (а это так и есть), причем данные проблемы встречаются относительно часто. И вот здесь-то на сцену и выходят паттерны, которые как раз таки и предоставляют способы решения этих проблем.

Преимущество применения паттернов IaaС на практике это возможность единожды описав инфраструктуру многократно её воспроизводить, производить развёртывние идентичных сервера/сред для тестирования/разработки, масштабирование при необходимости. Следующим преимуществом является автоматизация рутинных действий что приводит к снижению трудозатрат на их выполнение - как следствие повышается скорость разработки, выявления и устранения дефектов за счёт более раннего их обнаружения и тестирования на этапе сборки. Автоматизация поставки - позволяет сократить время от этапа разработки до внедрения. Паттерны IaaC позволяют стандартизировать развёртывание инфраструктуры, что снижает вероятность появления ошибок или отклонений связанных с человеческим фактором.

Применение на практике IaaC паттернов позволяет ускорить процесс разработки, снизить трудозатраты на поиск и устранение дефектов, организовать непрерывную поставку продукта

###### Какой из принципов IaaC является основополагающим?

Идемпотентность операций - свойство сценария/операции позволяющее многократно получать/воспроизводить одно и то же состояние объекта (среды) что и при первом применении, т.е. не зависимо от того сколько раз будет проигран сценарий, результат всегда будет идентичен результату полученному в первый раз.

###### Задача 2
###### Чем Ansible выгодно отличается от других систем управление конфигурациями?

- быстро осваивается, достаточно поверхностного понимания синтаксиса YAML и Jinja
- нет необходимости устанавливать специальное ПО на хосты, нужен только SSH и python
- подробная и наглядная документация
- большое количество модулей
- позволяет реализовать принцип идемпотентности в управлении состояниями хостов

###### Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?

Push - позволяет определить когда, куда и какую конфигурацию отправить, так же позволяет проконтролировать результат применения.

###### Задача 3

Установить на личный компьютер:

- VirtualBox
- Vagrant
- Ansible


    C:\Users\Калашников Олег>vboxmanage --version
    6.1.38r153438

    C:\Users\Калашников Олег>vagrant --version
    Vagrant 2.3.1

    phobos@DESKTOP-IVT86P0:~$ ansible --version
    ansible [core 2.13.6rc1]
    config file = /etc/ansible/ansible.cfg
    configured module search path = ['/home/phobos/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
    ansible python module location = /usr/lib/python3/dist-packages/ansible
    ansible collection location = /home/phobos/.ansible/collections:/usr/share/ansible/collections
    executable location = /usr/bin/ansible
    python version = 3.10.6 (main, Aug 10 2022, 11:40:04) [GCC 11.3.0]
    jinja version = 3.0.3
    libyaml = True

###### Задача 4

###### Воспроизвести практическую часть лекции самостоятельно.

- Создать виртуальную машину.


    D:\vagrant\ubuntu-20.04>vagrant up
    Bringing machine 'server1.netology' up with 'virtualbox' provider...
    ==> server1.netology: Checking if box 'bento/ubuntu-20.04' version '202206.03.0' is up to date...
    ==> server1.netology: There was a problem while downloading the metadata for your box
    ==> server1.netology: to check for updates. This is not an error, since it is usually due
    ==> server1.netology: to temporary network problems. This is just a warning. The problem
    ==> server1.netology: encountered was:
    ==> server1.netology:
    ==> server1.netology: The requested URL returned error: 404
    ==> server1.netology:
    ==> server1.netology: If you want to check for box updates, verify your network connection
    ==> server1.netology: is valid and try again.
    ==> server1.netology: Clearing any previously set forwarded ports...
    ==> server1.netology: Clearing any previously set network interfaces...
    ==> server1.netology: Preparing network interfaces based on configuration...
        server1.netology: Adapter 1: nat
        server1.netology: Adapter 2: hostonly
    ==> server1.netology: Forwarding ports...
        server1.netology: 22 (guest) => 20011 (host) (adapter 1)
        server1.netology: 22 (guest) => 2222 (host) (adapter 1)
    ==> server1.netology: Running 'pre-boot' VM customizations...
    ==> server1.netology: Booting VM...
    ==> server1.netology: Waiting for machine to boot. This may take a few minutes...
        server1.netology: SSH address: 127.0.0.1:2222
        server1.netology: SSH username: vagrant
        server1.netology: SSH auth method: private key
        server1.netology: Warning: Connection reset. Retrying...
        server1.netology: Warning: Connection aborted. Retrying...
    ==> server1.netology: Machine booted and ready!
    ==> server1.netology: Checking for guest additions in VM...
    ==> server1.netology: Setting hostname...
    ==> server1.netology: Configuring and enabling network interfaces...
    ==> server1.netology: Mounting shared folders...
        server1.netology: /vagrant => D:/vagrant/ubuntu-20.04
    ==> server1.netology: Machine already provisioned. Run `vagrant provision` or use the `--provision`
    ==> server1.netology: flag to force provisioning. Provisioners marked to run always will still run.

    D:\vagrant\ubuntu-20.04>vagrant ssh
    Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-110-generic x86_64)Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-110-generic x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage

    System information as of Sat 05 Nov 2022 03:39:33 AM UTC

    System load:  0.04               Processes:             106
    Usage of /:   11.1% of 30.63GB   Users logged in:       0
    Memory usage: 19%                IPv4 address for eth0: 10.0.2.15
    Swap usage:   0%                 IPv4 address for eth1: 192.168.56.11


    This system is built by the Bento project by Chef Software
    More information can be found at https://github.com/chef/bento
    Last login: Tue Nov  1 07:26:12 2022 from 10.0.2.2
    vagrant@server1:~$ hostname -f
    server1.netology
    vagrant@server1:~$ cat /etc/*rel*
    DISTRIB_ID=Ubuntu
    DISTRIB_RELEASE=20.04
    DISTRIB_CODENAME=focal
    DISTRIB_DESCRIPTION="Ubuntu 20.04.4 LTS"
    NAME="Ubuntu"
    VERSION="20.04.4 LTS (Focal Fossa)"
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 20.04.4 LTS"
    VERSION_ID="20.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    VERSION_CODENAME=focal
    UBUNTU_CODENAME=focal
    vagrant@server1:~$ exit
    logout
    Connection to 127.0.0.1 closed.
    
    D:\vagrant\ubuntu-20.04>vagrant halt
    ==> server1.netology: Attempting graceful shutdown of VM...
    
    D:\vagrant\ubuntu-20.04>

    D:\vagrant\ubuntu-20.04>vagrant status
    Current machine states:
    
    server1.netology          poweroff (virtualbox)
    
    The VM is powered off. To restart the VM, simply run `vagrant up`
    
    D:\vagrant\ubuntu-20.04>vagrant destroy
        server1.netology: Are you sure you want to destroy the 'server1.netology' VM? [y/N] y
    ==> server1.netology: Destroying VM and associated drives...
    
    D:\vagrant\ubuntu-20.04>vagrant status
    Current machine states:
    
    server1.netology          not created (virtualbox)
    
    The environment has not yet been created. Run `vagrant up` to
    create the environment. If a machine is not created, only the
    default provider will be shown. So if a provider is not listed,
    then the machine is not created for that environment.
    
    D:\vagrant\ubuntu-20.04>

- Зайти внутрь ВМ, убедиться, что Docker установлен с помощью команды
  

    vagrant@server1:~$ sudo -i
    root@server1:~# docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    root@server1:~# docker -v
    Docker version 21.10.11, build b485636
    root@server1:~#
