
ansible-role-perlbrew
=======================

An Ansible role to install and manage **perlbrew** that is only used for manual testing at this time.


Description
-----------

Very Rough Draft

## Notes

You may want to remove / uninstall older versions before installing newer ones.


Resources
---------

-  [manual recipe](docs/perlbrew.md)



Requirements
------------


Options
-------


Issues
------


Role Variables
--------------

### roles/perlbrew/defaults/main.yml

```yaml
---
# file: roles/perlbrew/defaults/main.yml
#

# Requirements
#
# set the value of project_deployment_user_name in your projects group_vars/all/defaults.yml
#
# ---
# file: group_vars/all/defaults.yml
#
# project_deployment_user_name: 'deployment_user_name'

perlbrew_controller_home   : '{{ fact_controler_home }}'
perlbrew_remote_user       : '{{ project_deployment_user_name }}'
perlbrew_remote_users_home : '/home/users/{{ perlbrew_remote_user }}'

# probably set in this or a dependent role

perlbrew_state             : 'absent' # 'present' # 'absent'
#perlbrew_installation_type : 'local' # 'url'
perlbrew_app_name          : 'perlbrew'
perlbrew_package_name      : 'perlbrew-desktop'
perlbrew_ver               : '2.0.3' #'2.6.2' # 2.0.3
perlbrew_arch              : 'amd64'
perlbrew_package_type      : 'deb'

# calculated vars

# example below builds "perlbrew-desktop-2.6.2-amd64.deb"
perlbrew_package_filename  : '{{ perlbrew_package_name }}-{{ perlbrew_ver }}-{{ perlbrew_arch }}.{{ perlbrew_package_type }}'
perlbrew_controller_package_path : '{{ fact_controller_home }}/src/Ubuntu/16.04/perlbrew/{{ perlbrew_ver }}/{{ perlbrew_package_filename }}'

perlbrew_taget_node_package_dir  : '{{ perlbrew_remote_users_home }}/src/Ubuntu/16.04/perlbrew/{{ perlbrew_ver }}'
perlbrew_taget_node_package_path : '{{ perlbrew_taget_node_package_dir }}/{{ perlbrew_package_filename }}'
```

### roles/perlbrew/tests/vagrant.yml

```shell
---
# file: roles/{{ short_role_name }}/tests/vagrant.yml

- hosts: all
  remote_user: ubuntu
  become: false # or local directory creation will fail
  pre_tasks:

    - set_fact: fact_controller_user="{{ lookup('env','USER') }}"
    - debug: var=fact_controller_user

    - set_fact: fact_controller_home="{{ lookup('env','HOME') }}"
    - debug: var=fact_controller_home

  vars:

    - perlbrew_controller_home   : '{{ fact_controler_home }}'
    - perlbrew_remote_user       : 'ubuntu'
    - perlbrew_remote_users_home : '/home/ubuntu'

# probably set in this or a dependent role

    - perlbrew_state                   : 'absent' # 'present' # 'absent'
#    - perlbrew_installation_type       : 'local' # 'url'
    - perlbrew_app_name                : 'perlbrew'
    - perlbrew_package_name            : 'perlbrew-desktop'
    - perlbrew_ver                     : '2.0.3' #'2.6.2' # 2.0.3
    - perlbrew_arch                    : 'amd64'
    - perlbrew_package_type            : 'deb'
    # example below builds "perlbrew-desktop-2.6.2-amd64.deb"
    - perlbrew_package_filename        : '{{ perlbrew_package_name }}-{{ perlbrew_ver }}-{{ perlbrew_arch }}.{{ perlbrew_package_type }}'

    - perlbrew_controller_package_path : '{{ fact_controller_home }}/src/Ubuntu/16.04/perlbrew/{{ perlbrew_ver }}/{{ perlbrew_package_filename }}'

    - perlbrew_taget_node_package_dir  : '{{ perlbrew_remote_users_home }}/src/Ubuntu/16.04/perlbrew/{{ perlbrew_ver }}'
    - perlbrew_taget_node_package_path : '{{ perlbrew_taget_node_package_dir }}/{{ perlbrew_package_filename }}'

  roles:
    - ../../
```



