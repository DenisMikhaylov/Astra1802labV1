# Лабораторная работа №3: Работа с файлами в Linux

**Дисциплина:** Информационные технологии  
**Цель работы:** Освоить базовые операции с файлами и каталогами в Linux: создание, копирование, перемещение, удаление, поиск файлов по различным критериям, определение типа файла, архивация и сжатие данных.  
**Стенд:** Виртуальная машина Astra Linux.  
**Учетные данные:**  
- Пользователь: `sa`  
- Пароль: `12345678`  
- Имя машины: `Astra`

---

## Теоретическая справка

### Основные команды для работы с файлами

| Команда | Описание | Пример |
|---------|----------|--------|
| `ls` | Просмотр содержимого каталога | `ls -la` |
| `pwd` | Показать текущий каталог | `pwd` |
| `cd` | Сменить каталог | `cd /home/sa` |
| `touch` | Создать пустой файл или обновить время | `touch file.txt` |
| `mkdir` | Создать каталог | `mkdir new_dir` |
| `cp` | Копировать файлы/каталоги | `cp file1 file2` |
| `mv` | Переместить/переименовать | `mv file1 dir/` |
| `rm` | Удалить файлы | `rm file.txt` |
| `rmdir` | Удалить пустой каталог | `rmdir empty_dir` |
| `rm -r` | Удалить каталог с содержимым | `rm -r dir` |
| `file` | Определить тип файла | `file document.pdf` |
| `find` | Поиск файлов | `find / -name "*.txt"` |

### Поиск файлов с помощью find

Команда `find` имеет множество опций для поиска:

| Критерий | Опция | Пример |
|----------|-------|--------|
| По имени | `-name` | `find . -name "*.txt"` |
| По размеру | `-size` | `find . -size +10M` |
| По времени изменения | `-mtime` | `find . -mtime -7` |
| По времени доступа | `-atime` | `find . -atime +30` |
| По времени создания | `-cmin` | `find . -cmin -60` |
| По типу | `-type` | `find . -type f` (файлы), `-type d` (каталоги) |

**Размеры в find:**
- `+10M` — больше 10 МБ
- `-5k` — меньше 5 КБ
- `100c` — ровно 100 байт

### Определение типа файла

Команда `file` анализирует содержимое файла и определяет его тип:
```bash
file /etc/passwd          # текстовый файл
file /bin/ls              # исполняемый файл (ELF)
file image.jpg            # изображение JPEG
file archive.tar.gz       # сжатый архив
```

### Архивация и сжатие

#### Основные команды

| Команда | Описание | Пример |
|---------|----------|--------|
| `tar` | Создание/распаковка архивов | `tar -czf archive.tar.gz dir/` |
| `gzip` | Сжатие/разжатие файлов | `gzip file.txt` |
| `gunzip` | Разжатие .gz файлов | `gunzip file.txt.gz` |
| `bzip2` | Сжатие с лучшим коэффициентом | `bzip2 file.txt` |
| `zip` | Архивация в формат ZIP | `zip -r archive.zip dir/` |
| `unzip` | Распаковка ZIP архивов | `unzip archive.zip` |

#### Опции команды tar

| Опция | Описание |
|-------|----------|
| `c` | Создать архив (create) |
| `x` | Распаковать архив (extract) |
| `t` | Просмотреть содержимое |
| `v` | Подробный вывод (verbose) |
| `f` | Имя файла архива |
| `z` | Сжать через gzip |
| `j` | Сжать через bzip2 |
| `J` | Сжать через xz |

**Примеры:**
```bash
# Создание архива .tar.gz
tar -czvf archive.tar.gz /path/to/dir

# Создание архива .tar.bz2
tar -cjvf archive.tar.bz2 /path/to/dir

# Распаковка .tar.gz
tar -xzvf archive.tar.gz

# Распаковка в конкретную директорию
tar -xzvf archive.tar.gz -C /target/dir
```

---

## Задания

### Задание 1. Создание файлов и каталогов
1. В домашней директории создайте каталог `project`.  
2. Внутри `project` создайте каталоги: `docs`, `backup`, `temp`, `archives`.  
3. Создайте файлы:  
   - `project/readme.txt` с содержимым "Project description"  
   - `project/docs/manual.txt` с содержимым "User manual v1.0"  
   - `project/docs/install.txt` с содержимым "Installation guide"  
   - `project/docs/config.conf` с содержимым "DEBUG=true\nPORT=8080"  
4. Создайте в `project` файл `data.log` и запишите в него текущую дату с помощью команды `date`.  
5. Создайте 5 пустых файлов в `project/temp`: `file1.log` ... `file5.log` одной командой.

---

