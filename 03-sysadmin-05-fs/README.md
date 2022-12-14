# Домашнее задание к занятию "3.5. Файловые системы"

1. Узнайте о [sparse](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных) файлах.

    #### Решение:
    Разрежённый файл (англ. sparse file) — файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях (список дыр). Дыра (англ. hole) — последовательность нулевых байт внутри файла, не записанная на диск. Информация о дырах (смещение от начала файла в байтах и количество байт) хранится в метаданных ФС.
    Пример команды создания разряженного файла размером 5 Мб и команды детектирования таких файлов:
    ```bash
    wowpetr@lpau:~$ dd of=/tmp/sparse-file bs=5M seek=1 count=0
    0+0 records in
    0+0 records out
    0 bytes copied, 8.3537e-05 s, 0.0 kB/s
    wowpetr@lpau:~$ du /tmp/sparse-file
    0	/tmp/sparse-file
    wowpetr@lpau:~$ ls -lsh /tmp/sparse-file
    0 -rw-rw-r-- 1 wowpetr wowpetr 5.0M Sep 19 22:16 /tmp/sparse-file
    ```
    
2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

    #### Решение:
    При жесткой ссылке добавляется лишь дополнительное имя в файловой системе, которое не может иметь никаких прав. Аттрибуты, такие как права и владелец, определяются исходным файлом, на которые указывают жесткие ссылки. При создании дополнительной жесткой ссылки (кроме его исходного имени), никакие файлы не создаются в отличие от символьной ссылки, когда создаются файлы (аналог ярлыков Windows) указывающие на исходный файл, а потому и имеющие такие атрибуты как владелец и права доступа.   

3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

    #### Решение:
    ```bash
    Vagrant.configure("2") do |config|
      config.vm.box = "bento/ubuntu-20.04"
      config.vm.provider :virtualbox do |vb|
        lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
        lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
        vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
        vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
      end
    end
    ```
    Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.

    #### Решение:
    Указанная конфигурация Vagrant создала виртуальную машину с дополнительными дисками `/dev/sdb` и `/dev/sdc` по 2.5 Гб:
    ```bash
    vagrant@vagrant:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    loop0                       7:0    0 43.6M  1 loop /snap/snapd/14978
    loop1                       7:1    0 67.2M  1 loop /snap/lxd/21835
    loop2                       7:2    0 61.9M  1 loop /snap/core20/1328
    sda                         8:0    0   64G  0 disk
    ├─sda1                      8:1    0    1M  0 part
    ├─sda2                      8:2    0  1.5G  0 part /boot
    └─sda3                      8:3    0 62.5G  0 part
    └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm  /
    sdb                         8:16   0  2.5G  0 disk
    sdc                         8:32   0  2.5G  0 disk
    ```

4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
   
    #### Решение:
   ```bash
    vagrant@vagrant:~$ sudo fdisk -l /dev/sdb
    Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
    Disk model: VBOX HARDDISK
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0xba1a48f9

    Device     Boot   Start     End Sectors  Size Id Type
    /dev/sdb1          2048 4196351 4194304    2G 83 Linux
    /dev/sdb2       4196352 5242879 1046528  511M 83 Linux
    ```

5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc
    Checking that no-one is using this disk right now ... OK

    Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
    Disk model: VBOX HARDDISK
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Created a new DOS disklabel with disk identifier 0xba1a48f9.
    /dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
    /dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
    /dev/sdc3: Done.
    
    New situation:
    Disklabel type: dos
    Disk identifier: 0xba1a48f9
    
    Device     Boot   Start     End Sectors  Size Id Type
    /dev/sdc1          2048 4196351 4194304    2G 83 Linux
    /dev/sdc2       4196352 5242879 1046528  511M 83 Linux
    
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
    ```
6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md1 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
    mdadm: Note: this array has metadata at the start and
        may not be suitable as a boot device.  If you plan to
        store '/boot' on this device please ensure that
        your boot-loader understands md/v1.x metadata, or use
        --metadata=0.90
    mdadm: size set to 2094080K
    Continue creating array? y
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md1 started.
    ```
