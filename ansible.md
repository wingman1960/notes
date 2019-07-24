# Ansible
### command:
#### run playbook:
`ansible-playbook playbook.yml  -i inventory.yaml`

inventory file in yaml:
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
