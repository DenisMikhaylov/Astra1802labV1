
# Лабораторная работа №9: Настройка сетевых интерфейсов в Astra Linux

**Дисциплина:** Информационные технологии  
**Цель работы:** Освоить базовые навыки настройки сетевых интерфейсов в Astra Linux с использованием двух подходов: классического файла `/etc/network/interfaces` и современного инструмента `NetworkManager`/`nmcli`.  
**Стенд:** Виртуальная машина Astra Linux.  
**Учетные данные:**  
- Пользователь: `sa`  
- Пароль: `12345678`  
- Имя машины: `Astra`

---

## Теоретическая справка

### 1. Способы управления сетью в Astra Linux

Современные версии Astra Linux поддерживают три способа настройки сети:

| Способ | Описание | Применение |
|--------|----------|------------|
| **NetworkManager** | Служба для управления сетями с графическим интерфейсом и CLI-инструментом `nmcli` | Стандарт для настольных ПС; автоматически управляет внешними интерфейсами |
| **networking / resolvconf** | Классический способ через файл `/etc/network/interfaces` | Для серверных конфигураций и сценариев автоматизации |
| **systemd-networkd** | Современный способ от systemd | По умолчанию заблокирован в Astra Linux |

**Важно:** При стандартной установке Astra Linux служба `NetworkManager` автоматически запускается и получает управление всеми внешними сетевыми интерфейсами. Для работы с `NetworkManager` пользователю необходимо быть в группе `netdev`.

### 2. Конфигурационный файл /etc/network/interfaces

Файл `interfaces` используется для настройки сетевых интерфейсов в классическом подходе.

**Основные директивы:**

```bash
# Интерфейс локальной петли (всегда присутствует)
auto lo
iface lo inet loopback

# Интерфейс с DHCP
allow-hotplug enp0s3
iface enp0s3 inet dhcp

# Интерфейс со статическим адресом
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 192.168.1.1
    dns-domain example.com
```

**Ключевые опции:**
- `auto` – автоматический запуск интерфейса при загрузке системы
- `allow-hotplug` – запуск при подключении сетевого кабеля
- `iface` – определение конфигурации интерфейса
- `address`, `netmask`, `gateway` – настройки IP-адресации
- `dns-nameservers`, `dns-domain` – настройки DNS

### 3. NetworkManager и nmcli

NetworkManager является фактическим стандартом управления сетями в Astra Linux, обеспечивая полную функциональность.

**Основные команды nmcli:**

| Команда | Описание |
|---------|----------|
| `nmcli device status` | Показать состояние устройств |
| `nmcli device show enp0s3` | Показать детальную информацию об устройстве |
| `nmcli connection show` | Показать все соединения |
| `nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.100/24` | Изменить IP-адрес соединения |
| `nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1` | Изменить шлюз |
| `nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8 1.1.1.1"` | Изменить DNS |
| `nmcli con mod "Wired connection 1" ipv4.method manual` | Переключить на статический IP |
| `nmcli con down "Wired connection 1"` | Отключить соединение |
| `nmcli con up "Wired connection 1"` | Включить соединение |
| `nmcli con reload` | Перезагрузить конфигурацию |

**Важно:** Во избежание конфликтов служба NetworkManager не работает с интерфейсами, перечисленными в `/etc/network/interfaces`. По умолчанию в этом файле присутствует только интерфейс `lo`.

---

## Задания

### Задание 1. Изучение текущей сетевой конфигурации
1. Определите имя сетевого интерфейса с помощью команды `ip a`. (В виртуальных машинах это обычно `enp0s3`).  
2. С помощью `nmcli device status` определите состояние сетевых устройств.  
3. Выведите список всех активных соединений: `nmcli connection show`.  
4. С помощью `nmcli device show enp0s3` выведите детальную информацию об интерфейсе.  
5. Просмотрите содержимое файла `/etc/network/interfaces`.  
6. Просмотрите файлы конфигурации NetworkManager в `/etc/NetworkManager/`.

---

### Задание 2. Настройка сети через /etc/network/interfaces (статический IP)
**Внимание:** Перед выполнением задания убедитесь, что интерфейс удален из управления NetworkManager.

1. Создайте резервную копию файла `/etc/network/interfaces`:  
   `sudo cp /etc/network/interfaces /etc/network/interfaces.backup`  
2. Отредактируйте файл `/etc/network/interfaces` с помощью `nano` и добавьте настройки для статического IP (используйте свою подсеть):  
   ```
   auto enp0s3
   iface enp0s3 inet static
       address 192.168.1.100
       netmask 255.255.255.0
       gateway 192.168.1.1
       dns-nameservers 8.8.8.8
   ```
