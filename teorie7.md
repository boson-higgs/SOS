# Konfigurace služeb

## LAMP (Linux, Apache, MariaDB (kdysi MySQL), PHP/Perl/Python) server
    - umožňuje z jednoho serveru (jedné IP adresy) zprovoznit mnoho webových stránek
    ### Instalace

        ```console
        root@<your_computer_name>:~$ apt install apache2
        ```
        - tento příkaz nainstaluje a spustí apache server (webový server)

        ```console
        root@<your_computer_name>:~$ apt install libapache2-mod-php
        ```
        - tento příkaz doinstaluje a aktivuje k apache serveru PHP modul (podporu jazyka PHP)
        - modulů k doinstalaci či aktivaci (řada modulů je nativně instalována s apache serverem, ale není povolena) je mnoho  

        ```console
        root@<your_computer_name>:~$ apt install default-mysql-server
        ```
        - tento příkaz doinstaluje a aktivuje k apache serveru MariaDB modul (databáze)
        - i navzdory tomu, že se balík nazývá default-mysql-server, nainstaluje se MariaDB (MySQL již není nativně podporován)   
        - #### Restart apache serveru

            ```console
            root@<your_computer_name>:~$ service apache2 restart
            ```
            - tento příkaz restartuje apache server
            - tento příkaz nemusí být v cestách ($PATH), nachází se ve složce ```/usr/sbin```

    ### Konfigurace
        - konfigurační soubory serveru apache se nachazí se ve složce ```/etc/apache2```
        - jednotlivé webové stránky se nachází ve složce ```/var/www/html```
        - #### Povolení (aktivace) modulu
        
            ```console
            root@<your_computer_name>:~$ /usr/aesbin/a2enmod
            ```            
            - zobrazí všechny moduly k aktivaci

            ```console
            root@<your_computer_name>:~$ /usr/aesbin/a2enmod <modul_name>
            ``` 
            - aktivace (povolení) konkrétního modulu

            ```console
            root@<your_computer_name>:~$ /usr/aesbin/a2enmod ssl
            ``` 
            - povolení modulu ssl (modul pro šifrovaná spojení - HTTPS)
            - v případě ssl je nutné zároveň povolit ssl stránky (stránky zobrazené přes HTTPS protokol), protože mají samostatnou konfiguraci, proto je nutné je povolit, k čemuž slouží příkaz:

            ```console
            root@<your_computer_name>:~$ /usr/aesbin/a2ensite default-ssl
            root@<your_computer_name>:~$ systemctl reload apache2
            ```
            - je nutné zároveň restartovat apache, což provádí druhý příkaz (tentokrát pomocí systemctl)

            - dalším modulem je userdir (opět k dispozici, ale neaktivní), který umožňuje jednotlivým uživatelům v systému zveřejnit vlastní webové stránky prostřednictvím aktuálního serveru (stejným způsobem je nakofigurován linedu)


## NFS (Network File System) server
    - systém pro sdílení adresářů mezi PC, kdy se adresáře fyzicky nacházejí na serveru, ale lze je vzdáleně namountovat (jako disk) na předem definovaných klientských PC (definice se realizuje prostřednictvím IP adres)
    - stejným způsobem probíhá sdílení školních domovských adresářů na učebnách
    ### Instalace

        ```console
            root@<your_computer_name>:~$ apt install nfs-kernel-server
        ```
        - tento příkaz nainstaluje nfs server

    ### Konfigurace
        - pozor na **firewall**!
        - konfigurace tohoto serveru se nachází v souboru /etc/exports

        ```
            <složka_k_exportu>      <konkrétní IP adresa PC či IP adresa sítě (s prefixem)>(<...parametry>)
        ```
        - formát záznamu v konfiguračním souboru

        ```
            /opt      192.168.57.0/24(rw,sync,no_subtree_check)
        ```
        - příklad záznamu v konfiguračním souboru - složka /opt se exportuje pro PC ze sítě 192.168.57.0/24 s parametry rw - čtení/zápis, sync - zápis je synchronní, no_subtree_check - neprochází se struktura adresářů a podaresářů

        ```console
            root@<your_computer_name>:~$ service nfs-kernel-server restart
        ```
        - po uložení konfigurace je nutné službu restartovat (stejně jako u jiných služeb, u kterých se mění konfigurace)

        ```console
            root@<your_computer_name>:~$ exportfs
        ```
        - kontrola, zda konfigurace je správná - příkaz by měl vrátit jendotlivé záznamy

        - zároveň je nutné, aby do exportovaného adresáře měli prává všichni uživatele (jinak nepůjde zapisovat do adresáře na vzdáleném (klientském) PC) - nutné změnit prává týkajícího se adresáře pomocí příkazu ```chmod```

    ### Realizace
        - je nutné mít druhé PC představující klienta (druhý virtuální PC)
        - na klientovi je nutné naistalovat balík, který umožní mountovat prostřednictvím nfs (vzdáleně pomocí IP adres):
        ```console
            root@<your_computer_name>:~$ apt install nfs-common
        ```

        - následně je možné vzdáleně namountovat daný adresář ze serveru:
        ```console
            root@<your_computer_name>:~$ mount 192.168.57.6:/opt /opt
        ```
        - tento příkaz namountuje složku ```/opt``` (druhá čast prvního parametru příkazu - za ```:```) ze serveru, který má IP adresu ```192.168.57.6``` (první část prvního příkazu - před ```:```) do složky ```/opt``` na klientovi (poslední parametr)

        -jedniné bezpečnostní opatření je realizováno přes IP adresy, proto je nutné aby serveru i klientském počítači měli uživatelé stejné ID uživatele i skupiny

## DHCP server
    - PC je nutné připojit do sítě, která má ve virtualboxu zakázánou DHCP službu (dva DHCP servery nejsou šťastné řešení)
    ### Instalace
    
        ```console
                root@<your_computer_name>:~$ apt install isc-dhcp-server
        ```
        - tento příkaz nainstaluje DHCP server
        - defaultně se DHCP server nerozběhne (z důvodu kolize přidelených IP adres více DHCP servery)

    ### Konfigurace
        - nachází se ve dvou souborech:
            - první v  ```/etc/default/isc-dhcp-server``` - pouze je nutné přidat do položky ```INTERFACESv4``` rozhraní, které si mají o adresu z DHCP serveru říct (typicky ```enp0s8```)

ČAS: 52:32
