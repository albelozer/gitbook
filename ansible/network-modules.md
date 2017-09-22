# Ansible modules

Ansible provides a lot of network modules to work with different vendors and products. Here I will focus on several built-in modules and some additional.

## wait\_for

The first one is not a network module but I use it for checking device reachability.

```yaml
---
- name: Checking SSH connectivity
    wait_for: host={{ inventory_hostname }} port="{{ port | default(22) }}"  timeout=5
```

It's better to check the reachability of the target host at the very beginning of a play. If the host is not reachable all following tasks will skip it. I call this playbook from another one.

```yaml
---
# main.yml for the role

- include: "playbooks/check_connectivity.yml"

- include: "playbooks/{{ platform }}_backup.yml" # IOS config backup
  when: backup # var is defined for group_vars/csr1kv

- include: "playbooks/{{ platform }}_facts.yml"
  when: facts # var is defined for group_vars/csr1kv
```

[Link](http://docs.ansible.com/ansible/latest/wait_for_module.html)

##
