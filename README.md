Role Name
=========

API wrapper to manage secrets in Hashicorp Vault's K/V version 2 secret engine.

Requirements
------------

Python `requests`

Role Variables
--------------

`vault_addr`: Vault server address

`role_id`, `secret_id`: AppRole auth credentials. See [documentation](https://www.vaultproject.io/docs/auth/approle.html) for details on AppRole auth.

`mount`: The mount point for the K/V secret.

`name`: secret name.

`data`: { `key`: `value` }: K/V data

Dependencies
------------

None.

Example Playbooks
----------------

Using the module to store new or update existing secret with KV pairs.

```
---
- hosts: localhost
  gather_facts: no
  tasks:
  - import_role:
      name: openshiftoperations.hashicorp_vault
  - name: store secret
    hashicorp_vault:
      mount: secret
      name: mysecret
      data:
        foo: bar
      vault_addr: "https://vault-server.com"
      role_id: "{{ lookup('env','ROLE_ID') }}"
      secret_id: "{{ lookup('env','SECRET_ID') }}"
    register: mysecret
  - debug:
    msg: "{{ mysecret.results }}"
```
Using the module to get existing secrets with the module. 

```
---
- hosts: localhost
  gather_facts: no
  tasks:
  - import_role:
      name: openshiftoperations.hashicorp_vault
  - name: get secret
    hashicorp_vault:
      mount: secret
      name: mysecret
      vault_addr: "https://vault-server.com"
      role_id: "{{ lookup('env','ROLE_ID') }}"
      secret_id: "{{ lookup('env','SECRET_ID') }}"
    register: mysecret
  - debug:
    msg: "{{ mysecret.results }}"
```

Use filter plugins to get existings secret
```
---
- hosts: localhost
  gather_facts: no
  tasks:
  - import_role:
      name: openshiftoperations.hashicorp_vault
  - set_fact:
      vault_defaults:
        role_id: "{{ lookup('env','ROLE_ID') }}"
        secret_id: "{{ lookup('env','SECRET_ID') }}"
        vault_addr: "https://myvault.example.com/v1"
        mount: mysecretnamespace
  - name: store secret
    hashicorp_vault:
      mount: "{{ vault_defaults.mount }}"
      vault_addr: "{{ vault_defaults.vault_addr }}"
      role_id: "{{ vault_defaults.role_id }}"
      secret_id: "{{ vault_defaults.secret_id }}"
      name: mysecret
      data:
        foo: bar
        bar: foo
    register: mysecret
  - set_fact:
      secrets: "{{ vault_defaults | combine({'name': 'yoursecret'}) | get_secret}}"
  - debug:
      msg: "This is your secret: {{ secrets.data.yoursecret }
```
License
-------

Apache 2.0

Author Information
------------------

This role was created in 2018 by [Liam Pieri](http://liampieri.com).
