# Práce s disky, RAID, LVM
- je nutné mít více, než jeden disk (přidat jich více)
- v Linuxu je jednotný souborový systém - jednotlivé adresáře souborového systému jsou připojovány (svázány) k daným diskům (vazba disk -> adresář)
- seznam disků připojených k PC se nachází ve složce ```/dev``` (stejně jako ostatní HW, atd...), kdy ```sd<písmeno>``` (např. ```sda```) označuje disk a ```sd<písmeno><číslo>``` pak oddíl disku (partition) (např. ```sda1```)
- aby bylo možné nějaký disk využívat je nutné vytvořit tabulku oblastí (oddílů) (nemusí se provadět - volitelné), naformátovat a následně tento disk namountovat
- utilitka ```cfdisk``` slouží k vytvoření oblastí (tabulky oblastí) na disku (např. ```cfdisk /dev/sdb```)
- stále však není tato oblast naformátovaná (není v ní vytvořený souborový systém) (pokud se neprovede rozdělení disku, je nutné naformátovat celý disk)
- souborových systému existuje nepřeberné množství, v současnosti je nejrozšířenější souborový systém ```ext4```, neboť je velmi výkonný v mnoha aspektech (parametrech)
- pro naformátování se používá utilitka ```mkfs```
  ```console
  root@<your_computer_name>:~$ mkfs.ext4 /dev/sdb1
  ```
  - za ```.``` následuje vybraný souborový systém a jako parametr se bere název (cesta) zařízení (v tomto případě ```/dev/sdb1```)
- nyní je možné namountovat disk do adresáře - dá se využít jakýkoli adresář, nicméně je doporučeno využívat adresář ```/mnt``` k tomu určený
- mountování - proces spojení (svázání, připojení) disku (či oddílu disku) s nějakým adresářem v PC
- k tomuto účelu se používá příkaz ```mount```
  ```console
  root@<your_computer_name>:~$ mount
  ```
  - vypíše informace ohledně stavu mountování v PC
  
  ```console
  root@<your_computer_name>:~$ mount | grep sda1
  ```
  - vypíše informace ohledně mountu disku (oddílu) sda1
  ```console
  root@<your_computer_name>:~$ mount /dev/sdb1 /mnt
  ```
  - tento příkaz namountuje disk (oblast) ```sdb1``` do adresáře ```/mnt```
  - v dané složce (v tomto případě ```/mnt```) by se měl vytvořit adresář ```lost+found``` (známka toho, že se jedná o namountovaný adresář)
- pro rozvázání adresáře s diskem se používá příkaz ```unmount```, kde se jako parametr může použít daná složka (ve výše zmíněném případě ```/mnt```) nebo název disku (oblasti) (v příkladě ```/dev/sdb1```) - tímto způsobem lze dokázat, že se něco opravdu zapsalo na disk (po odmountování to něco již nebude k dispozici)
- ## fstab
  - konfigurační soubor specifikující defaultní (po startu (bootu) PC) konfiguraci disků či oblastí (jejich mountování) jako takových - aby se disky či oddíly namountovaly automaticky po startu PC
  - nachází se v ```/etc/fstab```
    ```
    <file system> <mount point> <type> <options> <dump> <pass> 
    ```
    - formát záznamu konfigurační soubour, kde:
      - ```<file system>``` - disk (oddíl/oblast), která se bude mountovat - lze specifikovat názvem (např. ```/dev/sda```) nebo ```UUID``` (např. ```UUID=<číslo s pomlčkami>```), ```UUID``` je preferováno (narozdíl od názvu, které se odvíjí od pořadí připojení disku k řadiči, které se může měnit), ```UUID``` příslušného disku či oddílu lze zjistit příkazem ```blkid```
      - ```<mount point>``` určuje místo, kam se disk specifikovaný ve ```<file system>``` namountuje (např. ```/mnt```)
      - ```<type>``` určuje souborový systém disku (např. ```ext4```)
      - ```<options>``` definuje parametry (např. ```ro``` - disk je určen pouze pro čtení), standartně ```defaults```
      - ```<dump>``` a ```<pass>``` mají historický význam, týkaly se zálohování, standartně se udává hodnota ```0   0``` (pro oba ```0```), u root file systému (první záznam souboru) ```0    1```
  ```console
  root@<your_computer_name>:~$ mount -a
  ```
  - tento příkaz namountuje všechny disky uvedeny v souboru ```fstab```

