Upgrade packages
=========

Обновляет установленные пакеты

Переменные
--------------
Дефолтные настройки для фулл апгрейда пакетов
```
# Debian\Ubuntu
upgrade_packages_update_cache: true       # true | false
upgrade_packages_mode: yes                # yes | dist | full | no
upgrade_packages_cache_valid_time: 3600

# RedHat
upgrade_packages_update_only: true        # true | false
```

Использование в плейбуке
----------------
```
- hosts: all
  roles:
    - upgrade_packages
```