7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
    mdadm: chunk size defaults to 512K
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md0 started.
    vagrant@vagrant:~$ cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]
    md0 : active raid0 sdc2[1] sdb2[0]
          1042432 blocks super 1.2 512k chunks
    
    md1 : active raid1 sdc1[1] sdb1[0]
          2094080 blocks super 1.2 [2/2] [UU]
    
    unused devices: <none>
    ```
8. Создайте 2 независимых PV на получившихся md-устройствах.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo pvcreate /dev/md0 /dev/md1
      Physical volume "/dev/md0" successfully created.
      Physical volume "/dev/md1" successfully created.
    vagrant@vagrant:~$ sudo pvs
      PV         VG        Fmt  Attr PSize    PFree
      /dev/md0             lvm2 ---  1018.00m 1018.00m
      /dev/md1             lvm2 ---    <2.00g   <2.00g
      /dev/sda3  ubuntu-vg lvm2 a--   <62.50g   31.25g
    ```
9. Создайте общую volume-group на этих двух PV.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo vgcreate NETOLOGY /dev/md0 /dev/md1
      Volume group "NETOLOGY" successfully created
    vagrant@vagrant:~$ sudo vgdisplay NETOLOGY -v
      --- Volume group ---
      VG Name               NETOLOGY
      System ID
      Format                lvm2
      Metadata Areas        2
      Metadata Sequence No  1
      VG Access             read/write
      VG Status             resizable
      MAX LV                0
      Cur LV                0
      Open LV               0
      Max PV                0
      Cur PV                2
      Act PV                2
      VG Size               <2.99 GiB
      PE Size               4.00 MiB
      Total PE              765
      Alloc PE / Size       0 / 0
      Free  PE / Size       765 / <2.99 GiB
      VG UUID               Ts72Gb-jdpt-30z6-TerS-gJpJ-eTem-0nb4cA
    
      --- Physical volumes ---
      PV Name               /dev/md0
      PV UUID               WIyRy0-bOao-ImkO-jn6o-cbZF-GRHk-Q8Telg
      PV Status             allocatable
      Total PE / Free PE    254 / 254
    
      PV Name               /dev/md1
      PV UUID               4L3M7b-roe6-koK4-XSmD-lHdc-leCG-hYFsLP
      PV Status             allocatable
      Total PE / Free PE    511 / 511
    ```
10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo lvcreate -L 100M -n lv1 NETOLOGY /dev/md0
      Logical volume "lv1" created.
    vagrant@vagrant:~$ sudo lvdisplay /dev/NETOLOGY/lv1
      --- Logical volume ---
      LV Path                /dev/NETOLOGY/lv1
      LV Name                lv1
      VG Name                NETOLOGY
      LV UUID                RJKkqh-k1C1-Kw15-uvWi-z3hE-GUMw-oGt55t
      LV Write Access        read/write
      LV Creation host, time vagrant, 2022-09-19 23:05:14 +0000
      LV Status              available
      # open                 0
      LV Size                100.00 MiB
      Current LE             25
      Segments               1
      Allocation             inherit
      Read ahead sectors     auto
      - currently set to     4096
      Block device           253:1
    vagrant@vagrant:~$ sudo pvdisplay -m /dev/md0
      --- Physical volume ---
      PV Name               /dev/md0
      VG Name               NETOLOGY
      PV Size               1018.00 MiB / not usable 2.00 MiB
      Allocatable           yes
      PE Size               4.00 MiB
      Total PE              254
      Free PE               229
      Allocated PE          25
      PV UUID               WIyRy0-bOao-ImkO-jn6o-cbZF-GRHk-Q8Telg
    
      --- Physical Segments ---
      Physical extent 0 to 24:
        Logical volume	/dev/NETOLOGY/lv1
        Logical extents	0 to 24
      Physical extent 25 to 253:
        FREE
    ```

11. Создайте `mkfs.ext4` ФС на получившемся LV.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo mkfs.ext4 /dev/NETOLOGY/lv1
    mke2fs 1.45.5 (07-Jan-2020)
    Creating filesystem with 25600 4k blocks and 25600 inodes
    
    Allocating group tables: done
    Writing inode tables: done
    Creating journal (1024 blocks): done
    Writing superblocks and filesystem accounting information: done
    ```

12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo mkdir /mnt/netology-lv1
    vagrant@vagrant:~$ mount | grep lv1
    /dev/mapper/NETOLOGY-lv1 on /mnt/netology-lv1 type ext4 (rw,relatime,stripe=256)
    ```

