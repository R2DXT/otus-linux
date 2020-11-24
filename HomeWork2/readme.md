В изначальный vagrantfile внесено изменение - добавлено 2 HDD. 
Создан рейд 0. 
mdadm --zero-superblock /dev/sdf /dev/sdg
mdadm --create /dev/md0 --level 0 -n 2 /dev/sdf /dev/sdg
mkdir /raid  /etc/mdadm
Размеечен GPT, разбит 5 разделов и отформатирован в ext4. 
parted -s /dev/md0 mktable gpt && parted -s /dev/md0 mkpart primary ext4 0 100M && parted -s /dev/md0 mkpart primary ext4 100 200M && parted -s /dev/md0 mkpart primary ext4 200 300M && parted -s /dev/md0 mkpart primary ext4 300 400M && parted -s /dev/md0 mkpart primary ext4 400 500M && for n in 1 2 3 4 5; do mkfs.ext4 /dev/md0p${n}; done
Создан конфиг и настроена автосборка при загрузке.
echo "DEVICE partitions" > /etc/mdadm/mdadm.conf && mdadm --detail --scan --verbose | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf

Vagrantfile - рейд собирается самостоятельно при разворачивании.  