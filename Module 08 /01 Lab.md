# Лабораторная работа №6: Управление пользователями и группами в Linux

**Дисциплина:** Информационные технологии  
**Цель работы:** Освоить управление учетными записями пользователей и группами в Linux: создание, модификация, удаление пользователей и групп, а также настройка политик сложности паролей с использованием PAM-модулей.  
**Стенд:** Виртуальная машина Astra Linux.  
**Учетные данные:**  
- Пользователь: `sa`  
- Пароль: `12345678`  
- Имя машины: `Astra`

---

## Теоретическая справка

### 1. Управление пользователями

| Команда | Описание | Пример |
|---------|----------|--------|
| `useradd` | Создание нового пользователя (низкоуровневая) | `sudo useradd -m -s /bin/bash user1` |
| `adduser` | Создание пользователя (интерактивная, Debian/Ubuntu) | `sudo adduser user1` |
| `usermod` | Изменение параметров пользователя | `sudo usermod -c "John Doe" user1` |
| `userdel` | Удаление пользователя | `sudo userdel -r user1` |
| `passwd` | Установка/изменение пароля | `sudo passwd user1` |
| `chage` | Управление сроком действия пароля | `sudo chage -M 90 user1` |
| `id` | Просмотр UID/GID пользователя | `id user1` |

**Ключевые опции useradd:**
- `-m` — создать домашнюю директорию
- `-s` — указать оболочку (shell)
- `-c` — комментарий (полное имя)
- `-u` — указать UID
- `-g` — указать основную группу
- `-G` — дополнительные группы
- `-e` — дата истечения аккаунта

**Примеры:**
```bash
# Создание пользователя с домашней директорией и оболочкой bash
sudo useradd -m -s /bin/bash developer

# Создание с указанием UID и группы
sudo useradd -u 1501 -g developers -m tester

# Изменение домашней директории
sudo usermod -d /new/home -m user1

# Блокировка/разблокировка учетной записи
sudo usermod -L user1
sudo usermod -U user1
```

### 2. Управление группами

| Команда | Описание | Пример |
|---------|----------|--------|
| `groupadd` | Создание группы | `sudo groupadd developers` |
| `groupmod` | Изменение группы | `sudo groupmod -n devs developers` |
| `groupdel` | Удаление группы | `sudo groupdel oldgroup` |
| `usermod -aG` | Добавление пользователя в группу | `sudo usermod -aG developers user1` |
| `gpasswd -d` | Удаление пользователя из группы | `sudo gpasswd -d user1 developers` |

**Важно:** При добавлении пользователя в группу обязательно используйте опцию `-a` (append), иначе пользователь будет удален из всех дополнительных групп .

**Примеры:**
```bash
# Создание группы
sudo groupadd project_team

# Переименование группы
sudo groupmod -n team project_team

# Добавление пользователя в группу
sudo usermod -aG team developer

# Проверка членства
groups developer
```

### 3. Настройка сложности паролей через PAM

PAM (Pluggable Authentication Modules) — модульная система аутентификации. Для проверки сложности паролей используются модули `pam_cracklib` или `pam_pwquality` .

#### Установка PAM-модуля

В Astra Linux (Debian-based) установка выполняется командой :
```bash
sudo apt-get install libpam-pwquality
```

#### Конфигурационные файлы

| Дистрибутив | Файл |
|-------------|------|
| Red Hat/CentOS/Fedora/Astra | `/etc/pam.d/system-auth` |
| Debian/Ubuntu | `/etc/pam.d/common-password` |

#### Параметры проверки сложности 

| Параметр | Описание | Пример |
|----------|----------|--------|
| `minlen=N` | Минимальная длина пароля | `minlen=8` |
| `dcredit=-N` | Минимальное количество цифр | `dcredit=-1` |
| `ucredit=-N` | Минимальное количество заглавных букв | `ucredit=-1` |
| `lcredit=-N` | Минимальное количество строчных букв | `lcredit=-1` |
| `ocredit=-N` | Минимальное количество спецсимволов | `ocredit=-1` |
| `difok=N` | Минимальное количество изменений от старого пароля | `difok=3` |
| `retry=N` | Количество попыток ввода пароля | `retry=3` |
| `enforce_for_root` | Применять политику для root | `enforce_for_root` |

#### Настройка для всех пользователей (включая root) 

Для применения политик ко всем пользователям, включая root, необходимо использовать модуль `pam_pwquality.so` с опцией `enforce_for_root`.

