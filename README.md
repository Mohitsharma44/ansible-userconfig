Ansible Role for User configuration and more ..

[![Build Status](https://travis-ci.org/Mohitsharma44/ansible-userconfig.svg?branch=master)](https://travis-ci.org/Mohitsharma44/ansible-userconfig)

Role Name
=========

Role for use with Ubuntu systems. This role will
- Update the system
- Install packages using apt
- Add users and groups
- Modify sudoer's for passwordless access for admins (check below for more info)
- Setup SSH keys and only allow key-based entry (check Role Variables for more info)
- Delete users (w/ or w/o their home directories)

Requirements
------------

The Ubuntu system must have python installed and an active internet connection

Role Variables
--------------

Following variables can be customized (default values are mentioned below):

``` yaml
users_default_shell: /bin/bash
users_create_homedirs: true

uousers:
  - name: admin
    username: testadmin
    group: 'uo-admin'
    groups: ['root', 'sudo']
    uid: 1001
    shell: '{{users_default_shell}}'
    generate_ssh_key: yes
    key: https://github.com/mohitsharma44.keys
    profile: |
        alias ll='ls -lah'
    comment: "Admin user"

uogroups:
  - name: uo-admin
    groupname: uo-admin
    type: admin
    gid: 10000

delusers:
  - name: user
    username: testuser
    remove: yes
    force: yes

apt_installs: apt_packages.txt
```

``` yaml
# Example of apt_packages.txt
git
python-dev
htop
```

For installing packages using apt, pass the filename to `apt_installs` and make
sure it exists.

For `uousers`, apart from general parameters, you can additionally create a list of
multiple `groups` that the `uouser` needs to be part of. By default, the uouser
is *not an admin user*. This user doesn't have any sudo-privileges

If the uouser has a role of *admin*, the `group` must be `uo-admin`. All the other
groups that the user needs to belong to, can be added in `groups`.

If the `uogroup` is of `type`: `admin` then all the `uousers` belonging to that
group will have passwordless sudo access.

By default, when a `uouser` account is created, the user cannot login to the server
with a password. The `uouser` *MUST* have the keys loaded in github.com and should
be accessible via https://github.com/<uouser>.keys. For more information refer
the [github documentation](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

User's profile can also be customized by adding things such as `alias` etc.

User's default `home` folder can be changed but it is upto you to make sure that
the path for new `home` directory exists.

When removing the users, passing `force: yes` will delete the user's home dir

Dependencies
------------

No Dependencies except the server must be running Python and should be able
accessible over ssh.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

``` yaml
---

- hosts: all
  gather_facts: no

  tasks:
  - include_role:
    name: init-host
```

License
-------

MIT

Author Information
------------------

Mohit Sharma. Mohitsharma44@gmail.com
