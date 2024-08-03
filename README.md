<p align="center">
  <a href="" rel="noopener">
 <img width=200px height=200px src="https://seeklogo.com/images/D/digital-ocean-logo-7B970FE624-seeklogo.com.png" alt="Project logo"></a>
</p>

<h3 align="center">Digital Ocean Droplet Deployment</h3>

<div align="center">

[![Status](https://img.shields.io/badge/status-active-success.svg)]()
[![GitHub Issues](https://img.shields.io/github/issues/kylelobo/The-Documentation-Compendium.svg)](https://github.com/kylelobo/The-Documentation-Compendium/issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/kylelobo/The-Documentation-Compendium.svg)](https://github.com/kylelobo/The-Documentation-Compendium/pulls)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](/LICENSE)
[![PyPI version](https://img.shields.io/pypi/v/ansible-core.svg)](https://pypi.org/project/ansible-core)

</div>

<b><p align="center">Repository Updated: <span style="color: #9FEF00;">2024-08-02</span></p></b>

<p align="center">
    <br> 
    Digital Ocean Droplets are virtual private servers that provide scalable compute platforms with add-on storage, security, and monitoring capabilities. They offer a flexible, cost-effective solution for deploying and scaling applications, websites, and services in the cloud. With various configurations available, Droplets can be tailored to meet specific performance and resource requirements for diverse workloads.
</p>

## Table of Contents

- [Getting Started](#getting-started)
- [Prerequisites](#prerequisites)
- [Generate SSH Key and Personal Access Token](#generate-ssh-key-and-personal-access-token)
- [Setup Ansible Vault](#setup-ansible-vault)
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

- Ansible `v2.16.3` or later
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

<b><span style="color: #9FEF00;">Step One:</span></b> Install Ansible-core on your local machine.

```
sudo pip3 install ansible-core
```

<b><span style="color: #9FEF00;">Step Two:</span></b> On your local machine, clone the repository and navigate to the project directory.

```
git clone https://github.com/Immain/DigitalOcean-Droplet-Deployment.git
```

<b><span style="color: #9FEF00;">Step Three:</span></b> Change directory to the project directory.

```
cd DigitalOcean-Droplet-Deployment
```

<b><span style="color: #9FEF00;">Step Four:</span></b> Edit the ```ansible.cfg``` file to include default values for the Ansible configuration.

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

## Setup Ansible Vault

<b><span style="color: #9FEF00;">Step One:</span></b> Create a new Ansible Vault password file.

```
ansible-vault create vault.yml
```
<b><span style="color: #9FEF00;">Step Two:</span></b> You will be prompted to enter and confirm a password:

```
Output
New Vault password: 
Confirm New Vault password:
```
<b><span style="color: #9FEF00;">Step Three:</span></b> To test the encryption function, enter some test text:

```
Secret information
```

Ansible will encrypt the contents when you close the file. If you check the file, instead of seeing the words you typed, you will see an encrypted block:

```
cat vault.yml
```

Output:
```
$ANSIBLE_VAULT;1.1;AES256
343333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333
```

## Run the playbook

<b><span style="color: #9FEF00;">Step One:</span></b> This step involves editing the `hosts` file in the project directory. The `hosts` file is a configuration file that contains the IP addresses or hostnames of the servers that Ansible will manage. In this case, we will add the IP address of the Digital Ocean Droplet that we will deploy.

```
web:
  hosts:
    DropletName:
```

<b><span style="color: #9FEF00;">Step Two:</span></b> Run the Ansible playbook to deploy the Digital Ocean Droplet.

```
ansible-playbook --vault-password-file vault.yml deploy.yml
``` 

<b><span style="color: #9FEF00;">Step Three:</span></b> Connect to your new Digital Ocean Droplet using SSH.

```
ssh root@DropletIP
``` 

##  Troubleshooting

If you encounter any general issues during the deployment process:

  1. Check your network connection and ensure you have stable internet access.

  2. Verify that your DigitalOcean API token is valid and has the necessary permissions.

  3. Double-check all configuration files for typos or formatting errors.

  4. Ensure that your SSH key is properly set up and accessible.

  5. If a specific task fails, try running the playbook with verbose output using the `-vvv` flag for more detailed information:
     ```
     ansible-playbook -i hosts deploy.yml -vvv
     ```
  6. Check the DigitalOcean control panel to see if the droplet was created successfully, even if the Ansible playbook reports an error. For more information on managing droplets, refer to the [DigitalOcean Droplet documentation](https://docs.digitalocean.com/products/droplets/).

  7. If issues persist, try destroying the droplet (if created) and running the playbook again from scratch. You can find instructions on how to destroy a droplet in the [DigitalOcean documentation on deleting droplets](https://docs.digitalocean.com/products/droplets/how-to/destroy/).

  8. For more detailed information, consult the [Ansible DigitalOcean Collection Index](https://docs.ansible.com/ansible/latest/collections/community/digitalocean/index.html).

## Changes

<p style="color: #9FEF00;"><b>2024-08-02:</b></p>

- Updated README.md to include information on new DigitalOcean API changes.
