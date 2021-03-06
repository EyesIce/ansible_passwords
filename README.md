# ansible_passwords

This is an ansible example for running command using multiple hostgroups with different credentials.

In hosts file are definied the hosts group

Within group_vars directory there are files named like hosts group. Those files cointain the variable ansible_password. That variable is set to the password which ansible server use to connect via ssh to the hosts.

In roles directory will define each roles of main playbook. In that case is empity because we execute 2 simple operations in test.yml

test.yml is the main playbook in which are defined hosts, main tasks and roles. This is the file we have to run

To test this playbook clone the entire directory and run the following command:
ansible-playbook /dir/of/your/playbook/test.yml


With test.yml like following:
---
- name: test script
  hosts: all
  remote_user: root
  tasks:
  - name: get date from server
    command: date
    register: out
  - name: get uptime from server
    command: uptime
    register: res
  - debug: var=out.stdout_lines
  - debug: var=res.stdout_lines

You need to set to skippy the variable stdot_callback within /etc/ansible/ansible.cfg. In this way you obtain an ouput of each executed command like this:

TASK [debug] **********************************************************************************************************************************************************
ok: [host1] => {
    "out.stdout_lines": [
        "Thu Feb 14 12:01:56 CET 2019"
    ]
}
ok: [host2] => {
    "out.stdout_lines": [
        "Thu Feb 14 13:01:55 CET 2019"
    ]
}

TASK [debug] **********************************************************************************************************************************************************
ok: [host1] => {
    "res.stdout_lines": [
        " 12:01:56 up 47 min,  1 user,  load average: 0.14, 0.05, 0.05"
    ]
}
ok: [host2] => {
    "res.stdout_lines": [
        " 13:01:55 up 47 min,  1 user,  load average: 0.00, 0.01, 0.05"
    ]
}


For obtaining an output most likly this:
host1 | SUCCESS | rc=0 >>
Thu Feb 14 12:05:22 CET 2019

host2 | SUCCESS | rc=0 >>
Thu Feb 14 13:05:21 CET 2019

host1 | SUCCESS | rc=0 >>
 12:05:22 up 51 min,  1 user,  load average: 0.08, 0.04, 0.05

host2 | SUCCESS | rc=0 >>
 13:05:21 up 51 min,  1 user,  load average: 0.07, 0.03, 0.05
 
 
It's necessary setting stdout_callback variable to minimal whitin /etc/ansible/ansible.cfg and remove/comment register and debug lines in test.yml

---
- name: test script
  hosts: all
  remote_user: root
  tasks:
  - name: get date from server
    command: date
    #register: out
  
  - name: get uptime from server
    command: uptime
    #register: res
    
  #- debug: var=out.stdout_lines
  
  #- debug: var=res.stdout_lines
