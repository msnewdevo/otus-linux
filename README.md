Добавляем структуру для добавления доп. диска в Vagrantfile:

    :sata6 =>   {
                            :dfile => './sata6.vdi',
                            :size => 250, # Megabytes
                            :port => 6
                    }

Добавляем в Vagrantfile строку для выполнения скрипта:
    box.vm.provision "shell", path: "script.sh" 

Тело script.sh :

    Зануляем суперблоки:
        mdadm --zero-superblock --force /dev/sd{b,c,d,e,f,g}

    Создаем рейд:
        mdadm --create --verbose /dev/md0 -l 6 -n 6 /dev/sd{b,c,d,e,f,g}

    Создаем каталог для mdadm.conf:
        mkdir -p /etc/mdadm
    
    Создаем файл mdadm.conf:
        echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
        mdadm --detail --scan --verbose | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf