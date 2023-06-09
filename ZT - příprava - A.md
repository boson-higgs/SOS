# Zadání finálního testu verze A (x.x.2023)

1. (3body) Ve virtualizačním prostředí Virtualbox vytvořte PC s jedním pevným diskem a dvěma síťovými kartami. Jednu kartu připojte do sítě NAT a druhou do sítě "Host-only network". Na toto virtualizované PC nainstalujte aktuální OS Linux/Debian v minimalistické verzi, pouze s podporou protokolu SSH (bez XWindows, apod.):
    - [Tutoriál](http://seidl.cs.vsb.cz/wiki2/index.php/SOS)
    - instalace a změna defaultního IDE, instalace ```man```:
        ```console
        apt install mc
        export EDITOR=mcedit
        apt install man
        ```
        
        
2. (3body) Nakonfigurujte systém tak, aby síťová karta na rozhraní NAT dostávala IP adresu prostřednictvím protokolu DHCP (z Virtualboxu) a druhá karta bude mít IP adresu nastavenou staticky. Pro konfiguraci obou rozhraní využijte standardní metody používané v distribuci Debian:
    - nastavení staické adresy v /etc/network/interfaces:        
     
    ```
    allow-hotplug enp0s8
    iface enp0s8 inet static
    address 192.168.41.11/24
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
        
4. (12bodů) Vytvořte spustitelný skript v jazyce bash, který do systému přidá definovaný počet uživatelských účtů ve tvaru uz001 až uz###, kde ### bude číslo zadané jako parametr skriptu. Zařiďte, aby číslo nemohlo přesáhnout 3 cifry. Interpret pro všechny uživatele bude /bin/bash a uživatelům se vytvoří domovský adresář ve složce /home. Uživatelé budou mít prázdné heslo a budou nuceni si ho po prvním přihlášení změnit. Každému uživateli se při vytvoření účtu vytvoří v domovské složce soubor READ_ME.txt . Všem uživatelů definujte diskové kvóty:
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
    
    echo "Hello, im readme file" > /etc/skel/READ_ME.txt
    
    max=$1
    for i in $(seq 1 $max)
    do
        space_number="";
        if (( $i < 10 ))
        then
            space_number="00";
        elif (( $i < 100 ));
        then
            space_number="0";
        fi
        name="uz${space_number}${i}";
        useradd -m -s /bin/bash -c "uzivatel ${name}" -p "" $name;
        passwd -e $name;
        edquota -p prototyp ${name};
        echo $name;
    done;
    ```
    - ještě je potřeba ve fstabu nastavit do raidu za defaults parametry
        ```console
        ,usrquota,grpquota
        ```
         

5. (5bodů) V kořenovém adresáři vytvořte složku /projekty. V systému vytvořte skupinu projekty a přidejte do ní deset uživatelů. Složka /projekty bude umožňovat přístup (rwx) jen uživatelům patřícím do skupiny projekty. Pokud některý z uživatelů vytvoří v této složce soubor, tento bude automaticky patřit skupině projekty a nikoli domovské skupině uživatele, který ho vytvořil:
    - vytvoření složky projekty:
    ```console
    mkdir /projekty
    addgroup projekty
    for usern in $(seq 1 9)
    do
        addgroup "uz00${usern}" projekty;
    done
    addgroup uz010 projekty
    chgrp projekty /projekty
    chmod 070 /projekty
    chmod g+s /projekty
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
        
9. (5bodů) Nainstalujte server NFS a vyexportujte složku /var/www/html pro všechny počítače v síti „Host-only network“. Správnou funkci demonstrujte:
    - instalace a nastavení - server:
      ```console
      apt install nfs-kernel-server
      mcedit /etc/exports
      ```
    - v /etc/export přidat:
      ```console
      /var/www/html      192.168.41.0/24(rw,sync,no_subtree_check)
      ```
    - restart služby NFS
      ```console
      service nfs-kernel-server restart
      ```
    - instalace a nastavení - klient:
       ```console
      apt install nfs-common
      mount 192.168.41.11:/var/www/html /opt
      ``` 
       
