# Zadání finálního testu verze B (x.x.2023)

1. (3body) Ve virtualizačním prostředí Virtualbox vytvořte PC s jedním pevným diskem a dvěma síťovými kartami. Jednu kartu připojte do sítě NAT a druhou do sítě "Host-only network". Na toto virtualizované PC nainstalujte aktuální OS Linux/Debian v minimalistické verzi, pouze s podporou protokolu SSH (bez XWindows, apod.).
- [Tutoriál](http://seidl.cs.vsb.cz/wiki2/index.php/SOS)
    - instalace a změna defaultního IDE, instalace ```man```:
        ```console
        apt install mc
        export EDITOR=mcedit
        apt install man
        ```
2. (3body) Nakonfigurujte systém tak, aby síťová karta na rozhraní NAT dostávala IP adresu prostřednictvím protokolu DHCP (z Virtualboxu) a druhá karta bude mít IP adresu nastavenou pevně. Pro konfiguraci obou rozhraní využijte standardní metody používané v distribuci Debian:
        - nastavení staické adresy v /etc/network/interfaces:        
     
        ```
        allow-hotplug enp0s8
        iface enp0s8 inet static
        address 192.168.41.12/24
        ```
3. (10bodů) Do virtualizovaného PC přidejte další čtyři pevné disky o kapacitě alespoň 200MB. Z těchto disků vytvořte v systému RAID, který bude odolný proti výpadku jednoho disku. Na tomto RAID poli vytvořte jeden oddíl a ten naformátujte souborovým systémem ext4. Nakonfigurujte systém tak, aby se oddíl vytvořený na RAID poli připojoval jako složka /home po startu systému. Využijte standardní systémovou konfiguraci, pro identifikaci raidu použijte UUID.

    - instalace a vytvoření, vytvoření oddílu:
        ```console        
        apt install mdadm
        mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd --spare-devices=1 /dev/sde
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


4. (12bodů) Vytvořte spustitelný skript v jazyce bash, který do systému přidá 100 uživatelských účtů. Loginy budou user00 až user99, interpret pro všechny uživatele bude /bin/bash a uživatelům se vytvoří domovský adresář ve složce /home. Jako parametr skriptu bude možné zadat defaultní heslo pro vytvářené uživatele. Skript otestuje zdali je heslo delší než 5 znaků. V případě, že nebude zadaný parametr, bude heslo prázdné. Všem uživatelů definujte diskové kvóty:
    
     - kvóty:
    ```console
    apt install quota
    mount -o remount,usrquota,grpquota /home
    service quota start
    quotacheck /dev/md127p1
    quotaon /dev/md127p1
    ```
    
    - uživatel prototyp:
    ```console
    useradd -m -s /usr/sbin/nologin -c "uzivatel prototyp" prototyp;
    edquota prototyp;
    ```
    
    - instalace openssl:
     ```console
    apt install openssl
    ```
    
    - skript pro vytvoření uživatelů:
    ```console
    #!/bin/bash
    
    password=$1
    for i in $(seq 0 99)
    do
        empty_space="";
        if (( $i < 10))
        then
            empty_space="0";
        fi
        
        name="user${empty_space}${i}"; 
        length=${#password};
        
        if (( $length > 5))
        then            
            useradd -m -s /bin/bash -c "uzivatel ${name}" -p $(echo $password | openssl passwd -1 -stdin) $name;
            edquota -p prototyp ${name};
        elif (( $length == 0 ));
        then
            useradd -m -s /bin/bash -c "uzivatel ${name}" -p "" $name;
            edquota -p prototyp ${name};
        else
            echo "too short password";
        fi
        
    done;
    ```
    - ještě je potřeba ve fstabu nastavit do raidu za defaults parametry
        ```console
        ,usrquota,grpquota
        ```
        
5. (5bodů) V adresáři /home vytvořte složku /studenti. V systému vytvořte skupinu studenti a přidejte do ní 10 uživatelů. Složka /home/studenti bude umožňovat přístup jen uživatelům patřícím do skupiny studenti (rwx). Pokud některý z uživatelů vytvoří v této složce soubor, tento bude automaticky patřit skupině studenti a nikoli skupině uživatele, který ho vytvořil:
     - vytovření a přiřazení:  
        ```console
        mkdir /home/studenti
        addgroup studenti
        for usern in $(seq 1 9)
        do
            addgroup "user0${usern}" studenti;
        done
        addgroup user10 studenti
        chgrp studenti /home/studenti
        chmod 070 /home/studenti
        chmod g+s /home/studenti
        ```

6. (3body) Nainstalujte webový server Apache2 s podporou PHP a SSL 

    ```console
    apt install apache2
    apt install libapache2-mod-php
    /usr/sbin/a2enmod ssl
    /usr/sbin/a2ensite default-ssl
    systemctl reload apache2
    ```  
