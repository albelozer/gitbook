# Connecting Ansible to Vagrant VM on localhost

The short manual of how to connect from locally installed Ansible to the Vagrant VMs running localy on Mac OS X.

1. Use **vagrant ssh-config** command to identify ssh ports of runnins VMs. Details may be found [here](http://automation.ipspace.net/Example:Creating_Ansible_Inventory_from_Vagrant_SSH_Configuration).

```bash
ABELOZER-M-9202:vagrant abelozer$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
&&&  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/abelozer/Documents/vagrant/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

2. Change the hosts file for Ansible project, add ssh port (2222).

```bash
ABELOZER-M-9202:ansible abelozer$ pwd
/Users/abelozer/Documents/ansible
ABELOZER-M-9202:ansible abelozer$ cat hosts
[AWS]
35.166.112.128

[local]
&&&localhost:2222
```

3. Add your id_rsa.pub key to the vagrant VM authorized_keys file.

4. Check the rechability from ansible.

```bash
ABELOZER-M-9202:ansible abelozer$ ansible localhost -i hosts -u vagrant -m ping
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
