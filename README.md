
Keystone OpenStack Ansible Role
=========

**Status**
* [![Build Status](https://travis-ci.org/openstack-ansible-galaxy/openstack-keystone.svg?branch=master)](https://travis-ci.org/openstack-ansible-galaxy/openstack-keystone) on master branch
* [![Build Status](https://travis-ci.org/openstack-ansible-galaxy/openstack-keystone.svg?branch=development)](https://travis-ci.org/openstack-ansible-galaxy/openstack-keystone) on development branch
* [![Ansible Galaxy](http://img.shields.io/badge/dguerri-openstack--keystone-blue.svg)](https://galaxy.ansible.com/list#/roles/1770) on Ansible Galaxy

OpenStack Keystone identity service installation.

_Tested on Trusty (14.04)_ with Mitaka.


Requirements
------------

A DBMS already configured with a user and a database (when applicable).
For RHEL/CentOS, RHOSP or RDO repositories are needed.

Role Variables
--------------

### Keystone (set by this role)

| Name | Default value | Description |
|---  |---  |---  |
| `openstack_keystone_database_url` | `sqlite:////var/lib/keystone/keystone.db` | Database URI |
| `openstack_keystone_admin_bind_host` | `0.0.0.0` | On which IP Keystone admin service should listen on |
| `openstack_keystone_admin_port` | `35357` | Desired Keystone admin service port |
| `openstack_keystone_bind_host` | `0.0.0.0` | On which IP Keystone public service should listen on |
| `openstack_keystone_port` | `5000` | Desired Keystone service port |
| `openstack_keystone_protocol` | `http` | Desired Keystone protocol (http/https) - WiP, do not use |
| `openstack_keystone_admin_token` | `keystone_admin_token` | Desired service token |
| `openstack_keystone_tenants` | `[ ]` | Array of of hash with tenant `name` and `description` (see examples) |
| `openstack_keystone_users` | `[ ]` | Array of hash with user: `name`, `password`, `tenant` and `email` (see examples) |
| `openstack_keystone_roles` | `[ ]` | Array of hash with role: `name`, `user` and `tenant` (see examples) |
| `openstack_keystone_services` | `[ ]` | Array of hash with role: `name`, `service_type` and `description` (see examples) |
| `openstack_keystone_endpoints` | `[ ]` | Array of hash with role: `service_name`, `region`, `public_url`, `internal_url` and `admin_url` (see examples) |
| `openstack_keystone_log_dir` | `/var/log/keystone` | Keystone log directory (it must exists) |
| `openstack_keystone_hostname` | `localhost` | Hostname/IP used internally during configuration. localhost is usually ok |


Dependencies
------------

None.

Example Playbook
----------------

    - hosts: keystone001
      roles:
        - role: openstack-keystone
          openstack_keystone_database_url: "mysql://{{ MYSQL_KEYSTONE_USER }}:{{ MYSQL_KEYSTONE_PASS }}@{{ DATABASE_HOSTNAME }}/{{ MYSQL_KEYSTONE_DB }}"
          openstack_keystone_admin_token: "{{ ADMIN_TOKEN }}"
          openstack_keystone_tenants:
            - { name: admin, description: "Admin tenant" }
            - { name: service, description: "Service tenant" }
            - { name: demo, description: "Demo tenant"  }
          openstack_keystone_users:
            - { name: admin, password: "{{ ADMIN_PASS }}", tenant: admin }
            - { name: demo, password: "{{ DEMO_PASS }}", tenant: demo }
            - { name: glance, password: "{{ GLANCE_PASS }}", tenant: service }
          openstack_keystone_roles:
            - { name: admin, user: admin, tenant: admin }
            - { name: _member_, user: demo, tenant: demo  }
            - { name: admin, user: glance, tenant: service  }
          openstack_keystone_services:
            - { name: keystone, service_type: identity }
            - { name: glance, service_type: image }
          openstack_keystone_endpoints:
            - service_name: keystone
              public_url: "http://keystone:5000/v2.0"
              internal_url: "http://keystone:5000/v2.0"
              admin_url: "http://keystone:35357/v2.0"
            - service_name: glance
              public_url: "http://glance:9292/"
              internal_url: "http://glance:9292/"
              admin_url: "http://glance:9292/"

---

A complete Ansible playbook demo, which uses this role, is available on Github (openstack-ansible-galaxy/vagrant-ansible-openstack) <https://github.com/openstack-ansible-galaxy/vagrant-ansible-openstack>

---

Credits
-------
RedHat suport implemented by Abel Boldú <abel.boldu@gmx.com>

License
-------

Apache

Copyright
------------------

Copyright (c) 2015 Davide Guerri <davide.guerri@gmail.com>
