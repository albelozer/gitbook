# Ansible modules

Ansible provides a lot of network modules to work with different vendors and products. Here I will focus on several built-in modules and some additional.

## wait\_for

The first one is not a network module but I use it for checking device reachability.

```yaml
---
- name: Checking SSH connectivity
    wait_for: host={{ inventory_hostname }} port="{{ port | default(22) }}"  timeout=5
```

I should explain `port="{{ port | default(22) }}"`. This is Jinja2 filter. First it checkes the variable port. If it is defined ansible will use it. If not it will use the default value 22.

It's better to check the reachability of the target host at the very beginning of a play. If the host is not reachable all following tasks will skip it. I call this playbook from another one.

```yaml
---
# main.yml for the role

&&&- include: "playbooks/check_connectivity.yml"

- include: "playbooks/{{ platform }}_backup.yml" # IOS config backup
  when: backup # var is defined for group_vars/csr1kv

- include: "playbooks/{{ platform }}_facts.yml"
  when: facts # var is defined for group_vars/csr1kv
```

[Link to wait_for at ansible docs](http://docs.ansible.com/ansible/latest/wait_for_module.html)

As you can see I call several other playbooks in the example above. The name of playbook has a Jijna variable {{ platform }} inside. Why do I do this? To make my main playbook universal.

Let's look at other modules from playbooks which I mentioned above.

## ios_config, iosxr_config, nxos_config

I use these modules for several purposes. First is not a general usage. I backup configuration. The filename for the playbooks is derived from {{ platform }} + "\_backup.yml". {{ platform }} var is defined as a group var. There are several ways to define variable in Ansible. But we'll talk about it in another article. Here are two playbooks:

```yaml
# file playbooks/cisco_ios_backup.yml
---
- name: Backup configuration
  ios_config:
    backup: yes
  tags:
    - backup
```

and

```yaml
# file playbooks/cisco_iosxr_backup.yml
---
- name: Backup configuration
  iosxr_config:
    backup: yes
  tags:
    - backup
```

But the original purpose of these modules is to configure devices. There are three ways of doing this.

### Line by line configuration

This is the slowest aproach.

```yaml
- name: Add ACLs MCast
  notify:
    - Write log
    - Save configuration
  ios_config:
    lines:
      - permit 10.201.2.0 0.0.0.255
      - permit 10.201.12.0 0.0.0.255
    parents: ['ip access-list standard BUILT-IN-ACL']
    before: ['no ip access-list standard BUILT-IN-ACL']
  tags:
    - config
```

Let's examine line by line.

- `name` - just a name, nothing more. By the way you can use vars in this name.
- `notify` — this is the call of handlers with names "Write log" and "Save configuration".
- `ios_config` - call Ansible module.
- `lines` - configuration lines.
- `parents` - defines the level where `lines` are executed.
- `before` - defines a command to run before applying `lines` at `parents` level.
- `tags` - Ansible can tag tasks. Lates you can call your playbook and filtes tasks by tags.
