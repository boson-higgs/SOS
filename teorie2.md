# Skripty, programy a příkazový řádek v linuxu

- ## IDE:
    - instalace MC commanderu a editoru:
        ```console
        root@<your_computer_name>:~$ apt install mc
        ```
    - tento příkaz nainstaluje Midnight Commander (MC), jehož součástí je také textový editor  ```mcedit```
        ```console
        root@<your_computer_name>:~$ export EDITOR=mcedit
        ```
    - tento příkaz nastaví textový editor MC ```mcedit``` jako defaultní textový editor systému 

- ## IDE:
    - je vhodné si doinstalovat manuálové stránky ```man```
      ```console
      root@<your_computer_name>:~$ apt install man
      ```
       

- ## Proměnná PATH
    - obsahuje všechny cesty, ve kterých příkazový řadek hledá příslušný program (příkaz)
    - výpis proměnné ```PATH```:

        ```console
        root@<your_computer_name>:~$ echo $PATH
        ```
        - vypíše všechny cesty, ve kterých příkazový řádek hledá daný program (příkaz)
        - ve výpisu jsou cesty oddělené dvojtečkami
    - pokud daný příkaz není v cestách nalzen, vypíše se, že daný příkaz neexistuje
    - v takovém případě je nutné jedna z variant:¨
        - zadat příkaz absolutní cestou:
            - pro zjištění absolutní cesty určitého příkazu lze použít příkaz ```whereis```:

            ```console
            root@<your_computer_name>:~$ whereis blkid
            ```
            - vypíše absolutní cestu příkazu ```blkid```

            - zadání příkazu absolutní cestou:
            ```console
            root@<your_computer_name>:~$ /sbin/blkid
            ```
            - tento příkaz spustí program ```bklid``` ve složce ```/sbin```

        - přidat cestou do proměnné ```PATH```:
            ```console
                root@<your_computer_name>:~$ export PATH=$PATH:/sbin
            ```
            - tento příkaz **přiřadí** do proměnné ```PATH``` proměnnou ```PATH``` + složku ```/sbin``` (pro přidání této složky do proměnné ```PATH``` i opětovném zapnutí PC je nutné přidat výše uvedený příkaz do souboru ```.bashrc```)

- ## Standartní vstup, výstup a chybový výstup
    - základem všeho jsou příkazy v příkazovém řádku: [zdroj-1](https://www.digitalocean.com/community/tutorials/linux-commands), [zdroj-2](https://www.hostinger.com/tutorials/linux-commands)
    - přesměrování standartního výstupu:
        ```console
        root@<your_computer_name>:~$ cat file1 > file2
        ```
        - tento příkaz přesměruje výpis (standartní výstup příkazu ```cat file1```) obsahu souboru ```file1``` do souboru ```file2``` tím, že jím přepíše jeho obsah
        
        ```console
        root@<your_computer_name>:~$ cat file1 >> file2
        ```
        - tento příkaz přesměruje výpis obsahu souboru ```file1``` do souboru ```file2``` tím, že jej přípíše na konec souboru

        ```console
        root@<your_computer_name>:~$ cat file1 | cut -d ' ' -f 2
        ```
        - tento příkaz přesměruje standartní výstup přílazu ```cat file1``` (výpis obsahu souboru ```file1```) na standartní vstup programu ```cut```

    - přesměrování standartního chybového výstupu:
        ```console
        root@<your_computer_name>:~$ mkdir /test 2> file
        ```
        - tento příkaz přesměruje výpis chybového hlášení tohot příkazu (pokud nastane chyba) do souboru ```file```

        ```console
        root@<your_computer_name>:~$ mkdir /test 2> /dev/null
        ```
        - tento příkaz způsobí, že se nebudou vypisovat žádná chybová hlášení daného spuštění programu
        - ```/dev/null``` je jakási černá díra - cokoli se do ní pošle, je ztraceno

        ```console
        root@<your_computer_name>:~$ mkdir /test &> /dev/null
        ```
        - tento příkaz pošle standartní výstup i standartní chybový výstup do ```/dev/null```

    - návratové kódy funkcí/příkazů/programů:
        - návratové kódy informují o konečném stavu programu (proběhl v pořádku/skončil chybou)
        - s návratovými kódy lze pracovat v konzoli:
            ```console
            root@<your_computer_name>:~$ mkdir /tmp/test && echo OK || echo KO
            ```
            - pokud příkaz proběhne v pořáadku vykoná se příkaz ```echo OK```, pokud příkaz skončí chybou provede se příkaz ```echo KO```

- ## Bash
    - programování v bashi - [zdroj-1](https://www.root.cz/clanky/programovani-v-bash-shellu/), [zdroj-2](https://www.cyberciti.biz/faq/bash-for-loop/)
