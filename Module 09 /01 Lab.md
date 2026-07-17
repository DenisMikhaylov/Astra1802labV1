# Лабораторная работа №7: Управление правами доступа к файлам в Linux

**Дисциплина:** Информационные технологии  
**Цель работы:** Освоить управление правами доступа к файлам и каталогам в Linux: изменение владельца, группы и прав доступа (chown, chgrp, chmod), работа с расширенными списками доступа ACL (getfacl, setfacl), управление атрибутами файлов (lsattr, chattr).  
**Стенд:** Виртуальная машина Astra Linux.  
**Учетные данные:**  
- Пользователь: `sa`  
- Пароль: `12345678`  
- Имя машины: `Astra`

---

## Теоретическая справка

### 1. Права доступа в Linux

Каждый файл и каталог имеет три категории пользователей:
- **u** (user) — владелец файла
- **g** (group) — группа-владелец
- **o** (others) — остальные пользователи

И три типа прав для каждой категории:

| Символ | Право | Для файлов | Для каталогов |
|--------|-------|------------|---------------|
| `r` | Чтение (4) | Просмотр содержимого | Чтение списка файлов |
| `w` | Запись (2) | Изменение содержимого | Создание/удаление файлов |
| `x` | Исполнение (1) | Запуск программы | Доступ в каталог (cd) |

**Буквенный формат:** `rwxr-xr--`  
**Числовой формат:** `755` (rwxr-xr-x)

### 2. Команды управления правами

| Команда | Описание | Пример |
|---------|----------|--------|
| `chown` | Изменить владельца | `sudo chown user:group file` |
| `chgrp` | Изменить группу-владельца | `sudo chgrp developers file` |
| `chmod` | Изменить права доступа | `chmod 755 script.sh` |
| `getfacl` | Просмотреть ACL | `getfacl file` |
| `setfacl` | Установить ACL | `setfacl -m u:user:rwx file` |
| `lsattr` | Просмотреть атрибуты | `lsattr file` |
| `chattr` | Изменить атрибуты | `sudo chattr +i file` |

### 3. Расширенные списки доступа (ACL)

**Установка ACL:**
```bash
# Дать права пользователю
setfacl -m u:username:rwx file

# Дать права группе
setfacl -m g:groupname:rx file

# Удалить ACL для пользователя
setfacl -x u:username file

# Удалить все ACL
setfacl -b file

# Рекурсивно применить ACL
setfacl -R -m u:username:rwx directory/
```

**Маска ACL:** Ограничивает максимальные права для всех, кроме владельца.
```bash
# Установить маску
setfacl -m m:rwx file

# Просмотр маски
getfacl file
```

### 4. Атрибуты файлов (chattr)

| Атрибут | Описание | Пример |
|---------|----------|--------|
| `i` | Immutable — файл нельзя изменить, переименовать, удалить | `chattr +i file` |
| `a` | Append only — только добавление в конец | `chattr +a file` |
| `d` | No dump — не включать в дамп | `chattr +d file` |
| `s` | Secure deletion — при удалении перезаписывается | `chattr +s file` |
| `u` | Undeletable — восстановление после удаления | `chattr +u file` |

**Примеры:**
```bash
# Сделать файл неизменяемым
sudo chattr +i important.txt

# Сделать файл доступным только для добавления
sudo chattr +a log.txt

# Удалить атрибут
sudo chattr -i important.txt

# Просмотр атрибутов
lsattr important.txt
```

---

## Задания

### Задание 1. Создание тестового окружения
1. Создайте каталог `lab_permissions` в домашней директории.  
2. Внутри создайте подкаталоги: `public`, `private`, `shared`.  
3. Создайте файлы:
   - `lab_permissions/file1.txt` с содержимым "Public file"
   - `lab_permissions/file2.txt` с содержимым "Private file"
   - `lab_permissions/script.sh` с содержимым "#!/bin/bash\necho 'Hello'"
   - `lab_permissions/public/file3.txt` с содержимым "Shared file"
