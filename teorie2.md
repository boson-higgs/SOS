# Skripty, programy a příkazový řádek v linuxu

- Proměnná PATH
    - obsahuje všechny cesty, ve kterých příkazový řadek hledá příslušný program (příkaz)
    - výpis proměnné PATH:

        ```console
        root@<your_computer_name>:~$ echo $PATH
        ```
        - výpíše všechny cesty, ve kterých hledá daný program (příkaz)
        - cesty jsou oddělené dvojtečkami
    - pokud daný příkaz ve svých cestách nenajde, je nutné zadat příkaz absolutní cestou:
        - pro zjištění absolutní cesty určitého příkazu lze použít příkaz "whereis":
            ```console
            root@<your_computer_name>:~$ whereis <your_command>
            ```
            - vypíše absolutní cestu příkazu

            ```console
            root@<your_computer_name>:~$ whereis blkid
            ```
            - vypíše absolutní cestu příkazu blkid
    