13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

    #### Решение:
    ```bash
    vagrant@vagrant:/mnt/netology-lv1$ sudo chown vagrant:vagrant /mnt/netology-lv1/
    vagrant@vagrant:~$ cd /mnt/netology-lv1/
    vagrant@vagrant:/mnt/netology-lv1$ wget https://mirror.yandex.ru/ubuntu/ls-lR.gz
    --2022-09-19 23:30:30--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
    Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
    Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 22353129 (21M) [application/octet-stream]
    Saving to: ‘ls-lR.gz’
    
    ls-lR.gz                       100%[===================================================>]  21.32M  4.50MB/s    in 5.2s
    
    2022-09-19 23:30:38 (4.09 MB/s) - ‘ls-lR.gz’ saved [22353129/22353129]
    
    vagrant@vagrant:/mnt/netology-lv1$ ls -lh
    total 22M
    drwx------ 2 root    root    16K Sep 19 23:22 lost+found
    -rw-rw-r-- 1 vagrant vagrant 22M Sep 19 23:07 ls-lR.gz
    ```

14. Прикрепите вывод `lsblk`.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    sda                         8:0    0   64G  0 disk
    ├─sda1                      8:1    0    1M  0 part
    ├─sda2                      8:2    0  1.5G  0 part  /boot
    └─sda3                      8:3    0 62.5G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
    sdb                         8:16   0  2.5G  0 disk
    ├─sdb1                      8:17   0    2G  0 part
    │ └─md1                     9:1    0    2G  0 raid1
    └─sdb2                      8:18   0  511M  0 part
      └─md0                     9:0    0 1018M  0 raid0
        └─NETOLOGY-lv1        253:1    0  100M  0 lvm   /mnt/netology-lv1
    sdc                         8:32   0  2.5G  0 disk
    ├─sdc1                      8:33   0    2G  0 part
    │ └─md1                     9:1    0    2G  0 raid1
    └─sdc2                      8:34   0  511M  0 part
      └─md0                     9:0    0 1018M  0 raid0
        └─NETOLOGY-lv1        253:1    0  100M  0 lvm   /mnt/netology-lv1
    ```

15. Протестируйте целостность файла:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

    #### Решение:
    ```bash
    vagrant@vagrant:~$ gzip -t /mnt/netology-lv1/ls-lR.gz
    vagrant@vagrant:~$ echo $?
    0
    ```

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo pvmove /dev/md0 /dev/md1
      /dev/md0: Moved: 16.00%
      /dev/md0: Moved: 100.00%
    vagrant@vagrant:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    sda                         8:0    0   64G  0 disk
    ├─sda1                      8:1    0    1M  0 part
    ├─sda2                      8:2    0  1.5G  0 part  /boot
    └─sda3                      8:3    0 62.5G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
    sdb                         8:16   0  2.5G  0 disk
    ├─sdb1                      8:17   0    2G  0 part
    │ └─md1                     9:1    0    2G  0 raid1
    │   └─NETOLOGY-lv1        253:1    0  100M  0 lvm   /mnt/netology-lv1
    └─sdb2                      8:18   0  511M  0 part
      └─md0                     9:0    0 1018M  0 raid0
    sdc                         8:32   0  2.5G  0 disk
    ├─sdc1                      8:33   0    2G  0 part
    │ └─md1                     9:1    0    2G  0 raid1
    │   └─NETOLOGY-lv1        253:1    0  100M  0 lvm   /mnt/netology-lv1
    └─sdc2                      8:34   0  511M  0 part
      └─md0                     9:0    0 1018M  0 raid0
    ```

17. Сделайте `--fail` на устройство в вашем RAID1 md.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo mdadm --fail /dev/md1 /dev/sdc1
    mdadm: set /dev/sdc1 faulty in /dev/md1
    vagrant@vagrant:~$ cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]
    md0 : active raid0 sdc2[1] sdb2[0]
          1042432 blocks super 1.2 512k chunks
    
    md1 : active raid1 sdc1[1](F) sdb1[0]
          2094080 blocks super 1.2 [2/1] [U_]
    
    unused devices: <none>
    ```

18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.

    #### Решение:
    ```bash
    vagrant@vagrant:~$ sudo dmesg | tail
    ...
    [13260.859938] md/raid1:md1: Disk failure on sdc1, disabling device.
                   md/raid1:md1: Operation continuing on 1 devices.
    ```

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```
    
    #### Решение:
    ```bash
    vagrant@vagrant:~$ gzip -t /mnt/netology-lv1/ls-lR.gz
    vagrant@vagrant:~$ echo $?
    0
    ```

20. Погасите тестовый хост, `vagrant destroy`.

    #### Решение:
    Может пригодится еще...