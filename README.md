### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-02-py/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Traceback (most recent call last):  File "<input>", line 1, in <module> TypeError: unsupported operand type(s) for +: 'int' and 'str  |
| Как получить для переменной `c` значение 12?  | c=str(a)+b  |
| Как получить для переменной `c` значение 3?  | c=a+int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(os.path.abspath(prepare_result))
#        break
```

### Вывод скрипта при запуске при тестировании:
```
us@ubuntu:~/netology/sysadm-homeworks$ ./3script_py.sh
/home/us/netology/sysadm-homeworks/01-intro-01/netology.jsonnet
/home/us/netology/sysadm-homeworks/04-script-01-bash/README.md
/home/us/netology/sysadm-homeworks/04-script-02-py/README.md
```
Почему-то в данном случае скрипт выдаёт полные имена файлов правильно только если скрипт лежит в папке репозитория. Если его переместить, то вывод будет содержать не путь к изменённому файлу в репозитории, а (путь к файлу скрипта)'+'(имя измененного файла). Вот так:  

```
us@ubuntu:~$ ./3script_py.sh
/home/us/01-intro-01/netology.jsonnet
/home/us/04-script-01-bash/README.md
/home/us/04-script-02-py/README.md
```
    
## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3
print("Введите полный путь к репозиторию(со знаком '/' в конце):")
path = input()

import os

os.chdir(path)
bash_command = ["git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(path+prepare_result)
#        break
```

### Вывод скрипта при запуске при тестировании:
```
us@ubuntu:~$ ./4script.sh
Введите полный путь к репозиторию(со знаком '/' в конце):
/home/us/new_repo/netology/ <- здесь вводим путь к репо
    
/home/us/new_repo/netology/README.md
/home/us/new_repo/netology/netology.tf
/home/us/new_repo/netology/netology.yaml
```  
    
Или можно передавать путь к репозиторию аргументом:
```python
#!/usr/bin/env python3

import os
import sys

path = sys.argv[1]
os.chdir(path)
bash_command = ["git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(path+prepare_result)
#        break
    
```
    
### Вывод скрипта при запуске при тестировании:
```
us@ubuntu:~$ ./6script.sh /home/us/netology/sysadm-homeworks/
/home/us/netology/sysadm-homeworks/01-intro-01/netology.jsonnet
/home/us/netology/sysadm-homeworks/04-script-01-bash/README.md
/home/us/netology/sysadm-homeworks/04-script-02-py/README.md
```
В задании говорилось только о модифицированных файлах, если нужно получить информацию о всех статусах, то скрипт нужно дополнить:
```python
#!/usr/bin/env python3
print("Введите полный путь к репозиторию(со знаком '/' в конце):")
path = input()

import os

os.chdir(path)
bash_command = ["git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        mod_result = result.replace('\tmodified:   ', '')
        print("modified:"+path+mod_result)
for result in result_os.split('\n'):
    if result.find('new file') != -1:
        new_result = result.replace('\tnew file:   ', '')
        print("new file:"+path+new_result)
for result in result_os.split('\n'):
    if result.find('deleted') != -1:
        del_result = result.replace('\tdeleted:   ', '')
        print("deleted:"+path+del_result)
```

Вывод будет такой:
```
us@ubuntu:~$ ./4script.sh
Введите полный путь к репозиторию(со знаком '/' в конце):
/home/us/netology/sysadm-homeworks/
modified:/home/us/netology/sysadm-homeworks/01-intro-01/netology.jsonnet
modified:/home/us/netology/sysadm-homeworks/04-script-01-bash/README.md
modified:/home/us/netology/sysadm-homeworks/04-script-02-py/README.md
new file:/home/us/netology/sysadm-homeworks/3script_py.sh
deleted:/home/us/netology/sysadm-homeworks/ 03-sysadmin-08-net/README.md
```                                                                                                                                                                                                                                                                                                                                                                   
## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import socket
import json

conf_file="config.json"

with open(conf_file) as json_data_file:
    conf = json.load(json_data_file)

for host, ip in conf.items():
    new_ip=socket.gethostbyname(host)

    if (ip != new_ip):
        print ('[ERROR] {} IP mismatch: {} {}'.format(host,ip,new_ip))
        conf[host]=new_ip

for host, ip in conf.items():
    print('{} - {}'.format(host,ip))

with open(conf_file, "w") as json_data_file:
    json.dump(conf, json_data_file, indent=2)
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\y.kozlov\PycharmProjects\pythonProject\venv\Scripts\python.exe C:/Users/y.kozlov/PycharmProjects/pythonProject/7.py
drive.google.com - 74.125.131.194
mail.google.com - 64.233.162.19
google.com - 173.194.221.138
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```
