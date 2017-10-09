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

&&&- include_tasks: "playbooks/check_connectivity.yml"

- include_tasks: "playbooks/{{ platform }}_backup.yml"
  when: backup # var is defined for group_vars/group_name/vars

- include_tasks: "playbooks/{{ platform }}_facts.yml"
  when: facts # var is defined for group_vars/group_name/vars
```

[Link to wait_for at ansible docs](http://docs.ansible.com/ansible/latest/wait_for_module.html)

> **Note** `include_tasks` parameter has arrived in Ansible 2.4. Now there are static and dynamic includes. Details [here](https://docs.ansible.com/ansible/devel/playbooks_reuse.html) and [here](https://docs.ansible.com/ansible/devel/playbooks_reuse_includes.html).

As you can see I call several other playbooks in the example above. The name of playbook has a Jijna variable `{{ platform }}` inside. Why do I do this? To make my main playbook universal and automatically change the name of playbook based on variable `{{ platform }}`. Here we can have cisco_ios_backup.yml or cisco_iosxr_backup.yml. Both of them are predefined in playbooks folder.

Let's look at other modules from playbooks which I mentioned above.

## ios_config, iosxr_config, nxos_config

I use these modules for several purposes. First is not a general usage. I backup configuration. The filename for the playbooks is derived from `{{ platform }}` + "\_backup.yml". `{{ platform }}` var is defined as a group var. There are several ways to define variable in Ansible. But we'll talk about it in another article. Here are two playbooks:

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

### 1. Line by line configuration

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
- `notify` — this is the call of **handlers** with names "Write log" and "Save configuration".
- `ios_config` - call Ansible module.
- `lines` - configuration lines.
- `parents` - defines the level where `lines` are executed.
- `before` - defines a command to run before applying `lines` at `parents` level.
- `tags` - Ansible can tag tasks. Later you can call your playbook and filter tasks by tags.

These are the handlers:

```yaml
---
- name: Save IOS configuration
  ios_config:
    save_when: always

- name: Write log
  ios_command:
    commands:
      - send log Changes from ansible
```

You can define handlers at the end of the play. But I prefer using role handlers. By default Ansible will use the path `roles/role\_name/handlers/main.yml`.

### 2. Configuration with predefined configuration file

The easiest way.

```yaml
- name: Add ACLs from file
  ios_config:
    src: "acl.txt"
  save_when: modified
```

- `save_when` is a new feature of this module arrived in Ansible 2.4. Before it has only `save` with `yes` or `no` as possible values. Now with `save_when` it has three options: `never`, `always` and `modified`.

### 3. Configuration with Jinja2 templates

The most advanced and fastest way. Looks like Ansible is optimized to work with j2 templates.

```yaml
---
- name: Configure IOS devices from hosts_var with Jinja2 templates
  ios_config:
    provider: "{{ provider }}" # defined in group_vars/*/. Contains host, username, password
    src: "{{ current_role }}.j2" # Main template. Can include other more specific templates
    save_when: modified
  notify:
    - Write log # Handler writes message in syslog
  tags: config
  register: config # registering for further use
```

You may want to `register` the output in a `config` var for the later usage. For example if I know that configuration was changed by a task I can perform verification or something.
