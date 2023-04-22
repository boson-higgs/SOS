# Skripty, programy a příkazový řádek v linuxu

- Proměnná PATH
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

16:00
        
