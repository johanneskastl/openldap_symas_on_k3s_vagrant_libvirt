# openldap_symas_on_k3s_vagrant_libvirt

Vagrant-libvirt setup that creates a VM with [k3s](https://k3s.io/), the minimal
lightweight Kubernetes distribution.

On top of k3s, this setup installs [OpenLDAP](openldap.org) using the "official"
[helm chart supported by Symas](https://github.com/symas/helm-openldap/).

Default OS is openSUSE Leap 15.6, but that can be changed in the Vagrantfile.
Please be aware, that this might break the Ansible provisioning.

## Vagrant

1. You need `vagrant`, obviously. And `git`. And Ansible...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing
   `git submodule init && git submodule update`
1. Run `vagrant up`
1. Run `kubectl --kubeconfig ansible/k3s-kubeconfig get nodes` and you should
   see your server.
1. Run `vagrant address` to find out your VM's IP address. If you do not have
   the address plugin installed, you can log in using `vagrant ssh` and check
   the IP address by running `ip a s`.
1. The URLs for both `phpldapadmin` and the [password change
   utility](https://ltb-project.org/documentation/self-service-password.html)
   are printed out at the end of the Ansible provisioning.
1. The LDAP server itself is reachable on the VM's IP, you can test this by
   running the following command (which needs ldapsearch installed):

   ```
   ldapsearch -H ldap://192.0.2.13 -W -b dc=openldap,dc=example,dc=org -D cn=admin,dc=openldap,dc=example,dc=org
   ```

   When asked for the password, use `dobby`. You should get something like the
   following output:

   ```
   $ ldapsearch -H ldap://192.0.2.13 -W -D cn=admin,dc=openldap,dc=example,dc=org -b dc=openldap,dc=example,dc=org
   Enter LDAP Password:
   # extended LDIF
   #
   # LDAPv3
   # base <dc=openldap,dc=example,dc=org> with scope subtree
   # filter: (objectclass=*)
   # requesting: ALL
   #

   # openldap.example.org
   dn: dc=openldap,dc=example,dc=org
   objectClass: dcObject
   objectClass: organization
   dc: openldap
   o: Example, Inc

   # Users, openldap.example.org
   dn: ou=Users,dc=openldap,dc=example,dc=org
   objectClass: organizationalUnit
   ou: users

   # Groups, openldap.example.org
   dn: ou=Groups,dc=openldap,dc=example,dc=org
   objectClass: organizationalUnit
   ou: Groups

   # hpotter, Users, openldap.example.org
   dn: cn=hpotter,ou=Users,dc=openldap,dc=example,dc=org
   objectClass: inetOrgPerson
   objectClass: posixAccount
   objectClass: top
   cn: hpotter
   gidNumber: 500
   givenName: Harry
   homeDirectory: /home/hpotter
   mail: hpotter@hogwarts.invalid
   sn: Potter
   telephoneNumber: 123-456-7890
   title: Tester
   uid: hpotter
   uidNumber: 1000
   userPassword:: ZG9iYnk=

   [...]

   # search result
   search: 2
   result: 0 Success

   # numResponses: 11
   # numEntries: 10
   ```

1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
