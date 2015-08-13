# Themis Finals Guidelines
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
- [themis-finals](https://github.com/aspyatkin/themis-finals) - Main repository of the project, contains checking system backends (Ruby) as well as frontend (JavaScript ES6).
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
4. Ruby 2.2.2 - better with [rbenv](https://github.com/sstephenson/rbenv) and [ruby-build](https://github.com/sstephenson/ruby-build)
5. *for Windows hosts* unix shell. You might want to try bare [Cygwin](http://cygwin.org/) or [Babun](http://babun.github.io/) (Ruby can be installed with Babun's `pact`).
6. Enough computational resources and disk space to get 2-3 virtual machines run simultaneously.

### Check prerequisites
1. `vagrant version` should report something like `Installed version: 1.7.4`.
2. `ruby -v` should report something like `ruby2.2.2p95`.

### Creating infrastructure
**Note:** `$` indicates nothing more that a shell prompt.

1. Install [Bundler](https://github.com/bundler/bundler)  
`$ gem install bundler`  
*Note:* You might need to run `rbenv rehash` after installation if Ruby binaries are managed by `rbenv` (installed dependency `bundler` exposes a shell utility).
2. Get [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)  

        $ mkdir ~/Documents/projects/whatever
        $ cd ~/Documents/projects/whatever
        $ git clone https://github.com/aspyatkin/themis-finals-infrastructure
        $ cd themis-finals-infrastructure

3. Install dependencies  
`$ bundle`  
*Note:* You might need to run rbenv rehash after running command if Ruby binaries are managed by rbenv (installed dependency `librarian-chef` exposes a shell utility).
4. Install cookbooks  
`$ librarian-chef install`
5. Create attributes file (for Chef)  
`$ cp nodes/vagrant.json.example nodes/vagrant.json`  
*Note:* You can overwrite attributes by modifying `nodes/vagrant.json` file. This file is ignored in Git so you can be sure that you won't break anything.
6. Create virtual machine attributes file (for Vagrant)  
`$ cp opts.yml.example opts.yml`  
*Note:* Virtual machine managed by Vagrant needs to have 2 network interfaces enabled. First is NAT interface - this is for Vagrant which will be managing the instance with SSH. Second is bridge interface. To make bridge interface work, you should specify (in `opts.yml`) an IP address (outside of your router's DHCP range, otherwise it might not work) and your network adapter's name (Non-latin names might not work, that's Vagrant issue).
7. Create and provision virtual machine  
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
*Note:* This command resumes that machine that was previously suspended.
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