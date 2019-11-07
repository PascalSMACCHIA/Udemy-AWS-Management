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

Or the second approah, we can create tje pass.yml file specifying a hashed password file.
 
<PRE>
openssl rand -base64 2048 > vault.pass
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
</PRE>

It's necessary to use this file when the playbook is launched.

<PRE>
ansible-playbook playbook.yml --vault-password-file vault.pass
</PRE>
Please change the value "id" to define the id name for your instance.

Edit the pass.yml file and create the keys global constants
Create the variables ec2_access_key and ec2_secret_key and set the values gathered after user creation (IAM).
<PRE>
ansible-vault edit group_vars/all/pass.yml 
Vault password:
ec2_access_key: AZZERVGYLL56ABBBBBBBBBBBB                                      
ec2_secret_key: aRT(tg6YUIIOPPPPPirhf
</PRE>


Exemple: id: "Webserver01"

Directory structure:
<PRE>
➜  AWS_Ansible tree
.
├── group_vars
│   └── all
│       └── pass.yml
└── playbook.yml
2 directories, 2 files
</PRE>


The file pass.yml is your credential information to access on your AWS cloud, which is protected by vault.

Begin by:

ansible-playbook  playbook.yaml --vault-password-file ~/.ssh/vault.pass
ansible-playbook  playbook.yaml --vault-password-file ~/.ssh/vault.pass --tags create_ec2
