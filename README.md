# ansible_command_to_local_file_encryption
The provided Ansible playbook is designed to collect the contents of the /etc/passwd file from a remote server and save it locally.

This playbook provides a systematic way to collect and store user account information from a designated server, contributing to system administration and audit trail efforts.

The playbook follows security best practices by utilizing a separate secret.yml file to store encrypted credentials. This approach enhances the security of sensitive information, such as login credentials, by keeping them in an encrypted form. The use of encrypted files like secret.yml is a recommended practice in Ansible, especially when dealing with sensitive data like passwords.

## Instructions

Ansible Vault allows you to keep sensitive data such as passwords or keys in encrypted files, rather than as plaintext in your playbooks or inventory files. Here’s how you can use Ansible Vault to encrypt the sensitive data in your inventory.yml file:

### Step 1: Installing Ansible (if not already installed)

First, ensure you have Ansible installed on your machine. If you need to install it, follow the instructions relevant to your operating system.

### Step 2: Encrypting sensitive data with Ansible Vault

1. Open the terminal and move to the directory where your inventory.yml file is located.

2. Run the following command to create a new encrypted variable with Ansible Vault:

```
ansible-vault create secret.yml
```


This command will prompt you to enter a new Vault password and confirm it. After creating the password, a new file called secret.yml will open in your default command-line text editor.

***For edit | ansible-vault edit secret.yml |***

3. In the editor, you can define variables to store your sensitive data:

```
default_ssh_pass: 123
default_ssh_pass2: 1234
admin_password: 1111
other_admin_password: 2222
```


Save and exit the editor (usually with :wq in Vim or Ctrl+X followed by Y in Nano).

### Step 3: Referencing the encrypted variables

```
vars_files:
  - secret.yml
```
and
```
ansible_ssh_pass: "{{ default_ssh_pass }}"
```

Modify your playbook.yml to reference the variables from the secret.yml as follows:

```
---
- name: Collect /etc/passwd and save locally
  hosts: host_servers
  vars_files:
    - secret.yml
```

### Step 4: Define default variables for all hosts

Modify your inventory.yml and add or remove variables or use variables from the secret.yml

```
all:
  children:
    host_servers:
      vars:
        ansible_user: root
        ansible_ssh_pass: "{{ default_ssh_pass }}"
      hosts:
        server1:
          ansible_host: 192.168.56.140
        server2:
          ansible_host: 192.168.56.145
          ansible_ssh_pass: "{{ default_ssh_pass2 }}"
        server3:
          ansible_host: 192.168.56.150
```

### Step 5: Add hosts

```
server3:
  ansible_host: 192.168.56.150
server4:
  ansible_host: 192.168.56.155
```

### Step 6: Running a playbook with encrypted data

When running playbooks that use Vault-encrypted files, you’ll need to provide the Vault password:

```
ansible-playbook -i inventory.yml playbook.yml --ask-vault-pass
```

This will prompt you for the Vault password before the playbook executes.

Alternatively, you can store the Vault password in a file and reference it with the --vault-password-file option:

```
ansible-playbook -i inventory.yml playbook.yml --vault-password-file /path/to/vault_password_file
```
