# Hiding In Ansible Vault

After creating your first playbooks that consist of various modules, using templates or variables you can ask yourself a question - can I keep my provisioning scripts in the repository alongside my application code? You can, and you should! But what about some secret information, like passwords, SSH keys, etc? This is what Ansible Vault has been created for.

### What is Vault?

Ansible Vault is a tool that comes as a part of Ansible core - it's available out of the box. It is very simple to use, as it provides just a couple of commands that are pretty self-explanatory:

    $ ansible-vault
    Usage: ansible-vault [create|decrypt|edit|encrypt|rekey|view] [--help] [options] vaultfile.yml

The idea is very simple: take a YAML file that contains your secret variables and encrypt it, so that it cannot be read as a plain text. Then, when running a playbook Ansible recognizes that given file is encrypted and requires you to provide a password in order to read its contents. You can also provide a

### What should be kept there?

When you find out about Ansible Vault, you might be tempted to put all your data inside to make it safer. But in fact you should limit the amount of data that is encrypted. The reason behind this is simple - if you ecrypt everything, you probably need to share your vault password with more people which may lead to actually lowering your security level. In my opinion, Ansible vault is a place for any login credentials (usernames, passwords, SSH keys), certificates (such as SSL), etc.

### Usage

We start by creating a file with secret variables. It is useful to prefix their names with eg. `VAULT_`, so that you can quickly distinguish them from non-encrypted ones:

    # secret-vars.yaml
    vault_db_username: database_user
    vault_db_password: database_pass

Then we need to encrypt the file:

    ansible-vault encrypt secret-vars.yaml
    New Vault password:
    Confirm New Vault password:
    Encryption successful

After this, if you include `secret-vars.yaml` file in your playbook, you need to provide the password. If you don't, `ansible-playbook` command will fail immediately:

    $ ansible-playbook -i hosts playbook.yaml
    ERROR! Decryption failed

You have basically two options here - provide a password via command line:

    $ ansible-playbook -i hosts playbooks/deploy-dev.yaml --ask-vault-pass
    Vault password:
    ...

Or provide a password file that contains the password itself. What is interesting is that the file does not have to contain the password as a plain text. You can instead prepare a script which prints the password to the standard output, and that output will be passed to the `ansible-playbook`. This may be useful if you want to keep the password as an environmental variable - just echo the value in the script.

    $ ansible-playbook -i hosts playbooks/deploy-dev.yaml --vault-password-file path/to/file
    ...

### Security tips

Depite an obvious advantage that Ansible Vault brings to the table, you still need to remember about a few security principles to keep your work as safe as you can. First of all, remember not to commit your vaulr passwords/ password files to the repository! This might sound silly, but it happens. Make sure it does not happen to you.

Another thing is that you should change your vault password periodically, and every time a person leaves your development team. This is very important, as you should always limit the number of people that can access the most crucial data to minimum. In order to re-encrypt your file with a new key you need to use `rekey` command, provide an old password and a new one:

    $ ansible-vault rekey secret-vars.yaml
    Vault password:
    New Vault password:
    Confirm New Vault password:
    Rekey successful

Security is one of the most important, yet often forgotten elements of a modern developer repertoire. You should never, ever forget about that - make sure that your data is always secure, because this is how you can recognize that somebody is serious about their job. If you want to be trusted with provisioning data, Ansible Vault should be your close friend.