4. Создайте пользователей для тестирования (если не созданы):
   - `alice` (пароль Alice123!)
   - `bob` (пароль Bob456@)
5. Создайте группы:
   - `developers`
   - `managers`
6. Добавьте `alice` в группу `developers`.  
7. Добавьте `bob` в группу `managers`.

---

### Задание 2. Управление владельцем и группой (chown, chgrp)
1. Установите владельцем файла `file1.txt` пользователя `alice`.  
2. Установите группу-владельца файла `file2.txt` в `developers`.  
3. Установите владельца и группу для `file3.txt` как `alice:developers`.  
4. Рекурсивно измените владельца всех файлов в каталоге `public` на `bob`.  
5. Рекурсивно измените группу-владельца каталога `shared` и его содержимого на `managers`.  
6. Измените владельца файла `script.sh` на `alice`, а группу на `developers`.

---

### Задание 3. Управление правами доступа (chmod)

#### 3.1. Числовой режим
1. Установите права `755` для файла `script.sh`.  
2. Установите права `644` для файла `file1.txt`.  
3. Установите права `750` для каталога `private`.  
4. Установите права `775` для каталога `public`.

#### 3.2. Буквенный режим
1. Добавьте право на выполнение для группы к файлу `file1.txt`.  
2. Уберите право на запись для всех, кроме владельца, у файла `file2.txt`.  
3. Добавьте право на запись для группы к каталогу `shared`.  
4. Установите права `rwxr-x---` для каталога `private` (буквенно).  
5. Сделайте файл `script.sh` исполняемым для всех.

---

### Задание 4. Работа с ACL (setfacl, getfacl)
1. Просмотрите текущие ACL для файла `file1.txt`.  
2. Дайте пользователю `bob` право на чтение и запись в файл `file1.txt`.  
3. Дайте группе `managers` право на чтение файла `file2.txt`.  
4. Дайте пользователю `bob` полный доступ (rwx) к каталогу `private`.  
5. Установите рекурсивно ACL для каталога `shared`, чтобы пользователь `alice` имел полный доступ ко всем файлам внутри.  
6. Просмотрите ACL для каталога `shared`.  
7. Удалите ACL для пользователя `bob` из файла `file1.txt`.  
8. Установите маску ACL (mask) для файла `file2.txt` на `rw-`.  
9. Удалите все ACL для файла `file2.txt`.

---

### Задание 5. Работа с атрибутами (chattr, lsattr)
1. Просмотрите атрибуты файла `file1.txt`.  
2. Сделайте файл `file1.txt` неизменяемым (immutable, атрибут `i`).  
3. Попробуйте изменить содержимое файла `file1.txt` (должна быть ошибка).  
4. Попробуйте удалить файл `file1.txt` (должна быть ошибка).  
5. Удалите атрибут `i` у файла `file1.txt`.  
6. Сделайте файл `file2.txt` доступным только для добавления (append only, атрибут `a`).  
7. Попробуйте перезаписать файл `file2.txt` (должна быть ошибка).  
8. Попробуйте добавить данные в конец файла (должно работать).  
9. Сделайте файл `script.sh` защищенным от случайного удаления (атрибут `i`).  
10. Просмотрите все атрибуты в каталоге `lab_permissions`.

---

### Задание 6. Комплексный сценарий
Выполните последовательность действий:

1. Создайте каталог `project` в `/tmp` (или в домашней директории).  
2. Установите владельцем `alice`, группой `developers`.  
3. Установите права `770` на каталог `project`.  
4. Создайте внутри файл `README.md` с правами `644`, владельцем `alice`, группой `developers`.  
5. Добавьте ACL для пользователя `bob`: право на чтение и запись в каталог `project` (рекурсивно).  
6. Сделайте файл `README.md` неизменяемым (атрибут `i`).  
7. Просмотрите все права и атрибуты файлов в каталоге `project`.  
8. Сохраните отчет в файл `permissions_report.txt` с использованием `ls -la`, `getfacl` и `lsattr`.

---