### Задание 2. Копирование файлов и каталогов
1. Скопируйте файл `project/readme.txt` в `project/backup/readme_backup.txt`.  
2. Скопируйте весь каталог `project/docs` в `project/backup/docs_backup`.  
3. Скопируйте все файлы с расширением `.log` из `project/temp` в `project/backup`.  
4. Создайте копию файла `project/data.log` с именем `project/data_old.log`.

---

### Задание 3. Перемещение и переименование
1. Переместите файл `project/temp/file1.log` в `project/docs/file1.log`.  
2. Переименуйте `project/docs/file1.log` в `project/docs/old_file.log`.  
3. Переместите все файлы из `project/temp` в `project/backup`.  
4. Переименуйте каталог `project/temp` в `project/archived_temp`.

---

### Задание 4. Поиск файлов (find)

#### 4.1. Поиск по имени
1. Найдите в каталоге `project` все файлы с расширением `.txt`.  
2. Найдите все файлы, в имени которых есть слово `backup`.

#### 4.2. Поиск по размеру
1. Найдите в каталоге `project` файлы размером **меньше 1 КБ**.  
2. Найдите файлы размером **больше 100 байт** во всей домашней директории.

#### 4.3. Поиск по дате
1. Найдите в `project` файлы, которые были **изменены за последние 5 минут**.  
2. Найдите файлы, которые **не изменялись более 7 дней** (во всей домашней директории).  
3. Найдите файлы, которые были **созданы менее 2 часов назад** (используйте `-cmin`).

#### 4.4. Комбинированный поиск
1. Найдите в `project` все файлы с расширением `.log`, размером больше 0 байт.  
2. Найдите каталоги в `project`, которые были изменены за последний час.

---

### Задание 5. Определение типа файла
1. Определите тип файлов:  
   - `/etc/passwd`  
   - `/bin/bash`  
   - `project/readme.txt`  
   - `project/docs/config.conf`  
2. Создайте символическую ссылку на `project/readme.txt` в домашней директории с именем `link_to_readme`.  
3. Определите тип созданной ссылки.  
4. Найдите в системе все файлы типа `directory` (каталоги) с помощью `find` и `file`.

---

### Задание 6. Архивация и сжатие (НОВЫЙ МОДУЛЬ)

#### 6.1. Сжатие отдельных файлов
1. Создайте сжатые копии файла `project/readme.txt` с помощью:  
   - `gzip` (получится `readme.txt.gz`)  
   - `bzip2` (получится `readme.txt.bz2`)  
2. Сравните размеры исходного и сжатых файлов.  
3. Распакуйте сжатый файл обратно.

#### 6.2. Создание архивов (tar)
1. Создайте архив `project_archive.tar` из каталога `project` (без сжатия).  
2. Создайте сжатый архив `project_backup.tar.gz` из каталога `project` (сжатие gzip).  
3. Создайте архив `project_docs.tar.bz2` только из каталога `project/docs` (сжатие bzip2).  
4. Просмотрите содержимое созданных архивов без распаковки.

#### 6.3. Извлечение архивов
1. Создайте каталог `extract_test` и распакуйте в него `project_archive.tar`.  
2. Распакуйте `project_backup.tar.gz` в каталог `extract_gz`.  
3. Распакуйте только файл `readme.txt` из архива `project_backup.tar.gz` в текущую директорию.

#### 6.4. ZIP архивы
1. Создайте ZIP архив `project.zip` из каталога `project` с максимальным сжатием.  
2. Просмотрите содержимое `project.zip` без распаковки.  
3. Распакуйте `project.zip` в каталог `extract_zip`.  
4. Добавьте файл `project/data_old.log` в существующий архив `project.zip`.

#### 6.5. Комплексное задание по архивации
Создайте скрипт `archive_manager.sh` в домашней директории, который:
1. Создает каталог `backup_$(date +%Y%m%d_%H%M%S)`.  
2. Копирует все файлы с расширением `.txt` и `.log` из `project` в этот каталог.  
3. Создает два архива этого каталога:  
   - `archive.tar.gz` (с использованием gzip)  
   - `archive.zip` (с использованием zip)  
4. Определяет тип созданных архивов с помощью `file`.  
5. Сравнивает размеры полученных архивов и выводит результат сравнения.

---

### Задание 7. Комплексное задание (скрипт резервного копирования)
Создайте скрипт `backup.sh` в домашней директории, который:
1. Создает каталог `backup_$(date +%Y%m%d)`.  
2. Копирует все файлы с расширением `.txt` из `project` в этот каталог.  
3. Определяет тип каждого скопированного файла и записывает результат в `file_types.txt` внутри каталога резервной копии.  
4. Создает сжатый архив `backup_$(date +%Y%m%d).tar.gz` из этого каталога.  
5. Удаляет исходный каталог резервной копии, оставляя только архив.