NOT IN USE AT THIS TIME - Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles. Examples:

* [ ansible-role-acemenu ]( https://github.com/cjsteel/ansible-role-acemenu )
* [ ansible-role-ensure_dirs ]( https://github.com/csteel/ansible-role-ensure_dirs )
* [ ansible-role-skel ]( https://github.com/csteel/ansible-role-skel )

### NOT IN USE AT THIS TIME  - ensure_dirs

#### NOT IN USE AT THIS TIME - roles/ansible-role-perlbrew/meta/main.yml

```yaml
---
# file: roles/ansible-role-perlbrew/meta/main.yml in dependant role
dependencies:

- { role: ensure_dirs, 
        ensure_dirs_on_remote: "{{ perlbrew_remote_directories_description }}",
        ensure_dirs_on_local : "{{ perlbrew_local_directories_description }}"
  }
```

#### NOT IN USE AT THIS TIME 

#### roles/ansible-role-perlbrew/defaults/main.yml example

```yaml
perlbrew_remote_directories_description:

  perlbrew_installation_resources_dir:

    state       : "present"					# absent
    path        : "sys/sw"					# relative to Ansible users home
    owner       : "{{ ansible_ssh_user }}"
    group       : "{{ ansible_ssh_user }}"
    mode        : "0644"

perlbrew_local_directories_description:

  perlbrew_installation_resources_dir:

    state       : "present"					# absent
    path        : "sys/sw/" 				# relative to Ansible users home dir
    owner       : "{{ ansible_ssh_user }}"
    group       : "{{ ansible_ssh_user }}"
    mode        : "0644"
```



role playbook example
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:



### project_name/perlbrew.yml

```yaml
---
# file: project_name/ants.yml

- hosts: perlbrew
  become: false
  gather_facts: true
  pre_tasks:

    - set_fact: fact_controller_user="{{ lookup('env','USER') }}"
    - debug: var=fact_controller_user

    - set_fact: fact_controller_home="{{ lookup('env','HOME') }}"
    - debug: var=fact_controller_home

  roles:

    - { role: perlbrew, perlbrew_state: 'absent', perlbrew_ver: '2.0.3' }
    - { role: perlbrew, perlbrew_state: 'present', perlbrew_ver: '2.6.2' }

#    - { role: cjsteel.ansible-role-perlbrew, perlbrew_state: 'absent', perlbrew_ver: '2.0.3' }
#    - { role: cjsteel.ansible-role-perlbrew, perlbrew_state: 'present', perlbrew_ver: '2.6.2' }
```



## main playbook example

### project_name/systems.yml

```yaml
---
- hosts: all
  become: false

- include: deployment_user.yml

- include: shorewall.yml

- include: ldap.yml

- include: workstation.yml

- include: perlbrew.yml
...
```



## Ansible command examples

### without sudo

```shell
ansible-playbook -i inventory/dev systems.yml --limit ace-ws-77
```

### with sudo

```shell
ansible-playbook -i inventory/dev systems.yml --limit ace-ws-77 --ask-become-pass
```



## Vagrant testing

We need a really big box. This one workded with no editing:

```shell
vagrant init cbednarski/ubuntu-1604-large; vagrant up --provider virtualbox
```

```shell
mkdir -p .vagrant/synced
vagrant up
ssh-copy-id -p 2222 vagrant@localhost
vagrant ssh
```



## License

MIT

## Author Information

Christopher Steel on behalf of ACElabs  
Systems Administrator  
McGill Centre for Integrative Neuroscience  
Montreal Neurological Institute  
E-mail: christopherDOTsteel@mcgill.ca  
[mcin-cnim.ca](http://mcin-cnim.ca/)    
[theneuro.ca](http://www.mcgill.ca/neuro/)   

## Open Science

The Montreal Neurological Institute has adopted the principles of Open Science. We are inspired by the likes of the Allen Institute for Brain Science, the National Institutes of Health's Human Connectome project, and the Human Genome project. For additional information please see [open science at the neuro]( https://www.mcgill.ca/neuro/open-science-0).



![MCIN](imgs/mcin-logo-brain-140x116.png)          ![neuro](imgs/neuro-logo-160x116.png)  