### Задание 7. Специальные биты (Sticky, SGID, SUID)
1. Создайте каталог `tmp_shared` в `/tmp`.  
2. Установите бит **Sticky** на каталог `tmp_shared` (права `1777`).  
3. Объясните, что означает sticky-бит и как он работает.  
4. Создайте файл `suid_test` и установите ему **SUID** бит (права `4755`).  
5. Объясните, что такое SUID и когда применяется.  
6. Создайте каталог `sgid_test` и установите на него **SGID** бит (права `2755`).  
7. Объясните, что такое SGID и как он влияет на создаваемые файлы.  
8. Просмотрите права с помощью `ls -l`.

---

### Задание 8. Диагностика и отчетность
1. Используя `find`, найдите все файлы в домашней директории с SUID битом.  
2. Используя `find`, найдите все файлы с атрибутом `i`.  
3. Создайте скрипт (однострочник) для вывода подробной информации о правах всех файлов в каталоге `lab_permissions`, включая ACL и атрибуты.  
4. Создайте полный отчет в файл `full_report.txt`, содержащий:
   - Владельцев и группы всех файлов
   - Права доступа (в числовом и буквенном формате)
   - ACL для всех файлов (где есть)
   - Атрибуты всех файлов

---

## Решение (эталон выполнения)

