# Vault

Simple and generic role for keeping sensitive build information secure using Ansible Vault. Examples of sensitive
information include passwords to remote web services, databases, command line APIs, etc.

## Dependencies

* None

## Requirements

* Adding the `–-ask-vault-pass` or `–-vault-password-file` when calling Ansible.

## Example Playbook

```yaml
- hosts: servers
  roles:
     - { 
        role: AlexanderAllen.vault, 
        vault_plaintext_filename: vault.yml, 
        vault_crypto_filename: .vault-crypto.yml,
        vault_plaintext_location: "playbook_location/vars",
        vault_crypto_location: "playbook_location/vars",
       }
```
**Note**: *Do not use an ending slash when overriding the locations to the cipher- and plain-text paths.*

## Example Vagrant configuration

The [Ansible provisioner](http://docs.vagrantup.com/v2/provisioning/ansible.html) for Vagrant provides Ansible Vault support via the `ansible.ask_vault_pass` and `ansible.vault_password_file` options for your `Vagrantfile`. You can only use one at a time.

```
  # Setup provisioning with an ansible playbook
  config.vm.provision "ansible" do |ansible|
    
    # ... rest of ansible configuration 
    
    # The Ansible provisioner will prompt you for a password every time it runs in order to decrypt vault data. 
    ansible.ask_vault_pass = true
    
    # Get the password for decrypting the vault cypher text from the "open_sesame" file.
    ansible.vault_password_file = "/root/.passwords/open_sesame"
  end
```

## What To Use For, Examples

* Adding an additional layer of security to automated builds that require sensitive information such as login 
information for other integration end points.
* Push/pull Docker images to the [Docker Hub Registry](https://registry.hub.docker.com/), which requires users to log in
with their their username, email, and password when publishing builds using the 
`[Docker CLI](https://docs.docker.com/reference/commandline/cli/#push)`.

## How To Use / Role Variables

* Copy the `examples/example.vault.yml` plain-text file to your `"{{ playbook_dir }}/vars"` directory,
or the directory specified by `vault_plaintext_location`.
* Rename `example.vault.yml` to `vault.yml` or the name specified by `vault_plaintext_filename`.
* Add/remove variables to `vault.yml` as needed.
* Include this role in your playbook.
* Pass the `–-ask-vault-pass` or `–-vault-password-file` option to Ansible when running your playbook via the command 
line, or;
* If using Vagrant add the `ansible.ask_vault_pass = true` flag to your `Vagrantfile`. 
* Run your playbook.
* Enter your Ansible Vault password.
* ...
* Profit?

## Notes / F.A.Q.

* **How do I populate the vault?**: In order to create new or modify the existing cipher text file, you can either add a
 new `vault.yml` file to the current configured `vault_crypto_location` location file, or use the
[Vagrant CLI](https://docs.ansible.com/playbooks_vault.html) to manually decrypt, edit, and re-encrypt the cipher text 
in `vault_crypto_file` directly.

* **Can I decrypt the vault manually?**: You can with the Vagrant CLI, but it will be your sole responsibility to 
re-encrypt the file, as Ansible does not provide a 100% reliable way for deciphering if `vault_crypto_file` is already
encrypted or not.

* **First-time password prompt**: If you are using the `–-ask-vault-pass` option when running this role for the first 
time Ansible will prompt you for your password three times:
Once using the `–-ask-vault-pass` flag - which you can just leave blank, and then two consecutive times for initial 
password and confirmation. On subsequent runs, you will only need to enter your password once, unless you are using the 
`–-vault-password-file` flag.

* **Password-less automation**: The [vault documentation](https://docs.ansible.com/playbooks_vault.html) does not
mention cryptographic hashing support for `--vault-password-file` but there is support for scripts - which may allow 
even more methods for protecting `vault_plaintext_filename` while enabling truly strong and secure password-less builds.

* **Plain-text file, Y U DISAPPEAR?**: Any data you add to `vault.yml` will be automatically encrypted using Ansible 
Vault and moved to `.vault-crypto.yml`, or the filename/location specified by `vault_crypto_location` + / + 
`vault_crypto_file` see `defaults/main.yml`. The original plain-text `vault.yml` file will be removed.

## Vault Host

The actions of finding, loading, encrypting and decrypting the structured data
file will be delegated via `delegate_to` to `vault_delegation_host`, 
which by default is `127.0.0.1` - usually the host running the Ansible playbook.

You can substitute `vault_delegation_host` to have your sensitive build information stored
in a machine separate from the host where Ansible builds run.

## Additional information

For more information on manually using Vault, see 
[https://docs.ansible.com/playbooks_vault.html](https://docs.ansible.com/playbooks_vault.html).

## Author Information

Richard Alexander Allen - [www.linkedin.com/in/drupalista](https://www.linkedin.com/in/drupalista)

## License

BSD 3-clause "New" or "Revised" License 
