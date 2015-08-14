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
4. Ruby 2.2.2 - предпочтительней установка [rbenv](https://github.com/sstephenson/rbenv) и [ruby-build](https://github.com/sstephenson/ruby-build)
5. *для Windows машин* командная строка unix. Вам может понадобиться [Cygwin](http://cygwin.org/) или [Babun](http://babun.github.io/) (Ruby может быть установлен с помощью утилиты `pact`, входящей в состав Babun).
6. Наличие достаточного количества вычислительных ресурсов и места на диске, для того чтобы можно было одновременно запустить 2-3 виртуальных машины.

### Проверка предварительных требований
1. `vagrant version` должно выводить что-то похожее на `Installed version: 1.7.4`.
2. `ruby -v` должно выводить что-то похожее на `ruby2.2.2p95`.

### Создание инфраструктуры
**Примечание:** `$` обозначает приглашение ввода в приложении командной строки.

1. Установите [Bundler](https://github.com/bundler/bundler)  
`$ gem install bundler`  
*Примечание:* Вам может потребоваться выполнить `rbenv rehash` после установки если Ruby установлен через `rbenv` (утилита `bundler` содержит программы командной строки).
2. Получите [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)  

        $ mkdir ~/Documents/projects/whatever
        $ cd ~/Documents/projects/whatever
        $ git clone https://github.com/aspyatkin/themis-finals-infrastructure
        $ cd themis-finals-infrastructure

3. Установите зависимости  
`$ bundle`  
*Примечание:* Вам может понадобиться выполнить `rbenv rehash` после установки, если Ruby установлен через `rbenv` (утилита `librarian-chef` содержит программы командной строки).
4. Установите дополнительные модули  
`$ librarian-chef install`
5. Создайте файл атрибутов (для Chef)  
`$ cp nodes/vagrant.json.example nodes/vagrant.json`  
*Примечание:* Вы можете устанавливать атрибуты в файле `nodes/vagrant.json`. Этот файл будет игнорироваться системой контроля версий Git, поэтому будьте уверены, что изменяя его, вы ничего не поломаете.
6. Создайте файл атрибутов виртуальной машины (для Vagrant)  
`$ cp opts.yml.example opts.yml`  
*Примечание:* Виртуальная машина, создаваемая Vagrant, должна иметь два сетевых интерфейса. Первый интерфейс NAT - требуется Vagrant'у для доступа к машине по SSH. Второй интерфейс типа мост. Чтобы он заработал, вам необходимо указать (в `opts.yml`) свободный IP адрес (вне диапазона DHCP вашего роутера, иначе может не заработать) и полное название вашего сетевого адаптера (название, содержащие не латинские символы, не работают, это проблема Vagrant).
7. Создайте и разверните инфраструктуру на виртуальной машине   
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
## Лицензия
MIT @ [Пяткин Александр](https://github.com/aspyatkin)
