# Udemy-ansible-advanced


This playbool will be used to create a new Instances on AVS cloud.

To create one instance, edit the file "playbook.yaml"

Please change the value "id" to define the id name for your instance.

Exemple: id: "Webserver01"

Directory structure
➜  AWS_Ansible tree
.
├── group_vars
│   └── all
│       └── pass.yml
└── playbook.yml
2 directories, 2 files

The file pass.yml is your credential information to access on your AWS cloud, which is protected by vault.

Begin by:

ansible-playbook  playbook.yaml --vault-password-file ~/.ssh/vault.pass
ansible-playbook  playbook.yaml --vault-password-file ~/.ssh/vault.pass --tags create_ec2
