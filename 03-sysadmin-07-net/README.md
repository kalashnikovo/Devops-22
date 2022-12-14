# Домашнее задание к занятию "3.7. Компьютерные сети.Лекция 2"

## Задание

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?

    #### Решение
    Список интерфейсов на моем компьютере:
    ```bash
    ~ ❯ ifconfig
    lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
        options=1203<RXCSUM,TXCSUM,TXSTATUS,SW_TIMESTAMP>
        inet 127.0.0.1 netmask 0xff000000
        inet6 ::1 prefixlen 128
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
        nd6 options=201<PERFORMNUD,DAD>
    gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280
    stf0: flags=0<> mtu 1280
    anpi1: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:ee
        inet6 fe80::3414:23ff:fe0a:98ee%anpi1 prefixlen 64 scopeid 0x4
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    anpi0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:ed
        inet6 fe80::3414:23ff:fe0a:98ed%anpi0 prefixlen 64 scopeid 0x5
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    anpi2: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:ef
        inet6 fe80::3414:23ff:fe0a:98ef%anpi2 prefixlen 64 scopeid 0x6
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    en4: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:cd
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    en6: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:ce
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    en7: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:14:23:0a:98:cf
        nd6 options=201<PERFORMNUD,DAD>
        media: none
        status: inactive
    en1: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
        options=460<TSO4,TSO6,CHANNEL_IO>
        ether 36:bc:d7:41:91:c0
        media: autoselect <full-duplex>
        status: inactive
    en2: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
        options=460<TSO4,TSO6,CHANNEL_IO>
        ether 36:bc:d7:41:91:c4
        media: autoselect <full-duplex>
        status: inactive
    en3: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
        options=460<TSO4,TSO6,CHANNEL_IO>
        ether 36:bc:d7:41:91:c8
        media: autoselect <full-duplex>
        status: inactive
    ap1: flags=8802<BROADCAST,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether fa:4d:89:83:c7:64
        media: autoselect
    en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether f8:4d:89:83:c7:64
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect (<unknown type>)
        status: inactive
    awdl0: flags=8802<BROADCAST,SIMPLEX,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
        ether 22:18:dd:e6:31:e9
        inet6 fe80::2018:ddff:fee6:31e9%awdl0 prefixlen 64 scopeid 0x10
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect (<unknown type>)
        status: inactive
    bridge0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=63<RXCSUM,TXCSUM,TSO4,TSO6>
        ether 36:bc:d7:41:91:c0
        Configuration:
            id 0:0:0:0:0:0 priority 0 hellotime 0 fwddelay 0
            maxage 0 holdcnt 0 proto stp maxaddr 100 timeout 1200
            root id 0:0:0:0:0:0 priority 0 ifcost 0 port 0
            ipfilter disabled flags 0x0
        member: en1 flags=3<LEARNING,DISCOVER>
                ifmaxaddr 0 port 10 priority 0 path cost 0
        member: en2 flags=3<LEARNING,DISCOVER>
                ifmaxaddr 0 port 11 priority 0 path cost 0
        member: en3 flags=3<LEARNING,DISCOVER>
                ifmaxaddr 0 port 12 priority 0 path cost 0
        nd6 options=201<PERFORMNUD,DAD>
        media: <unknown type>
        status: inactive
    llw0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 22:18:dd:e6:31:e9
        inet6 fe80::2018:ddff:fee6:31e9%llw0 prefixlen 64 scopeid 0x12
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect
        status: inactive
    utun0: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 2000
        inet6 fe80::f96e:5181:ba75:b286%utun0 prefixlen 64 scopeid 0x13
        nd6 options=201<PERFORMNUD,DAD>
    utun1: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1000
        inet6 fe80::ce81:b1c:bd2c:69e%utun1 prefixlen 64 scopeid 0x14
        nd6 options=201<PERFORMNUD,DAD>
    utun2: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1380
        inet6 fe80::d9c7:4600:4ac5:e15e%utun2 prefixlen 64 scopeid 0x15
        nd6 options=201<PERFORMNUD,DAD>
    utun3: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    utun4: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    utun5: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    utun6: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    en5: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=6467<RXCSUM,TXCSUM,VLAN_MTU,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
        ether 00:e0:4c:ca:0a:3a
        inet6 fe80::1884:e081:3cb2:9120%en5 prefixlen 64 secured scopeid 0xf
        inet 192.168.88.11 netmask 0xffffff00 broadcast 192.168.88.255
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect (1000baseT <full-duplex>)
        status: active
    utun7: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        options=6463<RXCSUM,TXCSUM,TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    ```
    
    В Linux можно использовать команды `ifconfig` или `ip address` - в зависимости от установленных пакетов.

    В Windows можно использовать команду `ipconfig /all`.

2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
    #### Решение
    Для поиска соседа по сетевому интерфейсу используется протокол LLDP. В дистрибутивах основанных на Debian для использования данного протокола нужно установить пакет `lldpd` и убедиться, что одноименный сервис запущен. Для проверки соседа используется команда `lldpctl`.  

3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
    #### Решение
    Для разделения L2 коммутатора на несколько виртуальных сетей используется технология VLAN. В Debian можно установить пакет `vlan`.
    
    Пример конфига:  
    /etc/network/interfaces
    ```
    auto vlan1400
    iface vlan1400 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        vlan_raw_device eth0
    ```

    Для просмотра созданных VLAN интерфейсов в Linux можно воспользоваться командой:
    ```bash
    # ip --br link show
    ```

