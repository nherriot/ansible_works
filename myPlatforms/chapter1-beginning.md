# Chapter 1 Notes - Beginning Ansible - Setting Up Access And Testing It Works

This chapter 1 guide shows you the 5 basic steps in setting up Ansible. Creating SSH public/private keys. Installing ansible,
checking it's version, basic ansible commands work on your remote machine and how to get your remote machine setup to receive
ansible commands and ansible playbook scripts.

## Prerequisits 

It is assumed you have a server with an IP address. I will assume this server 'x' is on IP address: 104.236.14.123. It is assumed 
your first user on the system is called 'root'.


## Step 1 - Creating Public Private Keys

Create an SSH public and private key pair on your host machine. To learn more about what public/private keys are and used for or
what the SSH protocol does read this:
https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process
To create your public/private key on your machine do this:

	/>  ssh-keygen

	
	
# Step 2 - Installing Ansible

You don't need to install Ansible the hard way, you can just to a sudo apt-get install ansible. But we will install the latest and 
greatest version so that we get any fixes. First update your ppa keys for the aptitude package manager. This allows you to get the 
most up to date version:

## Step 3

Add Ansible PPA keys to the aptitude package manager:
	/>  sudo apt-add-repository ppa:ansible/ansible
	
Update the new packages of your host machine:
	/>  sudo apt-get update
	
Install Ansible:
	/>	sudo apt-get install ansible
	
Check your version of Ansible:
	/>  ansible --version
	ansible 2.3.1.0
	  config file = /etc/ansible/ansible.cfg
	  configured module search path = Default w/o overrides
	  python version = 2.7.12 (default, Nov 19 2016, 06:48:10) [GCC 5.4.0 20160609]	


# Step 4 - Setup Ansible For Your Project

Ansible will have an ansible.cfg and host file in the directory /etc/ansible. Most books and tutorials will get you to alter those
files. This guide will copy thos file into your project directory so that they are used directly for that project.

You need to copy your hosts and ansible.cfg to your project directory. The platforms directory will be local to our system, not in the 
ansible etc folder. This means that we can have different configurations for different projects. 

Create a 'myplatforms' directory in your project directory and copy the ansible files to it.
	/> cd ~ & mkdir myPlatforms
	/>  cd /etc/ansible
	/>  cp hosts ~/myPlatforms/
	/>  cp ansible.cfg ~/myPlatforms/
	
## Step 5 - Change Your Local Copy of Configuration Files 

Configure your ansible.cfg file to look locally to find the hosts file. Change the file to look like:

					[defaults]

					# some basic default values...

					inventory      = hosts
					#library        = /usr/share/my_modules/
					#module_utils   = /usr/share/my_module_utils/

Configure hosts to hold your new machine names. In this case our machine has IP address: 104.236.14.12. Your file should look 
something like this:


					# ************************************************************************
					# ********  Setup Ubuntu 16.1 Noisy Atom Portal Digital Oceans ***********

					# Ubuntu 16 Noisy Atom Server On Digital Oceans
					# This machine is setup from Nicholas Herriot's account and running Ubuntu 16.1 LTR
					[NoisyAtom-Portal]
					178.62.31.182

					[NoisyAtomUbuntu14]
					104.236.14.123

In this instance we have 2 machines linked to the IP address of the servers.




 

	
