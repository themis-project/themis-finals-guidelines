# Themis Finals: Руководство по эксплуатации
Данный документ также доступен на [английском языке](README.md).
## Вопрос-Ответ
### Что такое `Themis Finals`?
`Themis Finals` - это проверяющая система для организации финального раунда соревнования [VolgaCTF](http://volgactf.ru).

### Что находится в этом репозитории?
В данном репозитории содержатся указания по приведению системы `Themis Finals` в рабочее состояние.

## Проект Themis
`Themis Finals` - это часть проекта `Themis`. Цель проекта - создание комплекта инструментов для организации соревнований CTF (отборочный и финальный раунды).

## Связанные репозитории
`Themis Finals` содержит несколько репозиториев:
- [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure) - Конфигурация Chef Solo для развёртывания виртуальной машины или реального сервера.
- [themis-finals](https://github.com/aspyatkin/themis-finals) - Основной репозиторий проекта, содержит бэкэнд (Ruby) и фронтэнд (JavaScript ECMA-262) системы.
- [themis-checker-server](https://github.com/aspyatkin/themis-checker-server) - Утилиты для создания программ проверки состояния сервиса (Ruby).
- [themis-checker-result](https://github.com/aspyatkin/themis-checker-result) - Коды статуса для программ проверки состояния сервиса (Ruby).
- [themis-checker-py](https://github.com/aspyatkin/themis-checker-py) - Утилиты для создания программ проверки состояния сервиса (Python).
- [themis-attack-result](https://github.com/aspyatkin/themis-attack-result) - Коды статуса результата атак (Ruby).
- [themis-sample-checker-rb](https://github.com/aspyatkin/themis-sample-checker-rb) - Пример программы проверки состояния сервиса на Ruby.
- [themis-sample-checker-py](https://github.com/aspyatkin/themis-sample-checker-py) - Пример программы проверки состояния сервиса на Python.

## Установка (для виртуализованного окружения)
### Предварительные требования (основная машина)
1. Git
2. [VirtualBox 5.0](https://www.virtualbox.org/wiki/Downloads)
3. [Vagrant 1.7.4](https://www.vagrantup.com/downloads.html)
4. *[для Windows машин]* командная строка unix. Вам может понадобиться [Cygwin](http://cygwin.org/) или [Babun](http://babun.github.io/).
5. Наличие достаточного количества вычислительных ресурсов и места на диске, для того чтобы можно было одновременно запустить 2-3 виртуальных машины.

### Проверка предварительных требований
`vagrant version` должно выводить что-то похожее на `Installed version: 1.7.4`.

### Создание инфраструктуры
**Примечание:** `$` обозначает приглашение ввода в приложении командной строки.

1. Получите [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)  

        $ mkdir ~/Documents/projects/whatever
        $ cd ~/Documents/projects/whatever
        $ git clone https://github.com/aspyatkin/themis-finals-infrastructure
        $ cd themis-finals-infrastructure

2. Установите плагин librarian-chef для Vagrant  
`$ vagrant plugin install vagrant-librarian-chef`
3. Создайте файл атрибутов (для Chef)  
`$ cp nodes/vagrant.json.example nodes/vagrant.json`  
*Примечание:* Вы можете устанавливать атрибуты в файле `nodes/vagrant.json`. Этот файл будет игнорироваться системой контроля версий Git, поэтому будьте уверены, что изменяя его, вы ничего не поломаете.
4. Создайте файл атрибутов виртуальной машины (для Vagrant)  
`$ cp opts.yml.example opts.yml`  
*Примечание:* Виртуальная машина, создаваемая Vagrant, должна иметь два сетевых интерфейса. Первый интерфейс NAT - требуется Vagrant'у для доступа к машине по SSH. Второй интерфейс типа мост. Чтобы он заработал, вам необходимо указать (в `opts.yml`) свободный IP адрес (вне диапазона DHCP вашего роутера, иначе может не заработать) и полное название вашего сетевого адаптера (название, содержащие не латинские символы, не работают, это проблема Vagrant).
5. Создайте и разверните инфраструктуру на виртуальной машине   
`$ vagrant up`  
*Примечание:* Первый раз выполнение этой команды займёт достаточно много времени, поскольку Vagrant загрузит базовый образ Ubuntu и после этого установит необходимое ПО, включая  Git, Ruby, PostgreSQL и другие программы.

## Управление инфраструктурой (для виртуализованного окружения)
Вы всегда можете узнать состояние виртуальной машины с помощью графического интерфейса VirtualBox но **не изменяйте** состояние виртуальной машины через графический интерфейс VirtualBox. Это забота Vagrant.

Сначала, измените рабочую директорию на ту, в которую вы скопировали репозиторий при создании инфраструктуры:
`cd ~/Documents/projects/whatever/themis-finals-infrastructure`

### Проверка состояния машины
`$ vagrant status`
### Запуск (на выключенной/приостановленной машины)
`$ vagrant up`
### Выключение (на запущенной машине)
`$ vagrant halt`
### Приостановка (на запущенной машине)
`$ vagrant suspend`  
*Примечание:* Эта команда приостанавливает выполнение виртуальной машины без её выключения. Это ускоряет последующее возобновление работы с этой машиной.
### Возобновление (на приостановленной машине)
`$ vagrant up` or `$ vagrant resume`  
*Примечание:* Эта команда возобновляет выполнение виртуальной машины.
### Конфигурация (на запущенной машине)
`$ vagrant provision`  
*Примечание:* Эта команда запускает Chef для обновления системной конфигурации. Если вы обновили репозиторий `themis-finals-infrastructure` или изменили файл атрибутов `nodes/vagrant.json`, вам потребуется выполнить эту команду.
### Перезапуск (на запущенной машине)
`$ vagrant reload`  
*Примечание:* Вы должны выполнить эту команду, если вы изменили файл атрибутов виртуальной машины `opts.yml`.
### Удаление (на любом состоянии)
`$ vagrant destroy`  
*Примечание:* Эта команда полностью удаляет виртуальную машину VirtualBox.
### Дополнительная информация
Для получения дополнительной информации о вышеупомянутых командах обратитесь к [документации Vagrant](https://docs.vagrantup.com/v2/).
## Соревнование
Настройка и управление соревнования производится на гостевой виртуальной машине. Для подключения к виртуальной машине выполните следующие команды:
```
$ cd ~/Documents/projects/whatever/themis-finals-infrastructure
$ vagrant ssh
```
Теперь вы подключены в виртуальной машине по SSH. Для правки файлов на ней вы можете использовать SFTP или общие сетевые папки.
### Настройка
1. Создайте конфигурационный файл

        $ cd /var/themis/finals
        $ cp config.rb.example config.rb

2. Измените настройки (отредактируйте файл, например, с помощью Vim)
Вам потребуется изменить сетевые адреса, а также информаци о командах и сервисах. Это все можно указать в файле `/var/themis/finals/config.rb`.
3. Напишите программу проверки состояния сервиса
В поставку включено два примера таких программ: `/var/themis/sample-checker-rb` и `/var/themis/sample-checker-py`. Программа проверки запускается с помощью менеджера процессов [God](https://github.com/mojombo/god), поэтому вы также должны создать конфигурационный файл с параметрами запуска в папке `/var/themis/finals/god.d` (для примера вы можете посмотреть файлы `sample-checker-py.god` и `sample-checker-rb.god` в этой же папке).

### Управление
Управление состоянием соревнования осуществляется с помощью утилит командной строки.
Для запуска этих утилит, вам нужно находиться в папке `/var/themis/finals`.

#### Сброс
```
$ cd /var/themis/finals
$ bundle exec rake db:reset
$ bundle exec rake contest:init
```

#### Запуск процессов
```
$ sudo -s
# god -c /var/themis/finals/god.conf
# exit
$
```

#### Старт соревнования
```
$ cd /var/themis/finals
$ bundle exec rake contest:start
```
Предполагая, что ваша виртуальная машина имеет IP адрес `192.168.163.150`, вы можете получить доступ к фронтэнду системы, перейдя на `http://192.168.163.150/` в вашем браузере.

#### Приостановка соревнования
```
$ cd /var/themis/finals
$ bundle exec rake contest:pause
```

#### Возобновление соревнования
```
$ cd /var/themis/finals
$ bundle exec rake contest:resume
```

#### Завершение соревнования
```
$ cd /var/themis/finals
$ bundle exec rake contest:complete_async
```
Соревнования не останавливается мгновенно. Оно будет остановлено в автоматическом режиме после того, как у всех флагов в системе истечет срок жизни, и все заработанные очки будут пересчитаны.

#### Остановка процессов
```
$ sudo -s
# god terminate
# exit
$
```

Для начала нового соревнования, вам необходимо сначала сбросить состояние предыдущего.

#### Перезапуск программы проверки сервиса
Иногда в процессе разработки программу проверки приходится дописывать или исправлять прямо во время соревнования. Очевидно, требуется способ перезапуска процессов.
```
$ sudo -s
# god status  // find your checker's process name
# god restart my-service-checker
# exit
$
```

### Отключение табло (для подсетей команд и гостей)
```
$ cd /var/themis/finals
$ bundle exec rake scoreboard:disable
```

### Атаки
Атаки могут производиться только из подсетей команд. Чтобы совершить атаку, вам необходимо запустить другую виртуальную машину с подходящим IP адресом. Выполните следующие команды на основной машине:
```
$ cd ~/Documents/projects/whatever
$ git clone https://github.com/aspyatkin/themis-sample-node
$ cd themis-sample-node
$ cp opts.yml.example opts.yml
$ vim opts.yml  // specify IP address and network adapter's name
$ vagrant up
```
Vagrant запустит виртуальную машину с Ubuntu Desktop. В этой виртуальной машине вы можете использовать `curl`, чтобы выполнить атаку. Полагая, что IP адрес проверяющей системы `192.168.163.150`:
```
$ curl -X POST -v -H "Content-Type: application/json" http://192.168.163.150/api/submit -d "[\"FLAG_HERE\"]"
```
На вход подаются данные в формате JSON, ответ приходит тоже в формате JSON. Чтобы узнать о кодах результата атаки, обратитесь к репозиторию [themis-attack-result](https://github.com/aspyatkin/themis-attack-result).

### Подсказки
1. IP адрес основной машины должны быть прописана как `internal network` в `config.rb`.
2. Для того, чтобы узнать флаги в системе, можно просматривать логи в `/var/themis/finals/logs/queue.log`.

### Примечания
Если вы дочитали до этого места, то вы вероятно уже знаете, что работа над системой еще не завершена.

## Лицензия
MIT @ [Пяткин Александр](https://github.com/aspyatkin)
