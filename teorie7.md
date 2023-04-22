# Konfigurace služeb

- ## LAMP (Linux, Apache, MariaDB (kdysi MySQL), PHP/Perl/Python) server
    - umožňuje z jednoho serveru (jedné IP adresy) zprovoznit mnoho webových stránek
    - ### Instalace

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
        - konfigurace a zacházení s tímto modulem (s databází) se nachází na [webové stránce](http://seidl.cs.vsb.cz/wiki2/index.php/SOS) (osmá přednáška) a na videu:
        <iframe width="560" height="315" src="https://www.youtube.com/embed/672DyTiauWc?start=1414" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
        - #### Restart apache serveru

            ```console
            root@<your_computer_name>:~$ service apache2 restart
            ```

            - tento příkaz restartuje apache server
            - tento příkaz nemusí být v cestách ($PATH), nachází se ve složce ```/usr/sbin```

    - ### Konfigurace
        - konfigurační soubory serveru apache se nachazí se ve složce ```/etc/apache2```
        - jednotlivé webové stránky se nachází ve složce ```/var/www/html```
        - #### Povolení (aktivace) modulu
        
            ```console
            root@<your_computer_name>:~$ /usr/sbin/a2enmod
            ``` 

            - zobrazí všechny moduly k aktivaci

            ```console
            root@<your_computer_name>:~$ /usr/sbin/a2enmod <modul_name>
            ``` 

            - aktivace (povolení) konkrétního modulu

            ```console
            root@<your_computer_name>:~$ /usr/sbin/a2enmod ssl
            ``` 

            - povolení modulu ssl (modul pro šifrovaná spojení - HTTPS)
            - v případě ssl je nutné zároveň povolit ssl stránky (stránky zobrazené přes HTTPS protokol), protože mají samostatnou konfiguraci, proto je nutné je povolit, k čemuž slouží příkaz:

            ```console
            root@<your_computer_name>:~$ /usr/sbin/a2ensite default-ssl
            root@<your_computer_name>:~$ systemctl reload apache2
            ```

            - je nutné zároveň restartovat apache, což provádí druhý příkaz (tentokrát pomocí systemctl)

            - dalším modulem je userdir (opět k dispozici, ale neaktivní), který umožňuje jednotlivým uživatelům v systému zveřejnit vlastní webové stránky prostřednictvím aktuálního serveru (stejným způsobem je nakofigurován linedu)


- ## NFS (Network File System) server
    - systém pro sdílení adresářů mezi PC, kdy se adresáře fyzicky nacházejí na serveru, ale lze je vzdáleně namountovat (jako disk) na předem definovaných klientských PC (definice se realizuje prostřednictvím IP adres)
    - stejným způsobem probíhá sdílení školních domovských adresářů na učebnách
    - ### Instalace

        ```console
        root@<your_computer_name>:~$ apt install nfs-kernel-server
        ```

        - tento příkaz nainstaluje nfs server

    - ### Konfigurace
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

    - ### Realizace
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

- ## DHCP server
    - PC je nutné připojit do sítě, která má ve virtualboxu zakázánou DHCP službu (dva DHCP servery nejsou šťastné řešení)
    - ### Instalace
    
        ```console
        root@<your_computer_name>:~$ apt install isc-dhcp-server
        ```
        - tento příkaz nainstaluje DHCP server
        - defaultně se DHCP server nerozběhne (z důvodu kolize přidelených IP adres více DHCP servery)

    - ### Konfigurace
        - nachází se ve dvou souborech:
            - první v  ```/etc/default/isc-dhcp-server``` - pouze je nutné přidat do položky ```INTERFACESv4``` rozhraní, které si mají o IP adresu (dané verze - IPv4) z DHCP serveru říct (typicky ```enp0s8```)
            - druhá se nachází v /etc/dhcp/dhcp.conf - změnit několik položek:
                - změnit adresu primárního a sekundárního DNS serveru (položka ```option domain-name-servers```):
                  ```
                  option domain-name-servers <adresa primárního DNS serveru> <adresa sekundárního DNS serveru>;
                  ```
                
                - například:
                  ```
                  option domain-name-servers 10.0.2.3 8.8.8.8;
                  ```

                - další možnou (nikoliv nezbytnou) položkou ke změně je čas, na který si PC podrží přidělenou IP adresu, než bude žádat o novou ```default-lease-time``` a ```max-lease-time```, což je maximální možná doba, po kterou si PC podrží svou IP adresu (hodnota položek udávaná v sekundách)

                - změnit položku ```subnet``` (několik možných formátu - vhodný je ```BOOTP clients```):
                ```
                subnet <IP adresa sítě (typicky síť serveru)> netmask <maska sítě (typicky 255.255.255.0)> {
                    range <začátek rozsahu> <konec rozsahu>;
                    option broadcast-address <adresa borad-cast - poslední adresa sítě>;
                    option routers <IP adresa serveru>;
                }
                ```
                - tento záznam nastaví počítači, který si o IP adresu řekne, vše, co potřebuje ke komunikaci po síti:
                    - ```subnet``` - síť, ze které se budou adresy přidělovat
                    - ```netmask``` - maska sítě definovaná v ```subnet```
                    - range - rozsah adres z dané sítě, které se budou jednotlivým zařizením přidělovat
                    - ```option broadcast-address``` - broadcast adresa sítě definované v ```subnet``` (standartně X.X.X.255)
                    - ```option routers``` - IP adresa, kterou zařízení, kterým se adresa poskytne, použijí jako výchozí bránu - standartně adresa serveru
                - DNS server se nastavuje v položce ```option domain-name-servers``` (viz. výše)
                - v ```subnet``` položce lze definovat i DNS server a všechny ostatní položky (např. ```default-lease-time```), server může spravovat mnoho ```subnet``` položek pro různé sítě i DNS
                - lze použít globální konfiguraci nebo lokální konfiguraci
        - po změně konfigurace je opět nutné provést restart služby:
        ```console
        root@<your_computer_name>:~$ usr/sbin/service isc-dhcp-server restart
        ```

        - na virtuálním PC představující zařízení žádající o přidělení IP adresy je nutné shodit a opětovně nahodit síťovou kartu, následně si říct o IP adresu ze serveru a otestovat pomocí příkazu ```ping```:
        ```console
        root@<your_computer_name>:~$ ifdown enp0s3
        root@<your_computer_name>:~$ ifup enp0s3
        root@<your_computer_name>:~$ dhclient enp0s3
        root@<your_computer_name>:~$ ping post.cz
        ```
