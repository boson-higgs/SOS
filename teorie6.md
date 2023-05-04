# Konfigurace sítě a firewall
  - pro připojení do internetu nutnost nastavit: ip adresu, masku sítě, defaultní gateway, DNSku

- ## Utilitka ip
  - nahrazuje utilitku ifconfig
  - ```ip addr``` vypíše konfiguraci síťových rozhraní - interface
  - stav jednoho zařízení se dělá pomocí ```ip addr show dev eth0```
  - vypnutí / zapnutí interface ```ip link set dev eth0 up / down```
  - přidání IP adresy k rozhraní: ```ip addr add 192.168.1.11/24 dev eth0```
  - odebrání IP adresy k rozhraní: ```ip addr del 192.168.1.11/24 dev eth0```
  - routování: ```ip route```
  - https://www.root.cz/clanky/prikaz-ip-ovladnete-linuxova-sitova-rozhrani/

- ## Přiřazení ip adresy natrvalo (i po rebootu) 
  - nastavuje se v ```/etc/network/interfaces```
  - konfigurace:
 
        ```
        allow-hotplug enp0s8
        iface enp0s8 inet static
          address 192.168.57.2/24
        ```
        
   - inreface se také dá vypnout a zapnout pomocí: ```ifdown / ifup enp0s8```

- ## Linuxový firewall
  - statický firewall
  - stavový firewall
  - [princip](https://www.abclinuxu.cz/blog/Debian_Lenny/2009/10/zakladni-konfigurace-linux-firewallu-pomoci-iptables)
  - ### Utilitka iptables
    - pokud jejedno pravidlo ACCEPT, pak se další pravidla už nevyhodnocují -> nečastěji používaná pravidla je třeba mít nahoře
      ```
      iptables -P INPUT DROP
      iptables -A INPUT -i enp0s8 -p icmp -j ACCEPT
      
      ```
   
  - ### Gateway (NAT)
    - na klientovi routování přes server: ``` ip route add default via 192.168.57.2 ```
   
  - ### Překlad síťových adres (NAT)
    - na serveru: ``` iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE ```
    - maškaráda mi říká, když paket opouští enp0s3, tak se mu dá zdrojová ip adresa, kterou má ten daný interface
      - #### Přeposílání paketů mezi interface
        ``` echo 1 > /proc/sys/net/ipv4/ip_forward/ ```
        
      - #### Nastavení i po rebootu
        ```
        vim /etc/sysctl.conf
        net.ipv4.ip_forward = 1
        ```
      - #### DNS
        - v ``` /etcresolv.conf ```
       
   - ### Utilitka netcat
    - slouží k vyzkoušení komunikace na daných portech
    - na serveru: ``` netcat -l -p 8888 ```
    - na klientu: ``` netcat 192.168.57.2 8888 ```
      
- ### Utilitka iptables-persistent
  ```
  apt install iptables-persistent
  iptables-save > /tmp/iptest
  iptables-restore /tmp/iptest
  
  ```
  - všechna pravidla v ```/etc/iptables/rules.v4```
