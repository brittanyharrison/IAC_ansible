# IaC Configutartion Management with Ansible

## Infastructure as Code (IaC)
Largely up till now, we have mostly been doing mutable environments. We SSH in, run bash scripts, restart server and get it to the place the machines need to be.

- **Infrastructure as Code (IaC)** is the managing and provisioning of infrastructure through code instead of through manual processes. 

- IaC is an important part of implementing DevOps practices and continuous integration/continuous delivery (CI/CD). IaC takes away the majority of provisioning work from developers, who can execute a script to have their infrastructure ready to go.  

Benefits:
- Cost reduction
- Increase in speed of deployments
- Reduce errors 
- Improve infrastructure consistency
- Eliminate configuration drift

IaC has two parts:
- Configuartion Mangement: 
  - They help configure and test machines to a specific state.
  - Systems - Puppet, Chef and ansible
- Orchestration:
  - These tools focus on networking and architecture rather than the configuration of individual machines. 
  - Terraform, Ansible

## Ansible 
- Ansible is the fastest growing python-based open source IT automation tool that can be used to configure/manage systems, deploy applications and provision infrastructure on numerous cloud platforms. 

Benefits :

- Save time
- Open source
- Makes configuration management predictable
- Cost effective

Why use it:

- Ansible is angentless because we only need to have ansible installed on the controller. 
- We connect using SSH - this also adds to its simplicity


### Architecture of Ansible Cloud Integration
![img](img/Ansible_Diagram2-16-1536x692.png)

**Modules**
- Modules are script-like programs written to specify the desired state of the system. These are typically written in a code editor. 

- Modules are part of a larger program called Playbook. 

-  Ansible module is a standalone script that can be used inside an Ansible Playbook.

**Inventory**
- Ansible reads information about the machines you manage from the inventory. 

- Inventory is listed in the file which contains IP addresses, databases, and servers.

Example of an Inventory file:
```
---
[webservers]
www1.example.com
www2.example.com

[dbservers]
db0.example.com
db1.example.com
```

**Playbooks**
- Playbooks describe the tasks to be done by declaring configurations in order to bring a managed node into the desired state.

- Playbooks are files written in YAML - Yet Another markup Language.

- Syntax starts with 3 dashes and then a list of plays

Example of Playbook:
```
---
- hosts: webservers
  serial: 5 # update 5 machines at a time
  roles:
  - common
  - webapp

- hosts: content_servers
  roles:
  - common
  - content

```

## Ansible Set-up

![img](img/Iac_ansible-1.jpg)


### Connecting to hosts

- ssh into the ansible controller machine 

- Install Depoendencies:

```
sudo apt-get update
	
sudo apt-get install software-properties-common
	
sudo apt-add-repository ppa:ansible/ansible
	
sudo apt-get update
	
sudo apt-get install ansible

sudo apt-get install tree
```

- cd into `/etc/ansible/hosts` so we can connect to web and db hosts using the private ip

```
[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
 
[db]
192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
 
```
The command to check connections 

- `ansible all -m ping` - all host
- `ansible web -m ping` - specific host

### Ansble ad-hoc command

Ansible ad-hoc commands can be used in our ansible controller to communicated with our hosts. 

![img](img/ad-hoc.png)

- check date: `ansible all -a date`

- check memory: ` ansible all -m shell -a "free -m"`

- get uptime `ansible all -a uptime --become` `ansible all -m shell -a uptime`

- reboot ``

- update and upgrade environments

```
ansible host_a -m apt -a "upgrade=yes update_cache=yes" --become

```

### Creating Playbook

`sudo nano nginx_playbook.yml`

```
# Playbook to install nginx installation
# Written in YAML and starts with ---

---

# hosts dName of host 
- hosts: web
# host info 
  gather_facts: yes
# admin access
  become: true
# instructions using task modules
  tasks:
  - name: Install Nginx
# nginx
    apt: pkg=nginx state=present update_cache=yes
# ensure its active
# update cache
# restart nginx

```
- `ansible-playbook nginx_playbook.yml`

- `ansible web -m shell -a "systemctl status nginx"`

## Activity 

- create a playbook to install nodejs with required dependencies, 
- configure the reverse proxy and 
- node app should be running on web ip without the port 3000.
- playbook should get your code to web