# Systemd, Cron

- ## BIOS a UEFI
  - program nacházející se v EEPROM či Flash na základní desce
  - slouží při spuštění PC
  - provádí inicializaci a konfiguraci připojených hardwarových zařízení
  - následně spustí OS
  - ### BIOS (Basic Input Output System)
    - každý OS má svůj zavaděč
    - nevýhoda při dualbootu
  - ## UEFI (Unified Extensible Firmware Interface)
    - umí vybrat zavaděč konkrétní OS a pracovat s ním samostatně

- ## GRUB
  - zavaděč pro OS GNU/Linux
  - modulární
  - může dostvávat parametry, které mhohou měnit chování systému

- ## Proces Init
   - kořenový proces
   - má PID 1
   - má na starost rozběhnout celý systém
   - Linux jako takový pouze vytváří rozhraní mezi HW a SW, stará se o správu paměti, komunikaci po síti, ale o běh samotného systému se stará proces (program, systém) ```Init```, který má Linux za úkol spustit

- ## Systemd
  - má za úkol spustit všechny programy, které dělájí systém systémem
  - snaží se na sebe natáhnout spoustu funkcionalit jiných systémů, např.: logování, cyklické spouštění programů, atd.
  - snaží se spouštět procesy (služby) paralelně (pokud to lze) => podstatně rychlejší spouštění systému
  - umí spustit služby až když jsou potřeba (např. webový server)
  - ### Jednotka
    - část ```Systemd```, která se stará o zavedení nějaké služby do OS
    - nachází se ve složce ```/etc/systemd/system``` a mají příponu ```.service```
    - má daný formát popsaný u [třetí přednášky](http://seidl.cs.vsb.cz/wiki2/index.php/SOS):
      - skládá se ze tří částí:
        - [Unit] - popis jednotky - závislosti, popis (dokumentace)
        - [Service] - definice toho, co se má udělat
        - [Install] - uvádí se jakého celku bude příslušná jednotka součástí - lze je seskupovat (skupina má příponu ```.target```)
    - #### Vytvoření a spuštění jednotky:
      - vytvořit skript, který se má spouštět
      - ve složce ```/etc/systemd/system``` vytvořit příslušný soubour s příponou ```.service```
      - pro manipulaci s jednotkami se využivá příkaz ```systemctl```:
        ```console
        root@<your_computer_name>:~$ systemctl status sshd
        ```
        - tento příkaz vypíše informace o stavu jednotky ```sshd```
        ```console
        root@<your_computer_name>:~$ systemctl start test1
        ```
        - tento příkaz spustí službu s názvem ```test1```
        ```console
        root@<your_computer_name>:~$ systemctl stop test1
        ```
        - tento příkaz zastaví službu s názvem ```test1```
        ```console
        root@<your_computer_name>:~$ systemctl daemon-reload
        ```
        - tento příkaz aktualizuje ```daemon``` ```systemd``` (aktualizuje strom závislostí, ...), vhodné zadat vždy při změně či vytvoření jednotky
        ```console
        root@<your_computer_name>:~$ systemctl enable test1
        ```
        - tento příkaz vytvoří ```symlink``` na službu ```test1``` ve složce dané skupiny (bude se spouštět v rámci skupiny, např.: při startu PC) - povolí se
        ```console
        root@<your_computer_name>:~$ systemctl is-enabled test1
        ```
        - tento příkaz vypiše informaci o tom, zda je tato služba povolena

- # Cron
  - systém pro cyklické spouštění skriptů (příkazů)
  - uživatelský a systémový
  - je výhodnější použít tento systém, než ```systemd```, který to taktéž umí
  - jeho konfigurační soubor (systémový  ```cron```) má umístění  ```/etc/crontab```, ve kterém je popsán i jeho formát
  - je vhodné v sekci příkazů nejdřive otestovat, zda daný soubor obsahující skript existuje
  - konfigurační soubor uživatelského ```cron```u se otevírá příkazem  ```crontab -e``` a jeho skripty se spouští výlučně pod daným uživatelem (formát záznamu identický se systémovým ```cron```em výjma definice uživatele)
