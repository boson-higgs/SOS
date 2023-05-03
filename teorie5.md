# Uživatelská práva, diskové kvóty
  - ## Uživatelé
    - ### „Klasický“ uživatel
      - uživatel, který je určen zpravidla fyzické osobě
    - ### Systémový uživatel
      - uživatel, který je určen službě (pod kterým se nějaká služba spouští)
      - služby lze spouštět pod rootem (což není dobrý nápad - služba by měla všechna prává v systému)
      - systémovému uživateli lze udělit určité restrikce v rámci práv a pod ním (s jeho právy) se bude služba spouštět (služba by měla mít pouze ta práva, která potřebuje ke své činnosti)
