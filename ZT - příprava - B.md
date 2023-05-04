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
