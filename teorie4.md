# Práce s disky, RAID, LVM
- je nutné mít více, než jeden disk (přidat jich více)
- v Linuxu je jednotný souborový systém - jednotlivé adresáře souborového systému jsou připojovány (svázány) k daným diskům (vazba disk -> adresář)
- seznam disků připojených k PC se nachází ve složce ```/dev``` (stejně jako ostatní HW, atd...), kdy ```sd<písmeno>``` (např. ```sda```) označuje disk a ```sd<písmeno><číslo>``` pak oddíl disku (partition) (např. ```sda1```)
- aby bylo možné nějaký disk využívat je nutné vytvořit tabulku oblastí (oddílů) (nemusí se provadět - volitelné) a následně tento disk namountovat
- utilitka ```cfdisk``` slouží k vytvoření oblastí (tabulky oblastí) na disku (např. ```cfdisk /dev/sdb```)
- stále však není tato oblast naformátovaná (není v ní vytvořený souborový systém)
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
      - ```<mount point>``` - místo, kam se disk specifikovaný ve ```<file system>``` namountuje (např. ```/mnt```)
      - ```<type>``` - souborový systém disku (např. ```ext4```)
      - ```<options>``` - parametry (např. ```ro``` - disk je určen pouze pro čtení), standartně ```defaults```
      - ```<dump>``` a ```<pass>``` - dány historicky, týkaly se zálohování, standartně se udává hodnota ```0   0``` (pro oba ```0```), u root file systému (první záznam souboru) ```0    1```
   ```console
  root@<your_computer_name>:~$ mount -a
  ```
  - tento příkaz namountuje všechny disky uvedeny v souboru ```fstab```

- ## RAID
  - odkaz na [wikipedii](https://cs.wikipedia.org/wiki/RAID) (jsou tam obrázky)
  - technologie, sloužící k ochraně dat proti vypadku disku
  - nejedná se o zálohu, pouze chrání data proti chybě, výpadku, závadě nějákého disku
  - několik označení (typů) RAIDů:
    - RAID 1
      - obsahuje dva disky (klidně více 4, 6, 8, ...)
      - prinicpiálně velmi jednoduchý - co se zapíše na první disk, se zapíše taktéž na druhý disk (z tohoto důvodu se také někdy tomuto ozančení RAIDu přezdívá zrcadlení)
      - velikost: požaduje se 1TB disk => přidání druhého 1TB disku („zrcadla“) = v RAID 1 stále 1TB místa
      - neefektivní z hlediska místa, ale navyšuje rychlost čtení (paralelně z obou disků - na každém z různých sektorů)
      - pokud nějaký disk havaruje označí se jako špatný a ostatní budou fungovat
    - RAID 5
      - jeden z nejpouživanějších RAIDů
      - minimální počet disků jsou tři
      - princip - pro každý blok disku je jeden z disků určen 