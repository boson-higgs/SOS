# Uživatelská práva, diskové kvóty
  - ## Uživatelé
    - ### „Klasický“ uživatel
      - uživatel, který je určen zpravidla fyzické osobě
    - ### Systémový uživatel
      - uživatel, který je určen službě (pod kterým se nějaká služba spouští)
      - služby lze spouštět pod rootem (což není dobrý nápad - služba by měla všechna prává v systému)
      - systémovému uživateli lze udělit určité restrikce v rámci práv a pod ním (s jeho právy) se bude služba spouštět (služba by měla mít pouze ta práva, která potřebuje ke své činnosti)
      - nelze se pod tímto uživatelem přihlásit do systému
    - ### Skupiny
      - uživatele lze shlukovat do skupin
      - každý uživatel patří do nějaké skupiny
      - defaultně při vytvoření uživatele, patří uživatel do skupiny, která se jmenuje stejně jako jeho login (tato skupina se vytvoří při vytvoření uživatele)
      - lze vytvářet samostatné skupiny a uživatele do nich přiřazovat
    - ### Práce s uživateli
      - informace o uživatelých (jak „klasických“, tak systémových) lze nalézt v souboru ```/etc/passwd```:
        ```
        <login>:<>:<UID>:<GID>:<poznámky>:<domovský adresář>:<shell>
        ```
        - formát záznamu souboru ```/etc/passwd``` (záznam uživatele), kde:
          - ```<login>``` je login uživatele
          - ```<>```
          - ```<UID>``` znamená ID (jednoznáčny, unikátní identifikátor) uživatele v rámci systému
          - ```<GID>``` znamená ID (jednoznáčny, unikátní identifikátor) skupiny, do které uživatel patří
          - ```<poznámky>``` jsou detailnější informace o uživateli (nepovinné) ve formátu ```<celé jméno>,<číslo místnosti>,<telefon do zaměstnání>,<telefon domů>,<ostatní informace>```
          - ```<domovský adresář>``` ukazuje umístění (cestu) k domovskému adresáři daného uživatele
          - ```<shell>``` je interpret příkazů, který daný uživatel využívá
      - pro „klasické“ uživatele zpravidla lze nalézt jejich domovské adresáře v adresáři ```/home```
      - informace ohledně přihlašovacch hesel jednotlivých uživatelů se nachází v souboru ```/etc/shadow``` (hash hesla, informace o době platnosti hesla, ...), detailnější informace se nachází v manuálových stránkach (```man shadow```)
      - informace o skupinách uživatelů se nacházejí v souboru ```/etc/group```
      - adresář ```/etc/skel``` slouží jako template pro vytváření uživatelů, při jeho vytvoření sa tento adresář zkopíruje (s příslušnými právy) do domovského adresáře uživatele, pokud je nutné, aby uživatel měl při vytvoření ve svém domovském adresáři již něco defaultně umístěno, bude to něco umístěno právě do tohoto adreásře (při vytvoření uživatele se tento adresář zkopíruje spolu s tím něčím)
      - #### adduser
        ```console
        root@<your_computer_name>:~$ adduser franta
        ```
        - tento příkaz vytvoří uživatele franta
        - při vytvoření se ptá na dané informace ohledně uživatele (heslo, celé jméno, číslo místnosti, ...), proto není vhodný pro vytváření uživatelů skriptem
        ```console
        root@<your_computer_name>:~$ deluser franta
        ```
        - tento příkaz odstraní uživatele franta
        - bývá zvykem při osdtranění uživatele zachovat jeho domovský adresář (lze však vynutit odstranění domovského adresáře pomocí příznaku ```--remove-home```)
      - #### useradd
        - určený přímo pro vytváření uživatelů skriptem
        - je nutné použít ```openssl``` (```apt install openssl```)
          ```console
          root@<your_computer_name>:~$ useradd -m -s /bin/bash -c "Bezny Franta Uzivatel" -p $(echo "P4sSw0rD" | openssl passwd -1 -stdin) franta
          ```
          - tento příkaz vytvoří nového uživatele franta, kde:
            - ```-m``` vytvoří uživateli domovský adresář
            - ```-s``` nastaví interpret příkazu (v tomto případě na ```/bin/bash```)
            - ```-c``` je poznámka k uživateli (v tomto případě: Bezny Franta Uzivatel)
            - ```-p``` nastaví heslo (jeho hash) (pomocí ```openssl```, kterému se na vstup přiřadí dané heslo a, ke kterému vrátí příslušný hash)
          - lze vytvořit i uživatele bez hesla (s prázdným řetězcem za parametrem ```-p```), nicméně vždy by se měla okamžitě u uživatele vynutit jeho změna
      - #### passwd
        - utilitka pro manipulaci s hesly
          ```console
          root@<your_computer_name>:~$ passwd -e franta
          ```
          - tento příkaz způsobí vynucení hesla uživatele franta (okamžitě po jeho přihlášení)
    - ### Práce skupinami
      ```console
      root@<your_computer_name>:~$ addgroup student
      ```
      - tento příkaz vytvoří novou skupinu student
      ```console
      root@<your_computer_name>:~$ addgroup franta student
      ```
      - tento příkaz přiřadí uživatele franta do skupiny student
      ```console
      root@<your_computer_name>:~$ delgroup franta student
      ```
      - tento příkaz odstraní uživatele franta ze skupiny student
  - ## Uživatelská práva
    ```console
    drwxrwxrwx <uživatel - vlasntík> <skupina - vlastník>
    ```
    - formát uživatelských práv, kde:
      - ```d``` známená že se jedná o adresář (pokud se jedná o soubor, místo ```d``` se na této pozici nachází ```-```)
      - ```r``` znamená opravnění pro čtení
      - ```w``` znamená opravnění pro zápis
      - ```x``` znamená opravnění pro spuštění (u adresáře to znamená průchod adresářem - vylistování adresáře)
      - první trojice ```rwx``` jsou práva uživatele (vlastníka) ```<uživatel - vlastník>```
      - druhá trojice ```rwx``` jsou práva skupiny (skupiny, která jej vlastní) ```<skupina - vlastník>```
      - třetí trojice ```rwx``` jsou práva pro všechny ostatní
      - pokud některé z těchto oprávnění není povoleno, je na dané pozici místo příslušného písmene znak ```-```
    ```console
    root@<your_computer_name>:~$ chgrp student test_prav
    ```
    - tento příkaz změní vlastníka (skupinu) souboru/adresáře test_prav na skupinu student
    ```console
    root@<your_computer_name>:~$ chmod g+w test_prav
    ```
      - tento příkaz přídá skupině oprávnění zápisu (```w```)
      - ```chmod <kdo><jak><co> <název soubour/adresáře>```
      - ```<kdo>``` - ```u```(uživatel - vlastník)/```g```(skupina - vlastník)/```o```(ostatní)
      - ```<jak>``` - ```+```(přidávání)/```-```(ubírání)
      - ```<co>``` - ```r```(čtení)/```w```(zápis)/```x```(spuštění)
      - změna práv adresáře neplatí zároveň na jeho obsah (rekurze), to lze změnit přepínačem ```-R```
      - práva lze vyjádřit jako decimální součet binárního vyjádření v rámci jednotlivých trojic:
        ```console
        root@<your_computer_name>:~$ chmod 777 test_prav
        ```
        - přídá všechna práva všem
    - ### Rozšířená práva (s/t bit)
      - blíže specifikují či upravují výše zmíněná práva
      - #### t bit
        - specifikuje ```x``` bit
        - pokud je nastaven, znamená to, že v daném adresáři smí uživatel manipulovat (mazat) pouze vlastní soubory (či podadresáře)
      - #### s bit
        - specifikuje ```x``` bit
        - pokud je nastaven, tak jakémukoli souboru (či podadresáři) vytvořeném v tomto adresáři, bude nastaven vlastník tohoto adresáře (podle toho, ve které z trojic se ```s``` bit nachází, např. pokud se v adresáři s právy ```drwxrws---``` vytvoří soubor, pak tomuto souboru budou nastavena práva nikoli skupiny toho, kdo soubor vytvořil, ale skupiny (vlastníka) daného adresáře s ```s``` bitem)
        - standartně se programy spouští s oprávněním toho, kdo jej spustil (spuštěný program může to, co může ten, kdo jej spustil), pokud však je u tohoto programu nastaven ```s``` bit u uživatele (vlastníka), program se spustí s právy tohoto uživatele (vlastníka) (nikoli s právy toho, kdo jej spustil), proto obecně nikdy nenastavovat ```s``` bit u uživatele (vlastníka), a už vůbec nikdy u souborů (programů), které vlastní root!
       - existují další rozšířená práva (atributy), která rozšiřují ta základní ```rwx``` (např. specifikace čistě pro mazání, ...), u Debianu jsou standartně vypnuta

  - ## Diskové kvóty
    - místo (disková velikost) definované uživateli
    - vázané na konkrétní svazek (disk) (jiné kvóty na jiných discích)
    - způsob jakým uživatelům zabránit přehlcení disku
    - standartně se kvóty pro uživatele definují na novém disku (namountovém do adresáře ```/home```)
    - dělí se na:
      - kvótu měkkou (soft) - lze překročit, slouží jako indikátor, že se uživatel blíží k maximálnímu zaplnění své kvóty (která nelze překročit)
      - pevnou (hard) - nelze překročit
    - pro práci s kvótami se využívá utilitka ```qouta```:
      ```console
      root@<your_computer_name>:~$ apt install quota
      ```
        - tento příkaz nainstaluje utilitku ```qouta```
      ```console
      root@<your_computer_name>:~$ mount -o remount,usrquota,grpquota /home
      root@<your_computer_name>:~$ service quota start
      root@<your_computer_name>:~$ quotacheck /dev/sdb1
      root@<your_computer_name>:~$ quotaon /dev/sdb1
      ```
        - první příkaz přemountuje (změní parametry mountpointu), kdy přídá parametry ```usrquota``` a ```grpquota```
        - druhý příkaz nastartujte (spustí) službu pracující s kvótami
        - třetí příkaz slouží k ověření, zda jsou na svazku (disku) kvóty podporovány (v tomto případě na svazku ```/dev/sdb1```)
        - čtvrtý příkaz zapne kvóty na daném svazku (v tomto případě na svazku ```/dev/sdb1```)
      ```console
      root@<your_computer_name>:~$ edquota franta
      ```
        - tento příkaz umožní nastavit uživateli franta kvóty (otevře editor s konfigurací)
      ```console
      root@<your_computer_name>:~$ quota franta
      ```
        - tento příkaz zobrazí nastavení kvóty uživatele franta
      ```console
      root@<your_computer_name>:~$ repquota -a
      ```
        - tento příkaz vypíše informace o všech uživatelích a jejich kvótách
      ```console
      root@<your_computer_name>:~$ dd if=/dev/zero of=nuly bs=1k count=1000
      ```
        - tento příkaz vytvoří soubour o počtu 1000 bloků, kdy jeden blok má velikost 1kB (celkově jeden soubor o velikosti 1MB)
        - vhodný pro testování kvót
      ```console
      root@<your_computer_name>:~$ edquota -p franta franta2
      ```
        - tento příkaz uživateli franta2 nastaví stejnou kvótu jakou má uživatel franta (tzv. prototyp)
        - je vhodné si vytvořit prototyp uživatele, kterému se kvóta nastaví ručně a při vytváření ostatních uživatelů (v cyklu) se přiřadí tato kvóta z prototypu
      ```console
      root@<your_computer_name>:~$ edquota -t
      root@<your_computer_name>:~$ edquota -T <jméno uživatele>
      ```
        - prvním příkazem lze nastavit defaultní čas odkladu
        - druhým příkazem lze nastavit čas odkladu konkrétnímu uživateli
     - pokud je požadavek na aktivaci kvót při bootu OS (startu PC), je nutné ve ```fstab```u u parametrů za hodnotu ```defaults``` (oddělené čárkami) uvést i parametry ```usrquota``` a ```grpquota```
     - kvóty lze definovat i pro skupiny
   
      