3. Примените настройки: `sudo systemctl restart networking`.  
4. Проверьте, что адрес применился: `ip a show enp0s3`.  
5. Верните конфигурацию на DHCP, отредактировав файл:  
   ```
   auto enp0s3
   iface enp0s3 inet dhcp
   ```
6. Перезапустите сеть и проверьте получение адреса.

---

### Задание 3. Настройка сети через NetworkManager (статический IP)
1. Убедитесь, что файл `/etc/network/interfaces` содержит только интерфейс `lo`.  
2. Просмотрите существующие соединения: `nmcli con show`.  
3. Найдите имя вашего соединения (обычно "Wired connection 1" или "Проводное соединение 1").  
4. Настройте статический IP для соединения:  
   ```bash
   sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.100/24
   sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
   sudo nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8 1.1.1.1"
   sudo nmcli con mod "Wired connection 1" ipv4.method manual
   ```
5. Перезапустите соединение:  
   `sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"`  
6. Проверьте настройки: `nmcli con show "Wired connection 1" | grep ipv4`.  
7. Проверьте, что IP-адрес применился: `ip a show enp0s3`.  
8. Верните настройки на DHCP:  
   ```bash
   sudo nmcli con mod "Wired connection 1" ipv4.method auto
   sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
   ```

---

### Задание 4. Настройка DNS через NetworkManager
1. Добавьте дополнительные DNS-серверы к вашему соединению:  
   `sudo nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8 77.88.8.8"`  
2. Перезапустите соединение:  
   `sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"`  
3. Проверьте настройки DNS: `nmcli con show "Wired connection 1" | grep dns`.  
4. Проверьте резолвинг имен: `nslookup ya.ru` или `host ya.ru`.

---

### Задание 5. Отключение IPv6 через NetworkManager
1. Отключите IPv6 для соединения:  
   ```bash
   sudo nmcli con mod "Wired connection 1" ipv6.method ignore
   sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
   ```  
2. Проверьте, что IPv6-адрес не назначен интерфейсу: `ip a show enp0s3`.  
3. Верните IPv6 обратно:  
   ```bash
   sudo nmcli con mod "Wired connection 1" ipv6.method auto
   sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
   ```  

---

### Задание 6. Создание нового соединения через nmcli
1. Создайте новое соединение с именем "static_connection" для интерфейса `enp0s3`:  
   ```bash
   sudo nmcli con add type ethernet ifname enp0s3 con-name static_connection \
       ipv4.addresses 192.168.1.200/24 ipv4.gateway 192.168.1.1 \
       ipv4.dns "8.8.8.8" ipv4.method manual
   ```  
2. Проверьте создание соединения: `nmcli con show`.  
3. Активируйте новое соединение:  
   `sudo nmcli con up static_connection`  
4. Проверьте, что IP-адрес изменился: `ip a show enp0s3`.  
5. Переключитесь обратно на "Wired connection 1":  
   `sudo nmcli con up "Wired connection 1"`  
6. Удалите созданное соединение:  
   `sudo nmcli con delete static_connection`

---

### Задание 7. Настройка нескольких адресов на интерфейсе
**Вариант А (через interfaces):**
1. Добавьте в `/etc/network/interfaces` второй IP-адрес:  
   ```
   auto enp0s3:0
   iface enp0s3:0 inet static
       address 10.10.10.10
       netmask 255.255.255.0
   ```
2. Примените настройки: `sudo systemctl restart networking`.  
3. Проверьте добавление адреса: `ip a show enp0s3`.

**Вариант Б (через nmcli):**
1. Добавьте дополнительный IP-адрес к существующему соединению:  
   `sudo nmcli con mod "Wired connection 1" +ipv4.addresses 10.10.10.10/24`  
2. Перезапустите соединение:  
   `sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"`  
3. Проверьте добавление адреса: `ip a show enp0s3`.

---

### Задание 8. Настройка MTU и других параметров
1. Измените MTU интерфейса через `nmcli`:  
   `sudo nmcli con mod "Wired connection 1" 802-3-ethernet.mtu 1400`  
2. Перезапустите соединение.  
3. Проверьте MTU: `ip link show enp0s3`.  
4. Верните MTU обратно (обычно 1500):  
   `sudo nmcli con mod "Wired connection 1" 802-3-ethernet.mtu 1500`  
5. Перезапустите соединение.

---

