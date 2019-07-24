# Ansible
### command:
run playbook:
`ansible-playbook playbook.yml  -i inventory.yaml`

Inventory file in yaml:
```
all:
  hosts:
    host1:
      vars:
        ansible_ssh_common_args: "-F ~/.ssh/config host1"
    host2:
      vars:
        ansible_ssh_common_args: "-F ~/.ssh/config host2"
    host3:
      vars:
        ansible_ssh_common_args: "-F ~/.ssh/config host3"
```

Playbook for create user and authorized key:
```
- hosts: "all"
  connection: "ssh"
  vars:
    users:
    - "user1"
  tasks:
  - name: "Create user accounts"
    become: true
    become_user: root
    user:
      name: "{{ item }}"
      groups: "admin,www-data"
    with_items: "{{ users }}"
  - name: "Add authorized keys"
    become: true
    become_user: root
    authorized_key:
      user: "{{ item }}"
      key: "{{ lookup('file', 'files/'+ item + '.key.pub') }}"
    with_items: "{{ users }}"
  - name: "Allow admin users to sudo without a password"
    become: true
    become_user: root
    lineinfile:
      dest: "/etc/sudoers" # path: in version 2.3
      state: "present"
      regexp: "^%admin"
      line: "%admin ALL=(ALL) NOPASSWD: ALL"
```
