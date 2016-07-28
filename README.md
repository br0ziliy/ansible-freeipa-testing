ansible-freeipa-testing
=======================

This role provisions a VM to run [FreeIPA tests](http://www.freeipa.org/page/Testing).
It might be useful for those who want to focus on the testing process itself,
and not on the process of setting up a testing environment.

**WARNING!** The role will **erase** existing FreeIPA completely, don't run it on
production systems!

Role Variables
--------------

List of role variables with their defaults:

- `freeipa_do_uninstall: True` - determines if a role should uninstall the
    existing FreeIPA on a VM
- `freeipa_src_dir: "{{ ansible_env.HOME }}/freeipa"` - where you'd like to keep the FreeIPA
    sources
- `freeipa_git_url: "git://"` - location of FreeIPA sources
- `freeipa_git_revision: "master"` - Git revision you'd like to run the tests
    on. Can be a branch name, tag, or even hash.
- `freeipa_ds_pass: "changeme"` - Directory Manager password (for
    `ipa-server-install`)
- `freeipa_admin_pass: "changeme"` - Kerberos admin password (for
    `ipa-server-install`)
- `freeipa_domain: "dom-{{ ansible_hostname.split('-')[1] }}.{{ ansible_domain }}"` - the domain name FreeIPA will manage

Variables that you almost likely don't want to change:

- `freeipa_realm: "{{ freeipa_domain|upper }}"`
- `freeipa_install_params: "-a {{ freeipa_admin_pass }} -p {{ freeipa_ds_pass }} -n {{ freeipa_domain  }} -r {{ freeipa_realm  }} --setup-dns --auto-forwarders --auto-reverse -U"`

Usage
-----

- install Ansible
- create inventory file, playbook and install the role:

```bash
mkdir -p ~/freeipa-testing
pushd ~/freeipa-testing
cat > hosts<<EOF
[test-vms]
some.test.vm.hostname
other.vm.hostname
192.168.5.11
EOF
cat > site.yml<<EOF
- hosts: test-vms
  roles:
     - { role: ansible-freeipa-testing, freeipa_domain: "example.dom" }
EOF
mkdir roles/
git clone https://github.com/br0ziliy/ansible-freeipa-testing.git roles/ansible-freeipa-testing
popd
```

- make adjustments to `hosts` and `site.yml` files to fit your environment
- run the playbook:

    ansible-playbook -u ${USER} -v -D -i ~/freeipa-testing/hosts ~/freeipa-testing/site.yml

License
-------

GPLv3
