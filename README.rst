Macondo: a Raspberry pi cluster
===============================

Setup Raspberry Pis
--------------------

* Discover the devices.  Replace `172.16.0.0` by your own network address

.. code:: bash

    sudo nmap -sn 172.16.0.0-255 |grep rasp -i  -B 2

TODO
-----

* Ansible 
* Docker
* Spark 


Adhoc commands
------------------
.. code:: bash

    ansible all -m ping -i inventory.cfg -u macondo


Bootstrap
----------

.. code:: bash

    ansible-playbook playbooks/create_user.yml -i inventory.cfg --user pi --ask-pass  -e user_name=macondo  -e ssh_key=FULL_PATH_TO_ID_RSA_PUB 
    ansible-playbook playbooks/remove_user.yml -i inventory.cfg --user macondo --ask-become-pass -e user_name=pi
    ansible-playbook playbooks/change_hostname.yml -i "192.168.0.178," --user macondo --ask-become-pass -e hostname=remedios 


Credits
--------
- https://github.com/garthvh/ansible-raspi-playbooks
- https://github.com/vicchi/ansible-pi-lockdown