- ## RAID
  - odkaz na [wikipedii](https://cs.wikipedia.org/wiki/RAID) (jsou tam obrázky)
  - technologie, sloužící k ochraně dat proti vypadku disku
  - může být hardwarová (např. produkční servery) nebo softwarová (např. osobní NAS - Network Attached Storage)
  - nejedná se o zálohu, pouze chrání data proti chybě, výpadku, závadě nějákého disku
  - jednotlivé RAIDy se dají kombinovat
  - několik označení (typů) RAIDů:
    - RAID 1
      - obsahuje dva disky (klidně více 4, 6, 8, ...)
      - prinicpiálně velmi jednoduchý - co se zapíše na první disk, se zapíše taktéž na druhý disk (z tohoto důvodu se také někdy tomuto ozančení RAIDu přezdívá zrcadlení)
      - velikost (kapacita): (N * S) / 2, kde N = počet disků v RAIDu; S = velikost jednoho disku, např. požaduje se 1TB disk (celkově 1TB místa) => přidání druhého 1TB disku („zrcadla“) = 1TB místa (celkově dva 1TB disky: (2 * 1TB) / 2 = 1TB, kde N = 2; S = 1TB)
      - neefektivní z hlediska místa, ale navyšuje rychlost čtení (paralelně z obou disků - na každém z různých sektorů), při čtení se musí data zapsat na oba disky)
      - pokud nějaký disk havaruje označí se jako špatný a ostatní budou fungovat
    - RAID 5
      - jeden z nejpouživanějších RAIDů
      - minimální počet disků jsou tři (a více 4, 5, 6, ...)
      - princip - data se zapisují po blocích, jeden blok na první disk, druhý na druhý disk, ... a na poslední je zaznamenám tzv. checksum - číslo, na základě kterého je možné dopočítat příslušný blok jednoho špatného (zhavarovaného) disku, pro každý blok se pokaždé pro checksum určí jiný disk z RAIDu (pokud se pro všechny bloky pro checksum určí jeden disk, který by zhavaroval, nelze provést obnovu)
      - odolný proti výpadku jednoho disku
      - velikost (kapacita): (N * S) - (1 * S), kde N = počet disků v RAIDu; S = velikost jednoho disku, např. požadují se tři 1TB disky (celkově 3TB) => přidání jednoho dalšího 1TB disku = 3TB místa (celkově čtyři 1TB disky: (4 * 1TB) - (1 * 1TB) = 3TB, kde N = 4; S = 1TB)
      - z hlediska místa efektivní, z hlediska čtení efektivní (jednotlivé bloky se mohou zapisovat paralelně), z hlediska čtení opět velmi efektivní (stejně jako u zápisu)
    - RAID 6
      - prakticky se jedná o rozšíření RAIDu 5, kdy checksumy jsou dva a jsou nutné minimálně 4 disky
      - odolný proti výpadku dvou disků 
      - velikost (kapacita): (N * S) - (2 * S), kde N = počet disků v RAIDu; S = velikost jednoho disku, např. požadují se tři 1TB disky (celkově 3TB) => přidání dvou dalších 1TB disků = 3TB místa (celkově pět 1TB disků: (5 * 1TB) - (2 * 1TB) = 3TB, kde N = 5; S = 1TB)
    - RAID 0
      - není odolný proti výpadku disku
      - pouze serializuje disky
      - používá se v kombinaci s ostatními RAIDy pro navýšení kapacity
  - ### Spare disk
    - „horká“ záloha
    - pokud selže jeden z primárních disků, je tento disk okamžitě automaticky odpojen a místo něj se automaticky připojí záložní spare disk, který se tím stává primárním diskem
    - spare disků lze připojit více

  - ### RAIDy v GNU/Linux
    - pro práci s RAIDy slouží utilitka ```mdadm```:
        ```console
        root@<your_computer_name>:~$ apt install mdadm
        ```
        - tento příkaz nainstaluje utilitku ```mdadm```
        - zároveň vytvoří konfigurační soubor  ```/proc/mdstat```, do kterého ```mdadm``` promítá informace o vytvořených RAIDech
        ```console
        root@<your_computer_name>:~$ mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd --spare-devices=1 /dev/sde
        ```
        - tento příkaz vytvoří nový RAID, kde:
          - ```--create``` vytvoří nový RAID (v tomto případě ```/dev/md0```)
          - ```--level``` určuje příslušný typ (označení) RAIDu (v tomto případě ```1```, tzn. RAID 1)
          - ```--raid-devices``` určuje celkový počet disků v RAIDu (bez spare disků) následující cestami (názvy) jednotlivých disků, které mají tvořit daný RAID (v tomto případě ```/dev/sdc``` a ```/dev/sdd```), oddělené mezerami
          - ```--spare-devices``` určuje počet spare disků v RAIDu následující cestami (názvy) jednotlivých disků, které mají tvořit spare disky daného RAIDu (v tomto případě pouze ```/dev/sde```), oddělené mezerami
          - RAID se vytvoří jako zařízení ```/dev/md0```, to znamená, že s ním bude zacházeno jako s diskem
      - protože se s RAIDem zachází jako s nově připojeným diskem, bude nutné jej naformátovat a namountovat:
        ```console
        root@<your_computer_name>:~$ mkfs.ext4 /dev/md0
        ```
        - tento příkaz naformátuje zařízení ```/dev/md0``` (výše vytvořený RAID)
        ```console
        root@<your_computer_name>:~$ mount /dev/md0 /mnt
        ```
        - tento příkaz namountuje zařízení ```/dev/md0``` (výše vytvořený RAID) do složky /mnt
      - jeden disk z RAIDu lze záměrně (uměle) poškodit (označit jako pošokžený/faulty) v rámci testování RAIDu:
        ```console
        root@<your_computer_name>:~$ mdadm --manage /dev/md0 --set-faulty /dev/sdd
        ```
        - tento příkaz v RAIDu ```/dev/md0``` označí disk ```/dev/sdd``` jako poškozený, a protože se jedná RAID obsahující spare disk, měl by tento příkaz způsobit odpojení „poškozeného“ (tento příkaz jej pouze tak ozačí) primárního disku a následné okamžité připojení (záložního) spare disku, který se tak stane primárním diskem
      - pro kontrolu lze použít příkaz ```watch```:
        ```console
        root@<your_computer_name>:~$ watch -n 0 "cat /proc/mdstat"
        ```
        - tento příkaz bude cyklicky spouštět příkaz ```cat /proc/mdstat```, čímž se bude kontrolovat stav daného RAIDu (je vhodné jej spustit v nové konzoli)
      - pro výměnu disků poškozeného disku za nový se musí vykonat následující příkazy:
        ```console
        root@<your_computer_name>:~$ mdadm --manage /dev/md0 --remove /dev/sdd
        root@<your_computer_name>:~$ mdadm --manage /dev/md0 --add /dev/sdd
        ```
        - první příkaz odebere poškozený disk (v tomto případě ```/dev/sdd```)
        - následně by se disk fyzicky vyměnil za nový
        - druhý příkaz přidá nový disk (v tomto případě ```/dev/sdd```) - nový disk bude přidán jako spare disk
      - pro automatické mountování RAIDu po startu PC je nutné tento RAID ve ```fstabu```u definovat pomocí jeho UUID!
      - 
## LVM (nebude na ZT)
- technologie zjednodušující práci s diskovými oddíly
- umožňuje přídávání disků za běhu systému
- velmi oblíbené na serverech
- založen na vytváření tzv. grupy
- odkaz na článek ze serveru [Root](https://www.root.cz/clanky/uvod-do-lvm/#ic=articles-related&icc=lvm-prakticke-ukazky-17056)
