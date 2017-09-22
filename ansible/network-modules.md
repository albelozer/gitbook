# Ansible modules

Ansible provides a lot of network modules to work with different vendors and products. Here I will focus on several built-in modules and some additional.

## wait\_for

The first one is not a network module but I use it for checking device reachability. The playbook is as follows

```yaml
---
- name: Checking SSH connectivity
    wait_for: host={{ inventory_hostname }} port="{{ port | default(22) }}"  timeout=5
```

I use this playbook from another one. It's better to check the reachability of the target host at the very beginning of a play. If the host is not reachable all following tasks will skip it.

[Link](http://docs.ansible.com/ansible/latest/wait_for_module.html)

##
