---
title: "K3s on RPi notes"
date: 2021-03-24T22:48:42-07:00
draft: false
toc: true
images:
tags:
  - pi
---

## Flash the OS to the SD card
- Download [Raspbian Lite](https://www.raspberrypi.org/downloads/raspbian/)
- Flash to SD card using [Etcher from Balena](https://www.balena.io/etcher/)
- Enabling SSH for your Headless Raspberry Pi, `touch /boot/ssh`
## Customization
- `sudo raspi-config`
  - Set the GPU memory split to 16mb
  - Set the hostname, kubemaster, kube 1 -4 for me
  - Change the password for the pi user
- Disable swap
```
sudo dphys-swapfile swapoff && \
sudo dphys-swapfile uninstall && \
sudo systemctl stop dphys-swapfile && \
sudo systemctl disable dphys-swapfile
```
- Enable container features
  - adding `cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory` to the end of the line of `/boot/cmdline.txt`
  - reboot

- Set Up Keyless SSH Access Between Hosts

## Configure the Network
  - [Reference here](https://downey.io/blog/create-raspberry-pi-3-router-dhcp-server/)
  - set static ip on the home router for the master node
  - Wireless and eth0 Static IP on Raspberry PI by editing `sudo vi /etc/dhcpcd.conf`
  ```
  interface wlan0
  static ip_address=192.168.1.101/24
  static routers=192.168.1.1
  static domain_name_servers=8.8.8.8

  ....
  interface eth0
  static ip_address=10.0.0.1/24
  static domain_name_servers=8.8.8.8
  ```
  - a DHCP server for the cluster
    - Install dnsmasq
    ```
    # Install dnsmasq
    sudo apt install dnsmasq

    # Move it's default config file for safe-keeping
    sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.old
    ```
    - modify `/etc/dnsmasq.conf`
    ```
    # Our DHCP service will be providing addresses over our eth0 adapter
    interface=eth0

    # We will listen on the static IP address we declared earlier
    listen-address=10.0.0.1

    # My cluster doesn't have that many Pis, but since we'll be using this as
    # a jumpbox it is nice to give some wiggle-room.
    # We also declare here that the IP addresses we lease out will be valid for
    # 12 hours
    dhcp-range=10.0.0.32,10.0.0.128,12h

    # Decided to assign static IPs to the kube cluster members
    # This would make it easier for tunneling, certs, etc.
    dhcp-host=db:c6:12:f2:7b:16,10.0.0.11
    dhcp-host=db:c6:12:f3:7a:e3,10.0.0.12
    dhcp-host=db:c6:12:f4:7a:c7,10.0.0.13
    dhcp-host=db:c6:12:f5:7a:f2,10.0.0.14
    # This is where you declare any name-servers. We'll just use Google's
    server=8.8.8.8
    # server=8.8.4.4

    # Bind dnsmasq to the interfaces it is listening on (eth0)
    bind-interfaces

    # Never forward plain names (without a dot or domain part)
    domain-needed

    # Never forward addresses in the non-routed address spaces.
    bogus-priv

    # Use the hosts file on this machine
    expand-hosts

    # Useful for debugging issues
    # log-queries
    # log-dhcp
    ```
  - Give clustered Pis static IP addresses, `10.0.0.1` for the master node, `10.0.0.11 - 14` for the worker nodes
  
  ## Create the k3s cluster
  - On master ` curl -sfL https://get.k3s.io | sh -`
    - check the status `sudo systemctl status k3s`
  - grep the key from the master `sudo cat /var/lib/rancher/k3s/server/node-token`
  - Join worker nodes, on each node `kube1- kube4`
    - `export K3S_URL="https://10.0.0.1:6443"`
    - `export K3S_TOKEN=""`
    - install `curl -sfL https://get.k3s.io | sh -` (because of the env. variable, the script installs the agent)
  - List nodes -- `kubectl get node -o wide`
  ```
  NAME         STATUS   ROLES                  AGE   VERSION        INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION   CONTAINER-RUNTIME
kube3        Ready    <none>                 39m   v1.20.4+k3s1   10.0.0.13       <none>        Raspbian GNU/Linux 10 (buster)   5.4.83-v7l+      containerd://1.4.3-k3s3
kube4        Ready    <none>                 39m   v1.20.4+k3s1   10.0.0.14       <none>        Raspbian GNU/Linux 10 (buster)   5.4.83-v7l+      containerd://1.4.3-k3s3
kube1        Ready    <none>                 50m   v1.20.4+k3s1   10.0.0.11       <none>        Raspbian GNU/Linux 10 (buster)   5.4.83-v7l+      containerd://1.4.3-k3s3
kube2        Ready    <none>                 41m   v1.20.4+k3s1   10.0.0.12       <none>        Raspbian GNU/Linux 10 (buster)   5.4.83-v7l+      containerd://1.4.3-k3s3
kubemaster   Ready    control-plane,master   52m   v1.20.4+k3s1   192.168.1.101   <none>        Raspbian GNU/Linux 10 (buster)   5.4.83-v7l+      containerd://1.4.3-k3s3
pi@kube
```
  ## To be continued
  - try k3sup?
  - try arkade
  - Deploy a microservice, OpenFaaS, kafka, ?
  - hosted jupyter notebook?


  ## References
  - https://downey.io/blog/create-raspberry-pi-3-router-dhcp-server/
  - https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/
  - https://github.com/alexellis/k3sup
  - https://github.com/alexellis/arkade
  - https://help.dreamhost.com/hc/en-us/articles/216499537-How-to-configure-passwordless-login-in-Mac-OS-X-and-Linux
  - https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/
  - https://oliver-hu.medium.com/set-up-a-kafka-cluster-with-raspberry-pi-2859005a9bed
  
