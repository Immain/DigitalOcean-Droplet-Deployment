<p align="center">
  <a href="" rel="noopener">
 <img width=200px height=200px src="https://seeklogo.com/images/D/digital-ocean-logo-7B970FE624-seeklogo.com.png" alt="Project logo"></a>
</p>

<h3 align="center">Digital Ocean Droplet Deployment</h3>

<div align="center">

[![Status](https://img.shields.io/badge/status-active-success.svg)]()
[![GitHub Issues](https://img.shields.io/github/issues/Immain/DigitalOcean-Droplet-Deployment.svg)](https://github.com/Immain/DigitalOcean-Droplet-Deployment/issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/Immain/DigitalOcean-Droplet-Deployment.svg)](https://github.com/Immain/DigitalOcean-Droplet-Deployment/pulls)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](/LICENSE)

</div>

<b><p align="center">Repository Updated: <span style="color: #9FEF00;">2024-12-08</span></p></b>


<p align="center">
Digital Ocean Droplets are virtual private servers that provide scalable compute platforms with add-on storage, security, and monitoring capabilities. They offer a flexible, cost-effective solution for deploying and scaling applications, websites, and services in the cloud. With various configurations available, Droplets can be tailored to meet specific performance and resource requirements for diverse workloads.
</p>

## Table of Contents

- [Getting Started](#getting-started)
- [Prerequisites](#prerequisites)
- [Generate SSH Key and Personal Access Token](#generate-ssh-key-and-personal-access-token)
- [Ansible Vault](#ansible-vault)
- [Installation](#installation)
- [Troubleshooting](#troubleshooting)
- [Changes](#changes)



## Getting Started

For this project, we will be deploying a Digital Ocean Droplet using Ansible. The prerequisites for this project are as follows:

- Digital Ocean account (Sign up at [Digital Ocean](https://www.digitalocean.com/))
- Droplet Pricing ([Available pricing plans](https://www.digitalocean.com/pricing/droplets))
- Basic understanding of cloud computing concepts
- Familiarity with Linux command line interface

## Prerequisites

- Ansible `v2.10.8` or later
- SSH key for Digital Ocean account
- Digital Ocean personal access token
- Digital Ocean Slug ID (e.g. `s-1vcpu-2gb-amd`) and image ID (e.g. `ubuntu-24-04-x64`) - [Available slugs](https://slugs.do-api.dev/)
- Digital Ocean Region ID (e.g. `sfo3`) - [Available regions](https://docs.digitalocean.com/platform/regional-availability/#app-platform-availability)

## Generate SSH Key and Personal Access Token:

<b><span style="color: #9FEF00;">Step One:</span></b> Add SSH Key to Digital Ocean

1. Go to [Digital Ocean](https://www.digitalocean.com/) and sign in.
2. On the left sidebar, click on **"Settings"** and then **"Security"**.
3. Click on **"Add SSH Key"**.
4. Use ```ssh-keygen``` to generate a new SSH key and add it to Digital Ocean.
5. Use ```cat ~/.ssh/id_rsa.pub``` to get the public key and add it to your Digital Ocean account.
6. Give your key a name and click **"Add SSH Key"**.

<b><span style="color: #9FEF00;">Step Two:</span></b> Create a personal access token

1. On the left sidebar, click on **"API"** and then **"Tokens"**.
2. Click on **"Generate New Token"**.
3. Give your token a name and <span style="color: #9FEF00;">**fill out the expiration date and scopes**</span>, then click **"Generate Token"**.
4. Copy the token and save it to a secure location.


##  Installation

> [!IMPORTANT]  
> Ansible is only available on macOS and Linux. If you are using Windows, you can use the Windows Subsystem for Linux (WSL) to install Ansible.

<b><span style="color: #9FEF00;">Step One:</span></b>  Install Ansible on your local machine.


```
sudo apt install ansible -y
```

Verify the Ansible Version:
```
ansible --version
```

<b><span style="color: #9FEF00;">Step Two:</span></b> Install the Digital Ocean module from Ansible Community, you can find the Ansible Digital Ocean documentation [here](https://docs.ansible.com/ansible/latest/collections/community/digitalocean/index.html)

```
ansible-galaxy collection install community.digitalocean
```

<b><span style="color: #9FEF00;">Step Three:</span></b> On your local machine, clone the repository and navigate to the project directory.

```
git clone https://github.com/Immain/DigitalOcean-Droplet-Deployment.git
```

<b><span style="color: #9FEF00;">Step Four:</span></b> Change directory to the project directory.

```
cd DigitalOcean-Droplet-Deployment
```

<b><span style="color: #9FEF00;">Step Five:</span></b> Edit the ```ansible.cfg``` file to include default values for the Ansible configuration.

```
[defaults]
remote_user = root
host_key_checking = False
private_key_file = ~/.ssh/id_rsa

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

```

## Ansible Vault
Ansible vault provides a way to encrypt and manage sensitive data such as passwords and tokens

### Vault Setup:

Create a new Ansible Vault password file:
```
sudo ansible-vault create group_vars/vault.yml
```

You will be prompted to enter and confirm a password:
```
New Vault password: 
Confirm New Vault password:
```

Add your ```oauth_token``` to the vault using a ```vault_``` prefix
```
vault_oauth_token: superdupersecrettoken
```
Ansible will encrypt the contents when you close the file. If you check the file, instead of seeing the words you typed, you will see an encrypted block:
```
sudo cat group_vars/vault.yml
```
<b>Output:</b>
```
$ANSIBLE_VAULT;1.1;AES256
343333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333
```

In your ```/group_vars/vars.yml``` file, reference the encrypted variables:
```
oauth_token: "{{ vault_oauth_token }}"
```

Reference the vault under ```vars_files``` in your playbook, include both the vars.yml and vault.yml files:
```
- hosts: localhost
  vars_files:
    - ./group_vars/vars.yml
    - ./group_vars/vault.yml
```

## Adding Tags
Before running the playbook, set your desired tags, this will be useful for organizing and categorizing your Droplets, making it easier to manage and filter them in the DigitalOcean control panel or via API calls.
```
sudo vi deploy.yml
```
```
# Tag Droplets
    - name: Tag the Droplet
      community.digitalocean.digital_ocean_tag:
        oauth_token: "{{ oauth_token }}"
        name: "{{ item }}"
        resource_id: "{{ droplet_info.data[0].id }}"
        state: present
      loop:
        - changeme_1
        - changeme_2
      when: droplet_info.data | length > 0
```

## Run the playbook
To run your playbook use
```
sudo ansible-playbook run.yml --ask-vault-pass
```

## Changes

<p style="color: #9FEF00;"><b>2024-12-08:</b></p>

- Updated [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html) documentation.
- Added ability to [add Tags](https://docs.ansible.com/ansible/latest/collections/community/digitalocean/digital_ocean_tag_module.html#ansible-collections-community-digitalocean-digital-ocean-tag-module) per Digital Ocean Ansible documentation
- Added new variable for ```Dynamically add new host entry```, connecting to host in the final task now pulls the data from ```set_fact``` on localhost and applies it to the hosts name as ```"{{ hostvars['localhost']['droplet'] }}"```
- Updated README to reflect new playbook changes
- Removed outdated documentation
- Updated command to include ```--ask-vault-pass``` to run playbook
- Removed respository ```hosts``` file
- Updated ```group_vars/vars.yml```
- Added ```group_vars/vault.yml``` to variables in ```deploy.yml```


<p style="color: #9FEF00;"><b>2024-12-07:</b></p>

- DigitalOcean [API Changes](https://docs.ansible.com/ansible/latest/collections/community/digitalocean/digital_ocean_droplet_module.html#ansible-collections-community-digitalocean-digital-ocean-droplet-module), this included changing ```size_id``` to ```size``` under creating Digital Ocean Droplet. 
- Removed ```with_inventory_items```
- Removed the need for the additional hosts file, using ```/etc/hosts``` instead
- Added ```Add new droplet to in-memory inventory``` for better stability
- Added ```'{{ droplet_name }}'``` to name the Digital Ocean Droplet from ```group_vars/vars.yml```
- Added ```project``` under Create Digital Ocean Droplet to specificy which environment the droplet belonged to
- Updated the ```apt``` module, as the old one was not updating the system correctly

<p style="color: #9FEF00;"><b>2024-08-02:</b></p>

- Updated README.md to include information on new DigitalOcean API changes.
