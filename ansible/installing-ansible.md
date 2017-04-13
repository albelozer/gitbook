# Installing ansible

You can use any host to install Ansible. I used [vagrant](/vagrant/README.md) Ubuntu VM to run deploy ansible. Inside Ubuntu we can use apt-get for installing Ansible.
```sh
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```
The question is what are you going to manage with Ansible (managed hosts or devices) and from what control host.

As for now I use Ansible from Vagrant Ubuntu VM. All Ansible configuration, playbooks and tasks are pushed to a git. If I want to move Ansible to another host this will be as easy as clone repo from git.

I don't have managed hosts yet (except AWS VM) and almost all Ansible operations are performed locally.

## Links
[Ansible Installation](http://docs.ansible.com/ansible/intro_installation.html)
