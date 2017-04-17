# Building device configurations from Jinja2 templates

This sounds not so complicated but initially you must understand several Ansible terms and actions.
1. [Inventory](#ansible-inventory)
2. [Roles](#ansible-roles)
3. [Templates](#jinja2-templates)
4. [Tasks](#ansible-tasks)
5. [Vars](#ansible-vars)
6. [Playbooks](#ansible-playbooks)

## Ansible inventory

## Ansible roles
Use **ansible-galaxy init** *role-name* for creating folder structure and files for a new role named role-name. Use *--offline* option if you don't have connectivity to Internet. Otherwise you'll get en error:

```sh
ERROR! The API server (https://galaxy.ansible.com/api/) is not responding, please try again later.
```

Example:
```sh
cisco@server-1:~/anstest$ ansible-galaxy init csr1k --offline
&&&- csr1k was created successfully
```
You'll have the following folder tree and default files:
```sh
cisco@server-1:~/anstest$ tree roles/
└── csr1k
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```

## Jinja2 templates

## Ansible tasks

## Ansible vars

## Ansible playbooks
