## Сборка рейда
В репозитории находится  Vagrantfile с добавленными секциями дисков.  
В секцию shell добавлен скрипт по созданию 10-го рейда для сборки массива при инициализации виртуальной машины. Также добавлен файл конфигурации mdadm.conf.
***
## Потеря диска в рейде и его восстановление
	mdadm /dev/md0 --fail /dev/sdc
Cэмулировал выход из строя одного из дисков рейд массива. Проверил через /proc/mdstat, что диск в рейде в статусе remove.
	mdadm /dev/md0 --remove /dev/sdc 
Удалил запись о диске из рейд массива 
	mdadm /dev/md0 --add /dev/sdc
Добавил "новый" диск в рейд массив вместо удаленного.  Проверил через /proc/mdstat динамику перестроения рейд массива 
***
## Создание раздела GPT и пяти партиций
	parted -s /dev/md0 mklabel gpt
    parted  /dev/md0 primary ext4 0% 20%
	parted  /dev/md0 primary ext4 20% 40%
	parted  /dev/md0 primary ext4 40% 60%
	parted  /dev/md0 primary ext4 60% 80%
	parted  /dev/md0 primary ext4 80% 100%
промаркировал раздел как gpt (guid partition table) и нарезал пять primary секций  равного размера
	mkfs.ext4 /dev/md0/md0p1
	mkfs.ext4 /dev/md0/md0p2
	mkfs.ext4 /dev/md0/md0p3
	mkfs.ext4 /dev/md0/md0p4
	mkfs.ext4 /dev/md0/md0p5
в каждой секции создал файловую систему ext4
    mkdir /raid/part1
	mkdir /raid/part2
	mkdir /raid/part3
	mkdir /raid/part4
	mkdir /raid/part5
	mount /dev/md0/md0p1 /raid/part1
	mount /dev/md0/md0p2 /raid/part2
	mount /dev/md0/md0p3 /raid/part3
	mount /dev/md0/md0p4 /raid/part4
	mount /dev/md0/md0p5 /raid/part5
создал пять директорий точек монтирования и смонтировал секции раздела.
	[root@otuslinux vagrant]# df -h
	Filesystem      Size  Used Avail Use% Mounted on
	/dev/sda1        40G  4.4G   36G  11% /
	devtmpfs        488M     0  488M   0% /dev
	tmpfs           496M     0  496M   0% /dev/shm
	tmpfs           496M  6.7M  489M   2% /run
	tmpfs           496M     0  496M   0% /sys/fs/cgroup
	tmpfs           100M     0  100M   0% /run/user/1000
	/dev/md0p1      139M  1.6M  127M   2% /raid/part1
	/dev/md0p2      140M  1.6M  128M   2% /raid/part2
	/dev/md0p3      142M  1.6M  130M   2% /raid/part3
	/dev/md0p4      140M  1.6M  128M   2% /raid/part4
	/dev/md0p5      139M  1.6M  127M   2% /raid/part5

	
 