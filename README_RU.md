# Themis Finals: Руководство по эксплуатации
Данный документ также доступен на [английском языке](README.md).
## Вопрос-Ответ
### Что такое `Themis Finals`?
`Themis Finals` - это проверяющая система для организации финального раунда соревнований формата CTF.

### Что находится в этом репозитории?
В данном репозитории содержатся указания по приведению системы `Themis Finals` в рабочее состояние.

## Проект Themis
`Themis Finals` - это часть проекта `Themis`. Цель проекта - создание комплекта инструментов для организации соревнований CTF (отборочный и финальный раунды).

## Установка (для виртуализованного окружения)
### Предварительные требования (основная машина)
1. Git
2. [VirtualBox 5.0](https://www.virtualbox.org/wiki/Downloads)
3. [Vagrant 1.7.4](https://www.vagrantup.com/downloads.html)
4. *[для Windows машин]* командная строка unix. Вы можете попробовать использовать [Cygwin](http://cygwin.org/) или [Babun](http://babun.github.io/).
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
*Примечание:* Виртуальная машина, создаваемая Vagrant, должна иметь два сетевых интерфейса. Первый интерфейс NAT - требуется Vagrant'у для доступа к машине по SSH. Второй интерфейс типа мост. Чтобы он заработал, вам необходимо указать (в `opts.yml`) свободный IP адрес (вне диапазона DHCP вашего роутера, иначе может не заработать) и полное название вашего сетевого адаптера (название, содержащие не латинские символы, не работают, это проблема Vagrant). Вместо интерфейса типа мост также можно использовать т.н. режим host-only (обратитесь к [документации Vagrant](https://docs.vagrantup.com/v2/networking/private_network.html) для получения дополнительной информации).
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
### Конфигурационный файл
Создайте конфигурационный файл
```
$ cd /var/themis/finals
$ cp config.rb.example config.rb
```
Файл `config.rb` содержит все настройки системы, в том числе настройки сети, данные о командах и данные о сервисах.

В секции `network` нужно указать адрес хостовой машины. Например,
``` ruby
network do
    internal '10.0.0.0/24'
    # other settings
end
```

Каждая команда описывается в отдельной секции. Указывается псевдоним (предназначенный для внутреннего использования), название команды, адрес подсети и адрес игровой машины. Например,
``` ruby
team 'team_1' do
    name 'Team #1'
    network '10.0.1.0/24'
    host '10.0.1.3'
end
```

Каждый сервис описывается в отдельной секции. Указывается псевдоним (предназначенный для внутреннего использования) и название сервиса. Например,
``` ruby
service 'service_1' do
    name 'Service #1'
end
```

### Программы проверки состояния сервиса
Программа проверки состояния сервиса должна находиться в отдельной подпапке в папке `/var/themis`. В поставку включено два примера таких программ: `/var/themis/sample-checker-rb` и `/var/themis/sample-checker-py`.

Программа проверки запускается с помощью менеджера процессов [God](https://github.com/mojombo/god), поэтому вы также должны создать конфигурационный файл с параметрами запуска в папке `/var/themis/finals/god.d` (для примера вы можете посмотреть файлы `sample-checker-py.god` и `sample-checker-rb.god` в этой же папке). В конфигурационном файле вам потребуется указать команду запуска программы, рабочую директорию, пути к файлам логов, а также некоторые внутренние параметры:  
1. `TUBE_LISTEN` - `themis.service.SERVICE_ALIAS.listen`,  
2. `TUBE_REPORT` - `themis.service.SERVICE_ALIAS.report`,  
где `SERVICE_ALIAS` - псевдоним сервиса, указанный в файле `config.rb`.

Вместо развёртывания программы проверки вручную, вы можете написать Chef cookbook для автоматической установки.

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
$ bundle exec rake contest:start_async
```
Выполнение данной команды не запускает соревнование сразу. Старт соревнования будет проведён автоматически после того, как система создаст первые флаги.
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
Соревнование не может быть остановлено мгновенно, а будет остановлено в автоматическом режиме после того, как у всех флагов в системе истечет срок жизни, и все заработанные очки будут пересчитаны.

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
Vagrant запустит виртуальную машину с Ubuntu Desktop. В этой виртуальной машине вы можете сдавать флаги несколькими способами. Обратитесь к [themis-attack-protocol](https://github.com/aspyatkin/themis-attack-protocol) и [themis-attack-py](https://github.com/aspyatkin/themis-attack-py) для получения дополнительной информации.

### Подсказки
1. IP адрес основной машины должен быть прописан как `internal network` в `config.rb`.
2. Для того, чтобы узнать флаги в системе, можно просматривать логи в `/var/themis/finals/logs/queue.log`.

## Смотрите также
- [themis-finals](https://github.com/aspyatkin/themis-finals)
- [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)
- [themis-attack-protocol](https://github.com/aspyatkin/themis-attack-protocol)
- [themis-attack-py](https://github.com/aspyatkin/themis-attack-py)
- [themis-attack-result](https://github.com/aspyatkin/themis-attack-result)
- [themis-checker-server](https://github.com/aspyatkin/themis-checker-server)
- [themis-checker-result](https://github.com/aspyatkin/themis-checker-result)
- [themis-checker-py](https://github.com/aspyatkin/themis-checker-py)

## Лицензия
MIT @ [Пяткин Александр](https://github.com/aspyatkin)
