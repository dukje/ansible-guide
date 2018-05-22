===============
GETTING STARTED
===============


Project Repository
==================

Change to your Ansible directory and add all the roles via Galaxy. If you do not already have a project start a new one:

::

  git init $PROJECT_ROOT/
  cd $PROJECT_ROOT/
  git commit --allow-empty -m 'Initial commit.'

Add the adfinis-roles directory to the Ansible config and download all roles via Galaxy:

::

  cat <<__EOF__ > ansible.cfg
  [defaults]
  roles_path = ./adfinis-roles
  __EOF__
  cat <<__EOF__ > requirements.yml
  ---
  # -*- coding: utf-8 -*-
   - src: adfinis-sygroup.ansible
    version: master
  - src: adfinis-sygroup.console
    version: master
  - src: adfinis-sygroup.docker
    version: master
  - src: adfinis-sygroup.grub
    version: master
  - src: adfinis-sygroup.hostname
    version: master
  - src: adfinis-sygroup.hwraid
    version: master
  - src: adfinis-sygroup.hw_vm_tools
    version: master
  - src: adfinis-sygroup.ipmi
    version: master
  - src: adfinis-sygroup.iptables
    version: master
  - src: adfinis-sygroup.mariadb
    version: master
  - src: adfinis-sygroup.motd
    version: master
  - src: adfinis-sygroup.network
    version: master
  - src: adfinis-sygroup.nginx
    version: master
  - src: adfinis-sygroup.nodejs
    version: master
  - src: adfinis-sygroup.ntp
    version: master
  - src: adfinis-sygroup.pkg_mirror
    version: master
  - src: adfinis-sygroup.pki
    version: master
  - src: adfinis-sygroup.postfix
    version: master
  - src: adfinis-sygroup.rpcbind
    version: master
  - src: adfinis-sygroup.rsyslog
    version: master
  - src: adfinis-sygroup.snmp
    version: master
  - src: adfinis-sygroup.ssh
    version: master
  - src: adfinis-sygroup.telegraf
    version: master
  - src: adfinis-sygroup.upgrade
    version: master
  - src: adfinis-sygroup.users
    version: master

   # vim: set ts=2 sw=2 :
  __EOF__
  ansible-galaxy install -r requirements.yml


Create the main playbook ``site.yml`` with content along the following
example. Add your roles as needed:

::

  ---

  - hosts: all
    roles:
      - ansible
      - console
      - ssh

Create an inventory file ``hosts``, create as many hostgroups as you need. A
host can be in multiple hostgroups. Each host is in the hostgroup ``all``.

::

  www1.example.com
  www2.example.com
  db1.example.com

  [webservers]
  www1.example.com
  www2.example.com

  [mysql_servers]
  db1.example.com

  [ssh_servers]
  www1.example.com
  www2.example.com
  db1.example.com

You can now start Ansible, and Ansible will connect to each host with ssh.
If you can't login with public keys, you can use ssh controlmaster with
sockets, for that, create a file called ``ansible.cfg`` in the root of your
project directory.

::

  [defaults]
  ansible_managed     = Warning: File is managed by Ansible [https://github.com/adfinis-sygroup/ansible-roles]
  retry_files_enabled = False
  hostfile            = ./hosts
  roles_path          = ./adfinis-roles

  [ssh_connection]
  ssh_args            = -o ControlMaster=auto -o ControlPersist=30s
  #control_path       = ~/.ssh/sockets/%C

You need to create the directory ``~/.ssh/sockets`` and you should
manually establish a connection to each host (with a command like ``ssh -o
ControlMaster=auto -o ControlPath='~/.ssh/sockets/%C' -o ControlPersist=30s
-l root $FQDN``). While the connection is established (and 30 seconds
after that) a socket file in ``~/.ssh/sockets/`` is generated. Ansible will use this
socket file to connect to the hosts, and doesn't' need to reauthenticate.
This speeds up Ansible operations considerably especially with many hosts.


Run Ansible
===========

To run Ansible with your playbook and your hosts, just start
``ansible-playbook -i hosts site.yml``. If you want to know what has
changed, you can add the option ``--diff`` and if you want to know that
before you change anything, you can add ``--check``. With the checkmode
enabled, nothing gets changed on any of the systems!

As a possible way to go, start Ansible with diff and checkmode:

::

  ansible-playbook -i hosts --diff --check site.yml

If you think the changes do what you intend to do, you can start Ansible without the checkmode:

::

  ansible-playbook -i hosts --diff site.yml


Special Roles
=============

If you need new roles, which aren't created yet, create them and make a
pull-requests to the `ansible-roles`_ repository. Only generic roles will
be accepted. Follow the guidelines for new roles.

To create special roles for one project (e.g. not possible as a generic
role or never needed in another project) put them inside the directory
``roles/``. Each role in this directory will override roles in the directory
``adfinis-roles/``.


.. _ansible-roles: https://github.com/adfinis-sygroup/ansible-roles


.. vim: set spell spelllang=en foldmethod=marker sw=2 ts=2 et wrap tw=76 :
