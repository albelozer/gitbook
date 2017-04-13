# Building device configurations from Jinja2 templates

This sounds not so complicated but initially you must understand several Ansible terms and actions.
1. [Inventory](#inventory)
2. [Roles](#roles)
3. [Templates](#jinja2)
4. [Tasks](#tasks)
5. [Vars](#vars)
6. [Playbooks](#playbooks)

## <a id="inventory"></a>Ansible inventory

## <a id="roles"></a>Ansible roles
Use **ansible-galaxy init** *role-name* for creating folder structure and files for a new role named role-name. Use *--offline* option if you don't have connectivity to Internet. Otherwise you'll get en error:
```
ERROR! The API server (https://galaxy.ansible.com/api/) is not responding, please try again later.
```
Example:
```
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

## <a id="jinja2"></a>Jinja2 templates

## <a id="tasks"></a>Ansible tasks

## <a id="vars"></a>Ansible vars

## <a id="playbooks"></a>Ansible playbooks
