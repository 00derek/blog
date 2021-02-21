---
title: "Starting Pi"
date: 2021-02-21T10:47:39-08:00
draft: false
toc: true
images:
tags:
  - pi
---
## Tinkering Raspberry Pi Notes

Started to play around this little thing and I used to set it up to run a Minecraft server for my son.  But now he is moving on to public servers (inevitably) that I am going to take it for experimenting things myself.

## OS
For some reason that my pi no longer able to connect to my wifi, after few tries, I decided to reinstall everything.
- [Etcher](https://www.balena.io/etcher/) to re-image 
- with official image [download](https://www.raspberrypi.org/documentation/installation/installing-images/)
- install zsh
```
sudo apt-get install zsh
```
- set zsh as the default
```
chsh -s /bin/zsh
```
- install ohmyzsh
```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
- [zsh theme](https://github.com/romkatv/powerlevel10k)
## Python 3 installation
- update packages and install needed dependencies
```
> sudo apt-get update
> sudo apt-get screen
> sudo apt-get install -y build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev
```
- download python 3.9.2 and install
```
> screen -S py3
> wget https://www.python.org/ftp/python/3.9.2/Python-3.9.2.tgz
> tar zxvf Python-3.9.2.tgz
> cd Python-3.9.2/
> ./configure --prefix=/usr/local/opt/python-3.9.2 --enable-optimizations
> make -j4
> sudo make altinstall
```
- set it to the default
```
> sudo update-alternatives --list python
> sudo update-alternatives --install /usr/bin/python python /usr/local/opt/python-3.9.2/bin/python3.9 1
```
## To be continued
### jupyter notebook
### ELK
### Kafka/KSQL