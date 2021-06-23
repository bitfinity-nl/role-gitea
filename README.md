# role-gitea
Gitea is a community managed lightweight code hosting solution written in Go. It is published under the MIT license.


## For LDAP / Active Directory
- Create an service account with readonly account (BindDN).
- Create an security group for admins (example: app_giteadmin).

## Example playbooks
```
---
# Title: Ansible-pull playbook for Gitea server
#
# Author: bitfinity-nl - L. Rutten
# Owner: IT Operations
# File: playbook-gitea-01.yml
#
# Description:
#   Playbook for provisioning a UrBackup server
#   on a Ubuntu 20.04LTS server.
  
- hosts: localhost
  connection: local
  become: true
  
  vars:
    # -- Custom settings: app-cert-selfsigned --
    def_cert_state             : 'enabled'
    def_cert_base_path         : '/opt/ansible/app-selfsigned-cert/ssl'
    def_cert_days              : '365'
    def_cert_rsa               : '4096'
    def_cert_keyout            : '{{ def_cert_base_path }}/private/{{ ansible_hostname }}/apache-selfsigned.key'
    def_cert_out               : '{{ def_cert_base_path }}/certs/{{ ansible_hostname }}/apache-selfsigned.crt'
    def_cert_chain             : '{{ def_cert_base_path }}/chain/{{ ansible_hostname }}/chain.pem'
    def_cert_country           : 'NL'
    def_cert_state_prov        : 'Limburg'
    def_cert_locality          : 'Venlo'
    def_cert_orgname           : 'IT Operations'
    def_cert_orgunit           : 'Limburg'
    def_cert_email             : 'support.example.com'
    
    # -- Custom settings: role-gitea --
    gitea_admin_user           : 'giteaadmin'
    gitea_admin_pass           : 'password'
    gitea_admin_mail           : 'email@example.com'
    gitea_apache2_domain       : 'git.bitfinity.nl'
    gitea_apache2_cert_file    : '{{ def_cert_out }}'
    gitea_apache2_cert_keyfile : '{{ def_cert_keyout }}'
    
    # -- Gitea LDAP settings --
    # Check local settings for binddn password
    gitea_ldap_state           : 'enabled'
    gitea_ldap_host            : '192.168.0.1'
    gitea_bind_dn              : 'CN=svc_example,CN=Users,DC=example,DC=com'
    gitea_user_search_base     : 'ou=example,dc=example,dc=com'
    gitea_admin_filter         : '(memberOf=CN=app_gitea_admin,OU=Applications,OU=Resources,OU=example,DC=example,DC=com)'
  
  pre_tasks:
    - name: "Include local config /root/.ansible-settings.yml"
      include_vars: /root/.ansible-config.yml
     
  roles:
    - app-cert-selfsigned
    - role-mariadb-server-10.5
    - role-gitea
