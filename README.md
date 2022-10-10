
#### To run the playbook from the master, make sure to share ~/.ssh/id_rsa.pub key of master to node in ~/.ssh/authorized_keys

### Use the following command and checkout this repo

> git clone https://github.com/deathrangerr/cis-ubuntu.git

#### Create a shell script and paste the following given content in the file
> shell .sh
```
#!/bin/bash


node_ip=172.3.113.151
cat <<EOF >playbook.yml
---

- name: Harden Server
  hosts: client
  become: yes
  roles:
    - wakanda
EOF

cat <<EOF >inven
[client]
server1 ansible_host=$node_ip
[client:vars]
ansible_user=ubuntu
EOF

ansible-playbook -i inven playbook.yml



#ssh ubuntu@$node_ip export DEBIAN_FRONTEND=noninteractive
#ssh ubuntu@$node_ip sudo apt update -y
#ssh ubuntu@$node_ip sudo apt upgrade -y
#ssh ubuntu@$node_ip sudo apt install java-common -y
#ssh ubuntu@$node_ip wget -P /home/ubuntu wget https://d3pxv6yz143wms.cloudfront.net/8.222.10.1/java-1.8.0-amazon-corretto-jdk_8.222.10-1_amd64.deb
#ssh ubuntu@$node_ip sudo dpkg --install /home/ubuntu/java-1.8.0-amazon-corretto-jdk_8.222.10-1_amd64.deb
#ssh ubuntu@$node_ip sudo rm -rf /home/ubuntu/*
```


#### A inventory file to store your host [ansible node dns or ip ]
> inven
```
[client]
server1 ansible_host=172.31.83.99
[client:vars]
ansible_user=ubuntu
```
#### A playbook to run the role
> playbook.yml
```
---

- name: Harden Server
  hosts: client
  become: yes
  roles:
    - cis-ubuntu
```