Пример строки в `/etc/pam.d/common-password`:
```
password requisite pam_pwquality.so retry=3 minlen=8 dcredit=-1 ucredit=-1 lcredit=-1 ocredit=-1 difok=3 enforce_for_root
```

---

## Задания

### Задание 1. Создание пользователей
1. Создайте пользователя `developer` с домашней директорией и оболочкой `/bin/bash`.  
2. Создайте пользователя `tester` с комментарием "QA Engineer" и UID 1501.  
3. Установите пароль для пользователя `developer` — `DevPass123`.  
4. Установите пароль для пользователя `tester` — `TestPass456`.  
5. Создайте пользователя `admin` без домашней директории (только для демонстрации).  
6. Проверьте, какие пользователи были созданы (просмотр `/etc/passwd`).

---

### Задание 2. Изменение параметров пользователей
1. Измените комментарий для пользователя `developer` на "Software Developer".  
2. Измените оболочку пользователя `tester` на `/bin/sh`.  
3. Заблокируйте учетную запись пользователя `admin`.  
4. Проверьте, что аккаунт заблокирован (просмотр `/etc/shadow`).  
5. Разблокируйте пользователя `admin`.  
6. Установите для пользователя `developer` срок действия пароля 30 дней.  
7. Принудительно истечь пароль пользователя `tester` при следующем входе.

---

### Задание 3. Управление группами
1. Создайте группу `developers`.  
2. Создайте группу `testers`.  
3. Добавьте пользователя `developer` в группу `developers`.  
4. Добавьте пользователя `tester` в группы `developers` и `testers`.  
5. Проверьте группы пользователей с помощью команды `groups`.  
6. Переименуйте группу `testers` в `qa_team`.  
7. Удалите пользователя `tester` из группы `developers`.  
8. Удалите группу `qa_team`.

---

### Задание 4. Настройка политики сложности паролей (PAM)
1. Установите пакет `libpam-pwquality` (если не установлен).  
2. Сделайте резервную копию файла `/etc/pam.d/common-password`.  
3. Отредактируйте файл `/etc/pam.d/common-password`, установите следующие требования:
   - Минимальная длина пароля — 10 символов
   - Минимум 1 цифра
   - Минимум 1 заглавная буква
   - Минимум 1 спецсимвол
   - Не менее 3 символов отличаться от старого пароля
   - 3 попытки ввода
4. Проверьте настройки, попробовав изменить пароль пользователя `developer` на простой (например, `123456`).  
5. Измените пароль на соответствующий требованиям (например, `DevPass_2024`).  
6. Добавьте опцию `enforce_for_root`, чтобы политика применялась и к root .  
7. Проверьте, что root также подчиняется политике.

---

### Задание 5. Комплексное задание
Выполните последовательность действий:

1. Создайте группу `project_alpha`.  
2. Создайте пользователей:
   - `alice` с оболочкой `/bin/bash`
   - `bob` с оболочкой `/bin/bash`
   - `charlie` с оболочкой `/bin/sh`
3. Добавьте всех пользователей в группу `project_alpha`.  
4. Установите пароли: `Alice123!`, `Bob456@`, `Charlie789#`.  
5. Измените комментарий для `alice` на "Project Lead".  
6. Заблокируйте пользователя `charlie`.  
7. Создайте отчет о пользователях и их группах в файл `users_report.txt` с помощью `id`.  
8. Удалите пользователя `charlie` с удалением домашней директории.  
9. Удалите группу `project_alpha`.

---

### Задание 6. Работа с файлами конфигурации
1. Просмотрите содержимое файла `/etc/passwd` и найдите созданных пользователей.  
2. Просмотрите содержимое файла `/etc/shadow` (требуется sudo) и найдите хеши паролей.  
3. Просмотрите содержимое файла `/etc/group` и найдите созданные группы.  
4. Используя `grep`, выведите из `/etc/passwd` только строки с пользователями `developer` и `tester`.  
5. Найдите всех пользователей, у которых оболочка `/bin/bash`.  
6. Сохраните список всех групп, в которые входит пользователь `sa`.

---

### Задание 7. Дополнительные опции управления пользователями
1. Создайте пользователя `temp_user` с датой истечения аккаунта через 7 дней.  
2. Создайте пользователя `expired_user` с уже истекшим сроком действия (дата в прошлом).  
3. Используйте `chage -l` для просмотра информации о сроке действия пароля для `developer`.  
4. Установите для `developer` предупреждение за 5 дней до истечения пароля.  
5. Создайте пользователя с UID 2000 и GID 2000.

