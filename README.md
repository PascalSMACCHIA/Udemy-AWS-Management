# Udemy-ansible-advanced


These playbook and roles will be used to create a new Instances on AWS Infrastructure and same time enable the services learnt  on the course "Ansible Advanced".

# AWS instances

## Create Ansible Vault file to store the AWS Access and Secret keys.

<PRE>
ansible-vault create group_vars/all/pass.yml
New Vault password:
Confirm New Vault password:
</PRE>

In thise case, the password must be provided when we will execute the playbook. 

Or the second approah, we can create the pass.yml file specifying a hashed password file.
 
<PRE>
openssl rand -base64 2048 > vault.pass
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
</PRE>

It's necessary to use this file when the playbook is launched.

<PRE>
ansible-playbook playbook.yml --vault-password-file vault.pass
</PRE>

Edit the pass.yml file and create the keys global constants

Create the variables ec2_access_key and ec2_secret_key and set the values gathered after user creation (IAM).

<PRE>
ansible-vault edit group_vars/all/pass.yml 
Vault password:
ec2_access_key: AZZERVGYLL56ABBBBBBBBBBBB                                      
ec2_secret_key: aRT(tg6YUIIOPPPPPirhf
</PRE>


Directory structure:
<PRE>
➜  AWS_Ansible tree

├── group_vars
│   └── all
│       └── pass.yml
└── playbook.yml
2 directories, 2 files
</PRE>


Open the playbook.yml file and adapt the varaibles for your case.


The Playbook by default will be execute just to collect the information on AWS to create one instance execute with he tag "create_ec2".

## Running Ansible

<PRE>
ansible-playbook playbook.yml --ask-vault-pass
</PRE>

or

<PRE>
ansible-playbook  playbook.yaml --vault-password-file ~/.ssh/vault.pass
</PRE>


## Create the instance

<PRE>
ansible-playbook playbook.yml --ask-vault-pass --tags create_ec2
</PRE>

## Get the public DNS

<PRE>
ansible-playbook playbook.yml --ask-vault-pass
</PRE>

# Enable the services on the VMs

It's necessary to provision 3 VMs, including 2 VMs with Mysql Database, Python and Flask and the last one HaProxy for the balancer. 

Directory Structure:
<PRE>
➜  AWS_Ansible tree
├── Deployment_DB_Web.yaml
├── Deployment_HaProxy.yaml
├── group_vars
│   └── all
│       └── pass.yml
├── host_vars
│   ├── haproxy01.yaml
│   ├── web1
│   └── web2
├── inventory
├── playbook.yaml
├── README.md
└── roles
    ├── flask_web
    │   ├── tasks
    │   │   └── main.yml
    │   └── templates
    │       └── app.py
    ├── geerlingguy.haproxy
    │   ├── defaults
    │   │   └── main.yml
    │   ├── handlers
    │   │   └── main.yml
    │   ├── LICENSE
    │   ├── meta
    │   │   └── main.yml
    │   ├── README.md
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   │   └── haproxy.cfg.j2
    │   └── tests
    │       ├── README.md
    │       └── test.yml
    ├── Get-GitHub-Repository
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── README.md
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   ├── tests
    │   │   ├── inventory
    │   │   └── test.yml
    │   └── vars
    │       └── main.yml
    ├── mysql_db
    │   └── tasks
    │       └── main.yml
    └── python
        └── tasks
            └── main.yml
 </PRE>

## Create Database, Python and Flask server

Launch the playbook "Deployment_DB_Web.yaml" to initialise the environment. 

The variables are in the files vars_hosts/web[1-2]. The playbook use the roles:
* python
* mysql_db
* Get-GitHub-Repository (get the app.py on the GitHub Repository)
* flask_web

the command is:
<PRE>
ansible-playbook -i inventory Deployment_DB_Web.yaml  --vault-password-file ~/.ssh/vault.pass
</PRE>

## Create the HaProxy service

The loadbalancing is create with role "geerlingguy.haproxy".

The variables are on the host_vars/haproxy01.yaml to configure the HaProxy service

<PRE>
 haproxy_backend_servers:
    - name: app1
      address:  172.31.20.93:5000
    - name: app2
      address:  172.31.29.122:5000
</PRE>

## Dynamic Inventory

For AWS, the ec2.py file is the program wich be used to create the dynamic inventory. the file is on this [link](https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.py) too

<PRE>
ansible -i ec2.py all -m ping -u ubuntu  --key-file ~/.ssh/my_aws --vault-password-file   ~/.ssh/vault.pass
</PRE>

<PRE>
 --> REsult:
 18.224.136.48 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
13.58.34.207 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
18.223.102.138 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
</PRE>
