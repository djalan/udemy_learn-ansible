# Ansible for the Abosulte Beginner - Hands-on - DevOps
https://www.udemy.com/course/learn-ansible/


## 3) Ansible Inventory
- Agentless!
    - Linux: SSH
    - Windows: Powershell Remoting

### ssh failed
`ansible target1 -m ping -i inventory.txt`
#### Problem
```
target1 | FAILED! => {
    "msg": "to use the 'ssh' connection type with passwords or pkcs11_provider, you must install the sshpass program"
}
```
#### Solution
`dnf install sshpass`


## 4) YAML
- Dict in different order will be Equal
- List are ORDERED collection, items must be in same order to be Equal


## 5) Ansible Playbooks
### Speed up Gathering Facts
```bash
dnf install redis python3-redis
```
### Inventory caching
Only in JSON
### Idempotency
1) copy 1 says `changed=1`
2) copy 2 says `ok=1` NOTHING to do, already there, thus idempotent


## 6) Ansible Modules
- Cloud modules exist
    - AWS
    - Google
    - etc.

### Service
Idempotency: we should be able to run the same playbook over and over again
- `state=started`
    - `start` is bad for idempotency
    - `started` will check if already started, or starts the service
- Ansible is better than script when run multiple times
    - multiple lines with bash: `echo nameserver 8.8.8.8 >> /etc/resolv.conf`
    - one line always ensured `lineinfile: path=/etc/resolv.conf line='nameserver 8.8.8.8`

We want to avoid

```
nameserver 1.2.3.4
nameserver 8.8.8.8
nameserver 8.8.8.8
nameserver 8.8.8.8
```


## 7) Variables
- Jinja2 templating
- variables in different files
    - inventory
    - target1.yaml: `/etc/ansible/host_vars/target1.yaml`
    - group.yaml: `/etc/ansible/group_vars/all.yaml`


## 8) Conditionals
### DNF install is not working
Problem: this command has to be run under the root user.

Solution: privilege escalation
#### Play: per task
1) in task: `become: true`
2) in host_vars/target1: `ansible_become_pass: password` 
#### cli: all tasks
- `ansible-playbook -b -K dnf_playbook.yaml`
    - `-b` become root
    - `-k` ask-become-password
#### ansible.cfg: all tasks
```ini
[privilege_escalation]
become = yes
;become_method = sudo   ; default=sudo
;become_user = root     ; default=root
become_ask_pass = True
```
### Playbook tasks can be grouped in `block`
- To `become` for certain tasks
- Handling errors with `rescue` and `always` and `handlers`
### Tricks
- use `verbose -vvv` on the CLI to find keys of dictionaries


## 9) Loops
- `loop` is new
- `with_items` is the old way
    - `with_file`
    - `with_url`
    - `with_k8s`


## 10) Roles
Group tasks under a role (ex: 4 tasks to install MySQL)

Download roles @ https://galaxy.ansible.com

`ansible-galaxy` init creates a special directory structure to create the role

`ansible-galaxy collection install nginxinc.nginx_core`

`ansible-galaxy collection install nginxinc.nginx_core -p ./roles`