### Задание 9. Диагностика и устранение неполадок
1. Проверьте статус NetworkManager: `systemctl status NetworkManager`.  
2. Проверьте права текущего пользователя: `nmcli general permissions`.  
3. Примените изменения конфигурации NetworkManager без перезапуска службы: `sudo nmcli con reload`.  
4. Проверьте состояние соединения: `nmcli con show "Wired connection 1"`.  
5. Проверьте логи NetworkManager: `sudo journalctl -u NetworkManager -f` (запустите и измените настройки в другом терминале).

---

## Решение (эталон выполнения)

```bash
# ============ ЗАДАНИЕ 1 ============
# Определение интерфейса
ip a
# Ожидаемый вывод: интерфейс enp0s3

# Список устройств
nmcli device status

# Список соединений
nmcli connection show

# Детальная информация об интерфейсе
nmcli device show enp0s3

# Просмотр файлов конфигурации
cat /etc/network/interfaces
ls -la /etc/NetworkManager/

# ============ ЗАДАНИЕ 2 ============
# Резервная копия
sudo cp /etc/network/interfaces /etc/network/interfaces.backup

# Редактирование файла
sudo nano /etc/network/interfaces
# Добавить:
# auto enp0s3
# iface enp0s3 inet static
#     address 192.168.1.100
#     netmask 255.255.255.0
#     gateway 192.168.1.1
#     dns-nameservers 8.8.8.8

# Применение
sudo systemctl restart networking

# Проверка
ip a show enp0s3

# Возврат на DHCP
sudo nano /etc/network/interfaces
# Изменить на:
# auto enp0s3
# iface enp0s3 inet dhcp

sudo systemctl restart networking
ip a show enp0s3

# ============ ЗАДАНИЕ 3 ============
# Проверка наличия интерфейса в /etc/network/interfaces (только lo)
cat /etc/network/interfaces

# Определение имени соединения
nmcli con show

# Настройка статического IP через nmcli
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.100/24
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8 1.1.1.1"
sudo nmcli con mod "Wired connection 1" ipv4.method manual

# Перезапуск
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"

# Проверка
nmcli con show "Wired connection 1" | grep ipv4
ip a show enp0s3

# Возврат на DHCP
sudo nmcli con mod "Wired connection 1" ipv4.method auto
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"

# ============ ЗАДАНИЕ 4 ============
sudo nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8 77.88.8.8"
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
nmcli con show "Wired connection 1" | grep dns
nslookup ya.ru
# или
host ya.ru

# ============ ЗАДАНИЕ 5 ============
sudo nmcli con mod "Wired connection 1" ipv6.method ignore
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
ip a show enp0s3

# Возврат
sudo nmcli con mod "Wired connection 1" ipv6.method auto
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"

# ============ ЗАДАНИЕ 6 ============
sudo nmcli con add type ethernet ifname enp0s3 con-name static_connection \
    ipv4.addresses 192.168.1.200/24 ipv4.gateway 192.168.1.1 \
    ipv4.dns "8.8.8.8" ipv4.method manual

nmcli con show
sudo nmcli con up static_connection
ip a show enp0s3
sudo nmcli con up "Wired connection 1"
sudo nmcli con delete static_connection

# ============ ЗАДАНИЕ 7 ============
# Вариант А (через interfaces)
sudo nano /etc/network/interfaces
# Добавить:
# auto enp0s3:0
# iface enp0s3:0 inet static
#     address 10.10.10.10
#     netmask 255.255.255.0
sudo systemctl restart networking
ip a show enp0s3

# Вариант Б (через nmcli)
sudo nmcli con mod "Wired connection 1" +ipv4.addresses 10.10.10.10/24
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
ip a show enp0s3

# ============ ЗАДАНИЕ 8 ============
sudo nmcli con mod "Wired connection 1" 802-3-ethernet.mtu 1400
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
ip link show enp0s3

# Возврат MTU
sudo nmcli con mod "Wired connection 1" 802-3-ethernet.mtu 1500
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"

# ============ ЗАДАНИЕ 9 ============
systemctl status NetworkManager
nmcli general permissions
sudo nmcli con reload
nmcli con show "Wired connection 1"

# Просмотр логов
sudo journalctl -u NetworkManager -f
# В другом терминале выполните изменение настроек
```

---

## Дополнительные упражнения (для самостоятельной работы)

1. Используйте `nmtui` для настройки сети в псевдографическом интерфейсе.  
2. Изучите файлы конфигурации NetworkManager в `/etc/NetworkManager/conf.d/`.  
3. Настройте статический маршрут через `nmcli` с помощью `+ipv4.routes`.  
4. Сравните производительность `networking` и `NetworkManager` на виртуальной машине.  
5. Изучите настройки DNS в `/etc/resolv.conf` и его связь с NetworkManager.