---

## Решение (эталон выполнения)

```bash
# ============ ЗАДАНИЕ 1 ============
cd ~
mkdir -p project/{docs,backup,temp,archives}

echo "Project description" > project/readme.txt
echo "User manual v1.0" > project/docs/manual.txt
echo "Installation guide" > project/docs/install.txt
echo -e "DEBUG=true\nPORT=8080" > project/docs/config.conf
date > project/data.log
touch project/temp/file{1..5}.log

# Проверка структуры
tree project

# ============ ЗАДАНИЕ 2 ============
cp project/readme.txt project/backup/readme_backup.txt
cp -r project/docs project/backup/docs_backup
cp project/temp/*.log project/backup/
cp project/data.log project/data_old.log

# Проверка копирования
ls -l project/backup/

# ============ ЗАДАНИЕ 3 ============
mv project/temp/file1.log project/docs/file1.log
mv project/docs/file1.log project/docs/old_file.log
mv project/temp/* project/backup/
mv project/temp project/archived_temp

# Проверка
ls -la project/
ls -la project/backup/

# ============ ЗАДАНИЕ 4 ============
# 4.1 Поиск по имени
find project -name "*.txt"
find project -name "*backup*"

# 4.2 Поиск по размеру
find project -size -1k
find ~ -size +100c

# 4.3 Поиск по дате
find project -mmin -5
find ~ -mtime +7
find ~ -cmin -120

# 4.4 Комбинированный поиск
find project -name "*.log" -size +0
find project -type d -cmin -60

# ============ ЗАДАНИЕ 5 ============
file /etc/passwd
file /bin/bash
file project/readme.txt
file project/docs/config.conf

ln -s project/readme.txt ~/link_to_readme
file ~/link_to_readme

find / -type d 2>/dev/null | head -10

# ============ ЗАДАНИЕ 6 ============
# 6.1 Сжатие отдельных файлов
cd ~/project
cp readme.txt readme_copy.txt
gzip readme_copy.txt
# Теперь readme_copy.txt.gz

cp readme.txt readme_copy2.txt
bzip2 readme_copy2.txt
# Теперь readme_copy2.txt.bz2

# Сравнение размеров
ls -lh readme.txt readme_copy.txt.gz readme_copy2.txt.bz2

# Распаковка
gunzip readme_copy.txt.gz
bunzip2 readme_copy2.txt.bz2

# 6.2 Создание архивов (tar)
cd ~
tar -cvf project_archive.tar project/
tar -czvf project_backup.tar.gz project/
tar -cjvf project_docs.tar.bz2 project/docs/

# Просмотр содержимого архивов
tar -tvf project_archive.tar | head -5
tar -tzvf project_backup.tar.gz | head -5
tar -tjvf project_docs.tar.bz2

# 6.3 Извлечение архивов
mkdir ~/extract_test
tar -xvf project_archive.tar -C ~/extract_test/

mkdir ~/extract_gz
tar -xzvf project_backup.tar.gz -C ~/extract_gz/

# Извлечение только readme.txt
tar -xzvf project_backup.tar.gz --wildcards "*/readme.txt"

# 6.4 ZIP архивы
zip -r project.zip project/
unzip -l project.zip
mkdir ~/extract_zip
unzip project.zip -d ~/extract_zip/
zip -u project.zip project/data_old.log

# ============ ЗАДАНИЕ 7 ============
cat > ~/backup.sh <<'EOF'
#!/bin/bash
BACKUP_DIR="$HOME/backup_$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"
find ~/project -name "*.txt" -exec cp {} "$BACKUP_DIR/" \;

for file in "$BACKUP_DIR"/*.txt; do
    file "$file" >> "$BACKUP_DIR/file_types.txt"
done

cd ~
tar -czf "backup_$(date +%Y%m%d).tar.gz" "$BACKUP_DIR"
rm -rf "$BACKUP_DIR"
echo "Backup created: backup_$(date +%Y%m%d).tar.gz"
EOF

chmod +x ~/backup.sh
./backup.sh

# Проверка
ls -lh ~/backup_*.tar.gz
```

---

## Дополнительные упражнения (для самостоятельной работы)

1. **Сравнение алгоритмов сжатия:** Заархивируйте каталог project с помощью gzip, bzip2, xz и сравните размеры и время создания.  
2. **Поиск в архивах:** Используйте `zgrep` для поиска текста в сжатых файлах.  
3. **Скорость сжатия:** Сравните время сжатия `gzip`, `bzip2`, `xz` для большого файла.  
4. **Создание инкрементальных архивов:** Изучите опции `-N` и `--newer` для создания инкрементальных бэкапов.  
5. **Массовая архивация:** Напишите скрипт для архивации всех каталогов в домашней директории.

---



---

