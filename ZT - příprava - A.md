# Zadání finálního testu verze A (x.x.2023)

1. (3body) Ve virtualizačním prostředí Virtualbox vytvořte PC s jedním pevným diskem a dvěma síťovými kartami. Jednu kartu připojte do sítě NAT a druhou do sítě "Host-only network". Na toto virtualizované PC nainstalujte aktuální OS Linux/Debian v minimalistické verzi, pouze s podporou protokolu SSH (bez XWindows, apod.):
    - [Tutoriál](http://seidl.cs.vsb.cz/wiki2/index.php/SOS)
    - instalace a změna defaultního IDE, instalace ```man```:
        ```console
        apt install mc
        export EDITOR=mcedit
        apt install man
        ```
        
3. (10bodů) Do virtualizovaného PC přidejte další tři pevné disky o kapacitě alespoň 200MB. Z těchto disků vytvořte v systému RAID který bude odolný proti výpadku dvou disků. Na RAID vytvořte jeden oddíl a naformátujte ho souborovým systémem ext4. Tento souborový systém připojte jako složku /home. Nakonfigurujte systém tak, aby připojení diskového pole proběhlo vždy po startu systému, pro identifikaci raidu použijte UUID:
    - instalace a vytvoření, vytvoření oddílu:
        ```console        
        apt install mdadm
        mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc --spare-devices=1 /dev/sdd
        cfdisk /dev/md0p1
        ```
   - naformátování a namountování:     
        ```console        
        mkfs.ext4 /dev/md0p1
        mount /dev/md0p1 /mnt
        cp -r /home/. /mnt        
        mount /dev/md0p1 /home
        umount /mnt        
        ```
        
   - namountování RAIDu po startu => konfigurace fstabu:
        ```console
        blkid
        mcedit /etc/fstab
        ```
        ```        
        UUID=<file system>  /home   ext4    defaults    0   0 
        ```
        
        


       
        
6. (3body) Nainstalujte webový server Apache2 s podporou PHP, https a userdir:
    - instalace i konfigurace:
        ```console
        apt install apache2
        apt install libapache2-mod-php
        /usr/sbin/a2enmod ssl
        /usr/sbin/a2ensite default-ssl
        /usr/sbin/a2enmod userdir
        systemctl reload apache2
        ```