```bash
# ============ ЗАДАНИЕ 1 ============
cd ~
mkdir -p lab_permissions/{public,private,shared}

echo "Public file" > lab_permissions/file1.txt
echo "Private file" > lab_permissions/file2.txt
echo '#!/bin/bash' > lab_permissions/script.sh
echo "echo 'Hello'" >> lab_permissions/script.sh
echo "Shared file" > lab_permissions/public/file3.txt

# Создание пользователей и групп (если не существуют)
sudo groupadd developers 2>/dev/null
sudo groupadd managers 2>/dev/null
sudo useradd -m -s /bin/bash alice 2>/dev/null
sudo useradd -m -s /bin/bash bob 2>/dev/null

# Установка паролей
echo "alice:Alice123!" | sudo chpasswd
echo "bob:Bob456@" | sudo chpasswd

# Добавление в группы
sudo usermod -aG developers alice
sudo usermod -aG managers bob

# ============ ЗАДАНИЕ 2 ============
# Изменение владельца и группы
sudo chown alice lab_permissions/file1.txt
sudo chgrp developers lab_permissions/file2.txt
sudo chown alice:developers lab_permissions/public/file3.txt
sudo chown -R bob lab_permissions/public/
sudo chgrp -R managers lab_permissions/shared/
sudo chown alice:developers lab_permissions/script.sh

# Проверка
ls -la lab_permissions/

# ============ ЗАДАНИЕ 3 ============
# Числовой режим
chmod 755 lab_permissions/script.sh
chmod 644 lab_permissions/file1.txt
chmod 750 lab_permissions/private/
chmod 775 lab_permissions/public/

# Буквенный режим
chmod g+x lab_permissions/file1.txt
chmod o-w lab_permissions/file2.txt
chmod g+w lab_permissions/shared/
chmod u=rwx,g=rx,o=--- lab_permissions/private/
chmod a+x lab_permissions/script.sh

# Проверка
ls -la lab_permissions/

# ============ ЗАДАНИЕ 4 ============
# Просмотр ACL
getfacl lab_permissions/file1.txt

# Установка ACL для пользователя
setfacl -m u:bob:rw lab_permissions/file1.txt
getfacl lab_permissions/file1.txt

# ACL для группы
setfacl -m g:managers:r lab_permissions/file2.txt
getfacl lab_permissions/file2.txt

# ACL на каталог для пользователя
setfacl -m u:bob:rwx lab_permissions/private/
getfacl lab_permissions/private/

# Рекурсивный ACL
setfacl -R -m u:alice:rwx lab_permissions/shared/
getfacl lab_permissions/shared/

# Удаление ACL
setfacl -x u:bob lab_permissions/file1.txt

# Установка маски
setfacl -m m:rw lab_permissions/file2.txt
getfacl lab_permissions/file2.txt

# Удаление всех ACL
setfacl -b lab_permissions/file2.txt
getfacl lab_permissions/file2.txt

# ============ ЗАДАНИЕ 5 ============
# Просмотр атрибутов
lsattr lab_permissions/file1.txt

# Установка неизменяемости
sudo chattr +i lab_permissions/file1.txt

# Попытка изменения (ошибка)
echo "New content" >> lab_permissions/file1.txt  # Ошибка
rm lab_permissions/file1.txt  # Ошибка

# Снятие атрибута
sudo chattr -i lab_permissions/file1.txt

# Append only
sudo chattr +a lab_permissions/file2.txt

# Попытка перезаписи (ошибка)
echo "New" > lab_permissions/file2.txt  # Ошибка

# Добавление в конец (работает)
echo "Appended" >> lab_permissions/file2.txt
cat lab_permissions/file2.txt

# Защита скрипта
sudo chattr +i lab_permissions/script.sh

# Просмотр всех атрибутов
lsattr lab_permissions/*

# ============ ЗАДАНИЕ 6 ============
# Создание каталога project
mkdir ~/project
sudo chown alice:developers ~/project
chmod 770 ~/project

# Создание README.md
echo "# Project README" > ~/project/README.md
sudo chown alice:developers ~/project/README.md
chmod 644 ~/project/README.md

# ACL для bob
setfacl -R -m u:bob:rwx ~/project/

# Неизменяемость README
sudo chattr +i ~/project/README.md

# Отчет
{
    echo "=== File Permissions Report ==="
    echo "Date: $(date)"
    echo
    echo "=== ls -la ==="
    ls -laR ~/project/
    echo
    echo "=== getfacl ==="
    getfacl -R ~/project/
    echo
    echo "=== lsattr ==="
    lsattr -R ~/project/
} > permissions_report.txt

cat permissions_report.txt

# ============ ЗАДАНИЕ 7 ============
# Sticky bit
mkdir /tmp/tmp_shared
sudo chmod 1777 /tmp/tmp_shared
ls -ld /tmp/tmp_shared  # drwxrwxrwt

# SUID бит
echo "#!/bin/bash" > suid_test
echo "whoami" >> suid_test
chmod 4755 suid_test
ls -l suid_test  # -rwsr-xr-x

# SGID бит
mkdir sgid_test
chmod 2755 sgid_test
ls -ld sgid_test  # drwxr-sr-x

# ============ ЗАДАНИЕ 8 ============
# Поиск SUID
find ~ -perm -4000 2>/dev/null

# Поиск с атрибутом i
sudo find ~ -exec lsattr -d {} \; 2>/dev/null | grep "i"

# Комплексный отчет
{
    echo "=== Full Permissions Report ==="
    echo "Generated: $(date)"
    echo "Directory: ~/lab_permissions"
    echo
    echo "=== Standard Permissions (ls -la) ==="
    ls -laR ~/lab_permissions/
    echo
    echo "=== ACL (getfacl) ==="
    getfacl -R ~/lab_permissions/
    echo
    echo "=== Attributes (lsattr) ==="
    lsattr -R ~/lab_permissions/
} > full_report.txt

cat full_report.txt
```

---

## Дополнительные упражнения (для самостоятельной работы)

1. **Маска ACL:** Исследуйте, как маска ACL ограничивает права, даже если они установлены.  
2. **Стандартные ACL:** Настройте ACL по умолчанию для каталога (default ACL).  
3. **SUID пример:** Создайте программу с SUID и проверьте, что она выполняется от имени владельца.  
4. **SGID каталоги:** Создайте каталог с SGID и проверьте, что создаваемые файлы наследуют группу каталога.  
5. **Сравнение chattr:** Сравните поведение атрибутов `i` и `a` для файлов и каталогов.  
6. **Комбинации прав:** Создайте файлы с различными комбинациями прав (например, `rwx------`, `r--r--r--`, `---r-x---`) и протестируйте доступ.


