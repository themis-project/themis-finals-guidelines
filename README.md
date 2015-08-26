# Themis Finals Guidelines
This document is also available in [Russian language](README_RU.md), but it may be incomplete.
## QA section
### What is `Themis Finals`?
`Themis Finals` is a CTF contest checking system for [VolgaCTF Finals](http://volgactf.ru) contest.

### What's this repo all about?
This repo contains guidelines for getting `Themis Finals` up and running.

## Themis project
`Themis Finals` is a part of a project called `Themis`. Its purpose is to develop an entire toolset for organizing CTFs (quals and finals rounds).

## Related repos
`Themis Finals` consists of several repositories:
- [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure) - Chef Solo configuration to provision virtual machine or bare metal server.
- [themis-finals](https://github.com/aspyatkin/themis-finals) - Main repository of the project, contains checking system backends (Ruby) as well as frontend (JavaScript ECMA-262).
- [themis-checker-server](https://github.com/aspyatkin/themis-checker-server) - Tools to create service checker (Ruby).
- [themis-checker-result](https://github.com/aspyatkin/themis-checker-result) - Service checker result status codes (Ruby).
- [themis-checker-py](https://github.com/aspyatkin/themis-checker-py) - Tools to create service checker (Python).
- [themis-attack-result](https://github.com/aspyatkin/themis-attack-result) - Attack result status codes (Ruby).
- [themis-sample-checker-rb](https://github.com/aspyatkin/themis-sample-checker-rb) - Sample checker written in Ruby.
- [themis-sample-checker-py](https://github.com/aspyatkin/themis-sample-checker-py) - Sample checker written in Python.

## Installation (for virtualized environment)
### Prerequisites (host machine)
1. Git
2. [VirtualBox 5.0](https://www.virtualbox.org/wiki/Downloads)
3. [Vagrant 1.7.4](https://www.vagrantup.com/downloads.html)
4. *[for Windows hosts]* unix shell. You may try [Cygwin](http://cygwin.org/) or [Babun](http://babun.github.io/).
5. Enough computational resources and disk space to get 2-3 virtual machines run simultaneously.

### Check prerequisites
`vagrant version` should report something like `Installed version: 1.7.4`.

### Creating infrastructure
**Note:** `$` indicates nothing more that a shell prompt.

1. Get [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)  

        $ mkdir ~/Documents/projects/whatever
        $ cd ~/Documents/projects/whatever
        $ git clone https://github.com/aspyatkin/themis-finals-infrastructure
        $ cd themis-finals-infrastructure

2. Install librarian-chef plugin for Vagrant  
`$ vagrant plugin install vagrant-librarian-chef`
3. Create attributes file (for Chef)  
`$ cp nodes/vagrant.json.example nodes/vagrant.json`  
*Note:* You can overwrite attributes by modifying `nodes/vagrant.json` file. This file is ignored in Git so you can be sure that you won't break anything.
4. Create virtual machine attributes file (for Vagrant)  
`$ cp opts.yml.example opts.yml`  
*Note:* Virtual machine managed by Vagrant needs to have 2 network interfaces enabled. First is NAT interface - this is for Vagrant which will be managing the instance with SSH. Second is bridge interface. To make bridge interface work, you should specify (in `opts.yml`) an IP address (outside of your router's DHCP range, otherwise it might not work) and your network adapter's name (non-latin names might not work, that's Vagrant issue). Instead of bridge interface, you may try host-only networking (see [Vagrant documentation](https://docs.vagrantup.com/v2/networking/private_network.html) for more info).
5. Create and provision virtual machine  
`$ vagrant up`  
*Note:* For the first time, it will take a while, because Vagrant will download a base Ubuntu box and after that it will install some software including Git, Ruby, PostgreSQL and so on.

## Managing infrastructure (for virtualized environment)
You can always check virtual machine state by launching VirtualBox GUI but **do not** manage virtual machine with VirtualBox GUI. It's Vagrant duty.

First, change directory to the one you've cloned while creating infrastructure:
`cd ~/Documents/projects/whatever/themis-finals-infrastructure`

### Checking machine state
`$ vagrant status`
### Launching (on powered off/suspended machine)
`$ vagrant up`
### Powering off (on running machine)
`$ vagrant halt`
### Suspending (on running machine)
`$ vagrant suspend`  
*Note:* This command 'freezes' the machine state without powering it off. This can speed up instance startup.
### Resuming (on suspended machine)
`$ vagrant up` or `$ vagrant resume`  
*Note:* This command resumes the machine that was previously suspended.
### Provisioning (on running machine)
`$ vagrant provision`  
*Note:* This command launches Chef to update machine configuration. If you have updated `themis-finals-infrastructure` repository or changed something in `nodes/vagrant.json` file you should definitely provision the machine again.
### Reloading (on running machine)
`$ vagrant reload`  
*Note:* You should run this command if you have updated something in `opts.yml` file.
### Destroying (on any state)
`$ vagrant destroy`  
*Note:* This command will remove VirtualBox virtual machine.
### Additional information
For more information on aforementioned commands, please proceed to [Vagrant documentation](https://docs.vagrantup.com/v2/).
## Contest
Contest configuration and management is done on the guest virtual machine. To connect to the machine, you should run the following commands:
```
$ cd ~/Documents/projects/whatever/themis-finals-infrastructure
$ vagrant ssh
```
Now you have SSH connection to the machine. To edit the files on the VM, you can use SFTP, shared folders, whatever you like most.

### Configuration file
Create configuration file
```
$ cd /var/themis/finals
$ cp config.rb.example config.rb
```
All system settings are stored in `config.rb` file, including network options, teams and services data.

You should specify a host machine's IP address in `network` section. For instance,
``` ruby
network do
    internal '10.0.0.0/24'
    # other settings
end
```

Each team is described in its own section. There should be specified a team alias (for internal use), team name, subnetwork address and game machine's address. For instance,
``` ruby
team 'team_1' do
    name 'Team #1'
    network '10.0.1.0/24'
    host '10.0.1.3'
end
```

Each service is described in its own section. There should be specified a service alias (for internal use) along with service name. For instance,
``` ruby
service 'service_1' do
    name 'Service #1'
end
```

### Service checker
Service checker should be concluded in a separate subfolder in `/var/themis` folder. You can check out the examples in `/var/themis/sample-checker-rb` and `/var/themis/sample-checker-py`.

Service checker is launched with process manager [God](https://github.com/mojombo/god), so you should provide a configuration file in `/var/themis/finals/god.d` (check out samples `sample-checker-py.god` and `sample-checker-rb.god` in that directory). You should specify program's run command, working directory, log file paths and several internal options:  
1. `TUBE_LISTEN` - `themis.service.SERVICE_ALIAS.listen`,  
2. `TUBE_REPORT` - `themis.service.SERVICE_ALIAS.report`,  
where `SERVICE_ALIAS` stands for service alias, which you've specified in `config.rb` file.

Instead of deploying this stuff manually, you can write a Chef cookbook to automate deployment.

### Management
There are some command line tools to manage the contest.
To use these tools, you should ensure you're in `/var/themis/finals` directory.

#### Reset
```
$ cd /var/themis/finals
$ bundle exec rake db:reset
$ bundle exec rake contest:init
```

#### Start processes
```
$ sudo -s
# god -c /var/themis/finals/god.conf
# exit
$
```

#### Schedule start contest
```
$ cd /var/themis/finals
$ bundle exec rake contest:start_async
```
This command does not start contest immediately. Contest will be started automatically when the first flags will have become created.
Assuming virtual machine has an IP address `192.168.163.150`, you can navigate to `http://192.168.163.150/` in your browser to see system's frontend.

#### Pause contest
```
$ cd /var/themis/finals
$ bundle exec rake contest:pause
```

#### Resume contest
```
$ cd /var/themis/finals
$ bundle exec rake contest:resume
```

#### Schedule complete contest
```
$ cd /var/themis/finals
$ bundle exec rake contest:complete_async
```
This does not stop the contest at once. Contest will be stopped automatically in several minutes after when all flags become expired and all scores recalculate.

#### Stop processes
```
$ sudo -s
# god terminate
# exit
$
```

To start new contest, you should reset it at first.

#### Restart your checker's process
Sometimes things get messed up and you end up rewriting your service's checker while a contest is running. Obviously you will need a way to restart checker's process.
```
$ sudo -s
# god status  // find your checker's process name
# god restart my-service-checker
# exit
$
```

### Disabling scoreboard (for team and guest networks)
```
$ cd /var/themis/finals
$ bundle exec rake scoreboard:disable
```

### Attacking
You can attack only from some team's network. To accomplish this, you should run another virtual machine with an appropriate IP address. The following commands should be done on your host machine:
```
$ cd ~/Documents/projects/whatever
$ git clone https://github.com/aspyatkin/themis-sample-node
$ cd themis-sample-node
$ cp opts.yml.example opts.yml
$ vim opts.yml  // specify IP address and network adapter's name
$ vagrant up
```
Vagrant will launch an Ubuntu Desktop instance. Inside a team's machine, you can use `curl` to perform an attack. Assuming `192.168.163.150` is checker system's IP address:
```
$ curl -X POST -v -H "Content-Type: application/json" http://192.168.163.150/api/submit -d "[\"FLAG_HERE\"]"
```
Input is in JSON format, output is in the same JSON format. To discover available result codes, refer to [themis-attack-result](https://github.com/aspyatkin/themis-attack-result) repository.

### Tips
1. Treat your host machine as `internal network` in `config.rb`.
2. To find out some flags to test attacks, open `/var/themis/finals/logs/queue.log`.

### Notes
If you have reached this paragraph, you obviously know that frontend system is not finished yet.

## License
MIT @ [Alexander Pyatkin](https://github.com/aspyatkin)