4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
    #### Решение
    Драйвер bonding ядра Linux обеспечивает метод агрегации нескольких сетевых каналов в единый логический интерфейс. Поддерживается статическая агрегация и агрегация на основе LACP (802.3ad).  

    Поддерживаемые режимы
    | Режим Bonding     |  Конфигурация коммутатора |
    | ----------------- | --------------------------- |
    | 0 - balance-rr    | Требуется статическая агрегация Etherchannel (не LACP) |
    | 1 - active-backup | Независимый порт |
    | 2 - balance-xor   | Требуется статическая агрегация Etherchannel (не LACP) |
    | 3 - broadcast	    | Требуется статическая агрегация Etherchannel (не LACP) |
    | 4 - 802.3ad	    | Требуется динамическая агрегация Etherchannel - LACP |
    | 5 - balance-tlb	| Независимый порт |
    | 6 - balance-alb	| Независимый порт |

    Для балансировки используются режимы: 

    * balance-rr
      Балансировка нагрузки и резервирование каналов на уровне отдельных пакетов. Пакеты передаются по очереди в каждый из работоспособных каналов (по кругу, round robin).

    * balance-xor
      Балансировка нагрузки и резервирование каналов на уровне парных взаимодействий. Пакеты, принадлежащие каждой паре MAC-адресов (источник, назначение) передаются в определённый канал, таким образом, все парные взаимодействия распределяются равномерно между действующими каналами.

    * balance-tlb
      Адаптивная балансировка нагрузки на передачу (transmit load balancing). Не требует специальной поддержки агрегации на промежуточных коммутаторах. Исходящие пакеты распределяются между активными каналами таким образом, чтобы обеспечить им равномерную загрузку относительно номинальной скорости каждого канала. Все входящие пакеты принимаются одним активным каналом; если он выходит из строя, то другой канал становится активным и принимает его MAC-адрес.

    * balance-alb
      Адаптивная балансировка нагрузки (adaptive load balancing). Включает в себя balance-tlb плюс балансировку нагрузки на приёме (rlb) для пакетов IPv4. Не требует специальной поддержки агрегации на промежуточных коммутаторах; балансировка на приёме производится с помощью манипулирования пакетами ARP. В исходящие ответы ARP вместо системного МАС-адреса подставляется адрес конкретного порта в нём, с тем, чтобы каждый из удалённых хостов присылал пакеты на определённый порт устройства NSG.

    **Пример настройки bonding с LACP для CentOS 7**  

    Агрегирующий интерфейс bond0:  

    /etc/sysconfig/network-scripts/ifcfg-bond0
    ```
    DEVICE=bond0
    NAME=bond0
    TYPE=Bond
    BONDING_MASTER=yes
    IPV6INIT=no
    MTU=9000
    ONBOOT=yes
    USERCTL=no
    NM_CONTROLLED=no
    BOOTPROTO=none
    BONDING_OPTS="mode=802.3ad xmit_hash_policy=layer2+3 lacp_rate=1 miimon=100"
    ```
    Файл с конфигурацией slave-интерфейса:

    /etc/sysconfig/network-scripts/ifcfg-enp3s0
    ```
    DEVICE=enp3s0
    NAME=bond0-slave0
    TYPE=Ethernet
    MTU=9000
    BOOTPROTO=none
    ONBOOT=yes
    MASTER=bond0
    SLAVE=yes
    USERCTL=no 
    NM_CONTROLLED=no 
    ```
    Последующие slave-интерфейсы настраиваются аналогично.

    Альтернативой драйверу `bonding` в Linux является [Linux Team Driver](https://github.com/jpirko/libteam/wiki).

5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
    #### Решение
    * В сети с маской /29 2^3=8 адресов и 6 хостов.  
    * Из сети с маской /24 можно получить ровно 32 сети (256/8).

    * Первые три сети /29 из диапазона 10.10.10.0/24:
    ```
    1. Requested size: 6 hosts
    Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
    Network:   10.10.10.0/29        00001010.00001010.00001010.00000 000
    HostMin:   10.10.10.1           00001010.00001010.00001010.00000 001
    HostMax:   10.10.10.6           00001010.00001010.00001010.00000 110
    Broadcast: 10.10.10.7           00001010.00001010.00001010.00000 111
    Hosts/Net: 6                     Class A, Private Internet

    2. Requested size: 6 hosts
    Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
    Network:   10.10.10.8/29        00001010.00001010.00001010.00001 000
    HostMin:   10.10.10.9           00001010.00001010.00001010.00001 001
    HostMax:   10.10.10.14          00001010.00001010.00001010.00001 110
    Broadcast: 10.10.10.15          00001010.00001010.00001010.00001 111
    Hosts/Net: 6                     Class A, Private Internet

    3. Requested size: 6 hosts
    Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
    Network:   10.10.10.16/29       00001010.00001010.00001010.00010 000
    HostMin:   10.10.10.17          00001010.00001010.00001010.00010 001
    HostMax:   10.10.10.22          00001010.00001010.00001010.00010 110
    Broadcast: 10.10.10.23          00001010.00001010.00001010.00010 111
    Hosts/Net: 6                     Class A, Private Internet
    ```

6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
    #### Решение
    Думаю, можно взять подсеть 198.51.100.0/24, которая предназначена для документации и глобально не маршрутизируется. Для 40-50 хостов достаточно следующей подсети 198.51.100.0/26 в которой будет максимально 62 хоста, если делать с запасом то можно взять подстеть 198.51.100.0/27 в которой будет 126 адресов.

7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
    #### Решение
    * ARP таблицу в Linux можно проверить командой `arp -n`, а в Windows - коммандой `arp -a`.
    * Очистить ARP кэш в Linux можно командой `sudo ip neigh flush all`, а в Windows - коммандой `arp -d`.
    * Для удаления одного IP в Linux и Windows можно использовать команду `arp -d <ip-address>`.