---

## Решение (эталон выполнения)

```bash
# ============ ПРОВЕРКА ПРАВ ============
# Все команды управления пользователями требуют прав root
sudo -i

# ============ ЗАДАНИЕ 1 ============
# Создание пользователей
useradd -m -s /bin/bash developer
useradd -m -c "QA Engineer" -u 1501 tester
useradd admin

# Установка паролей
echo "developer:DevPass123" | chpasswd
echo "tester:TestPass456" | chpasswd

# Проверка
tail -5 /etc/passwd

# ============ ЗАДАНИЕ 2 ============
# Изменение комментария
usermod -c "Software Developer" developer

# Изменение оболочки
usermod -s /bin/sh tester

# Блокировка/разблокировка
usermod -L admin
grep admin /etc/shadow
usermod -U admin

# Установка срока действия пароля
chage -M 30 developer
passwd -e tester

# ============ ЗАДАНИЕ 3 ============
# Создание групп
groupadd developers
groupadd testers

# Добавление пользователей в группы
usermod -aG developers developer
usermod -aG developers,tester tester

# Проверка
groups developer
groups tester

# Переименование группы
groupmod -n qa_team testers

# Удаление пользователя из группы
gpasswd -d tester developers

# Удаление группы
groupdel qa_team

# ============ ЗАДАНИЕ 4 ============
# Установка PAM-модуля
apt-get update
apt-get install -y libpam-pwquality

# Резервная копия
cp /etc/pam.d/common-password /etc/pam.d/common-password.backup

# Редактирование файла (используйте nano или sed)
# Добавить строку:
# password requisite pam_pwquality.so retry=3 minlen=10 dcredit=-1 ucredit=-1 ocredit=-1 difok=3

# Или с помощью sed (для автоматизации)
# sed -i 's/password.*pam_unix.so/password requisite pam_pwquality.so retry=3 minlen=10 dcredit=-1 ucredit=-1 ocredit=-1 difok=3\npassword [success=1 default=ignore] pam_unix.so/g' /etc/pam.d/common-password

# Проверка: попытка установить слабый пароль
echo "developer:123456" | chpasswd  # Должна быть ошибка

# Установка сильного пароля
echo "developer:DevPass_2024" | chpasswd

# Добавление enforce_for_root
# Отредактируйте файл, добавив enforce_for_root в конец строки

# ============ ЗАДАНИЕ 5 ============
groupadd project_alpha

useradd -m -s /bin/bash alice
useradd -m -s /bin/bash bob
useradd -m -s /bin/sh charlie

usermod -aG project_alpha alice
usermod -aG project_alpha bob
usermod -aG project_alpha charlie

echo "alice:Alice123!" | chpasswd
echo "bob:Bob456@" | chpasswd
echo "charlie:Charlie789#" | chpasswd

usermod -c "Project Lead" alice
usermod -L charlie

# Отчет
echo "=== User Report ===" > users_report.txt
id alice >> users_report.txt
id bob >> users_report.txt
id charlie >> users_report.txt

# Удаление
userdel -r charlie
groupdel project_alpha

# ============ ЗАДАНИЕ 6 ============
# Просмотр файлов
cat /etc/passwd
sudo cat /etc/shadow
cat /etc/group

# Поиск пользователей
grep -E "developer|tester" /etc/passwd
grep "/bin/bash" /etc/passwd

# Группы пользователя sa
groups sa

# ============ ЗАДАНИЕ 7 ============
# Пользователь с истечением через 7 дней
useradd -e $(date -d "+7 days" +%Y-%m-%d) temp_user

# Пользователь с истекшим сроком
useradd -e 2024-01-01 expired_user

# Информация о сроке действия
chage -l developer

# Настройка предупреждения
chage -W 5 developer

# Пользователь с указанным UID и GID
groupadd -g 2000 custom_group
useradd -u 2000 -g 2000 custom_user
```

---

## Дополнительные упражнения (для самостоятельной работы)

1. **Сценарии создания:** Напишите однострочник для создания 5 пользователей с именами user1-user5.  
2. **Массовое добавление:** Создайте группу и добавьте всех пользователей в эту группу одной командой.  
3. **Политика паролей:** Изучите файл `/etc/security/pwquality.conf` как альтернативный способ настройки сложности .  
4. **Сроки действия:** Настройте автоматическое истечение пароля через 90 дней для всех новых пользователей.  
5. **Права sudo:** Настройте права sudo для пользователя `developer` на выполнение только `apt` и `systemctl` .

