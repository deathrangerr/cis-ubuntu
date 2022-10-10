

# wakanda

#### To run the playbook from the master, make sure to share ~/.ssh/id_rsa.pub key of master to node in ~/.ssh/authorized_keys


#### Create a shell script and paste the following given content in the file
> shell .sh
```
#!/bin/bash


node_ip=
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

#### Change in services userdata please refer below
```
UserData:
        "Fn::Base64":
          !Sub |
            #!/bin/bash -xe
            wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb -O /tmp/amazon-cloudwatch-agent.deb
            dpkg -i /tmp/amazon-cloudwatch-agent.deb
            sleep 60
            apt update -y
            export DEBIAN_FRONTEND=noninteractive

            #Install Dependencies for CFN-Bootstrap
            wget https://s3.amazonaws.com/cloudformation-examples/aws-cfn-bootstrap-py3-latest.tar.gz
            python3 -m easy_install aws-cfn-bootstrap-py3-latest.tar.gz

            apt upgrade -y

            cfn-init -v --configsets vision \
                     --verbose --stack ${AWS::StackName} \
                     --resource ServiceLaunchConfiguration \
                     --region ${AWS::Region}
            systemctl enable codedeploy-agent
            systemctl start codedeploy-agent
            #./home/ubuntu/verify_bootup.sh

            cfn-signal --exit-code $? \
                       --stack ${AWS::StackName} \
                       --resource ServiceAutoScalingGroup \
                       --region ${AWS::Region}
```
#### A inventory file to store your host [ansible node dns or ip ]
> inven
```
[client]
server1 ansible_host=
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
    - wakanda
```
