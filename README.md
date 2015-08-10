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

## Installation (for virtualized environment)
### Prerequisites (host machine)
1. [VirtualBox 5.0](https://www.virtualbox.org/wiki/Downloads)
2. [Vagrant 1.7.4](https://www.vagrantup.com/downloads.html)
3. Ruby 2.2.2 - better with [rbenv](https://github.com/sstephenson/rbenv) and [ruby-build](https://github.com/sstephenson/ruby-build)
4. *for Windows hosts* unix shell. You might want to try bare [Cygwin](http://cygwin.org/) or [Babun](http://babun.github.io/) (Ruby can be installed with Babun's `pact`).
5. Enough compute resources and disk space to get 2-3 virtual machines run simultaneously.
