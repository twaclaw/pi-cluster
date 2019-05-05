# Macondo: Yet Another Raspberry Pi Cluster

<p align="center">
<img src="images/raspberries.jpg" width="800"> 
</p>

There are some practical reasons motivating this exercise;
however, the main one is just for fun... Why not? 


## Hardware

* 5 Raspberry Pi model 3 Model B+
* 5 32 GB micro SD cards (I used SanDisk ultra class 10, manufacturer number: SDSQUAR-032G-GN6TA)
* A 8-port 100Mbps switch (I used [this one](https://www.conrad.com/p/renkforce-network-switch-8-ports-100-mbps-1483812))
* A power supply with enough wattage (I used [this 60W one](https://www.anker.com/products/A2133111))
* Micro-USB and ethernet cables
* Some sort of casing for the RPis cluster

<p align="center">
<img src="./images/cluster.jpg"  height="450">
</p>

## Setup

### Bootstrap and Configuration

Assuming you have flashed the SD cards (I used [raspbian](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md)),
enabled ssh (create an empty file called "ssh" in the boot partition: `touch /mount-point/boot/ssh`, and
powered-up the cluster, you can start with
the configuration. I use Ansible to configure the devices.

* Discover the devices ip addresses. Here, I assume your network IP address is 172.16.0.0/24.  You can create an Ansible [inventory.cfg](ansible/inventory.cfg) with those IP addresses.

    ```bash
    sudo nmap -sn 172.16.0.0-255 |grep rasp -i  -B 2
    ```
* The ansible playbooks are located in the ansible folder ( `cd ansible` )
* Create a new user (e.g. `macondo`), deploy an ssh public key; finally, delete the old user `pi`:
   
    ```bash
    ansible-playbook playbooks/create_user.yml -i inventory.cfg --user pi --ask-pass  -e user_name=macondo  -e ssh_key=FULL_PATH_TO_ID_RSA_PUB 
    
    ansible-playbook playbooks/remove_user.yml -i inventory.cfg --user macondo --ask-become-pass -e user_name=pi
    ```
* Optionally, you can change the devices hostnames. This playbook has to be applied on each device, for instance:
    
    ```bash
    ansible-playbook playbooks/change_hostname.yml -i "172.16.0.178," --user macondo --ask-become-pass -e hostname=remedios 
    ```

  In my case the nodes are called: Ursula, Amaranta, Rebeca, Pilar and Remedios.

## Additional Ansible Scripts

### Shutdown and Reboot
```bash
ansible-playbook playbooks/shutdown.yml -i inventory.cfg --user macondo --ask-become-pass

ansible-playbook playbooks/reboot.yml -i inventory.cfg --user macondo --ask-become-pass
```

### Adhoc commands

For instance:
```bash
ansible all -m ping -i inventory.cfg -u macondo
```

## Credits
- https://github.com/garthvh/ansible-raspi-playbooks
- https://github.com/vicchi/ansible-pi-lockdown
