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
          - ```<shell>``` interpret příkazů, který daný uživatel využívá
      - pro „klasické“ uživatele zpravidla lze nalézt jejich domovské adresáře v adresáři ```/home```
      ```console
      root@<your_computer_name>:~$ adduser franta
      ```
      - tento příkaz vytvoří uživatele franta
