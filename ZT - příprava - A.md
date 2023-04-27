# Zadání finálního testu verze A (x.x.2023)

1. (3body) Ve virtualizačním prostředí Virtualbox vytvořte PC s jedním pevným diskem a dvěma síťovými kartami. Jednu kartu připojte do sítě NAT a druhou do sítě "Host-only network". Na toto virtualizované PC nainstalujte aktuální OS Linux/Debian v minimalistické verzi, pouze s podporou protokolu SSH (bez XWindows, apod.):
    - [Tutoriál](http://seidl.cs.vsb.cz/wiki2/index.php/SOS)
    - instalace a změna defaultního IDE, instalace ```man```:
        ```console
        apt install mc
        export EDITOR=mcedit
        apt install man
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
