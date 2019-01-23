# Chapter 4 Notes - Getting Ansible To Run Your First Playbook

From chapter 3 we created users with ansible add hock commands this section will do the same thing but rather than running a
sequence of commands one after the other it will be done in a playbook. A playbook is a yaml formatted file which contains all the
commands you woud normally do in the terminal but inside a file. Once you have created the file, you run the ansible playbook command
to get ansible to run it.

This is a great way to automate and orchestrate bringing up your system or systems.

We will have 4 steps to do this. Which are:

* Create a simple script which contains enough to find your server and do a ping
* Create a script that can create your user with a hard coded encrypted password and runs 'tasks'
* Add 'handlers' and 'pre-requesite' tasks to your script.
* Allow your script to take data from an encrypted 'vault' file. This means you can inject private data at run time to your system e.g. passwords.



## Step 1 - Create A Simple Script To Ping Your Server

* Create a file called simple_playbook_1.yml
* Create a dir called /vars
* Creata a file called project_variables.yml in the /vars/ directory
* In the simple_playbook_1.yml file copy this script into it.
```
	---
	- hosts: NoisyAtomUbuntu14
	  user: root

	  vars_files:
	  - vars/project_variables.yml

	  tasks:
	  - name: ping the remote hosts
		ping:
```
This has the --- script line to let ansible know this is ansible commands. 
The next 2 lines identify the host to communicate with and the user to connect as.

The vars_files tells ansible where to read it's global variables from. In this file we do not read anything from the file. It's
just in place for the next ansible playbook.

Tasks tell ansible what commands to run. We have a named task called 'ping the remote hosts' which is simply a text name output
to the screen. The final line with 'ping' runs the ping module on the remote server.

To run the file use the ansible-playbook command i.e.

	/> ansible-playbook simple_playbook_1.yml
	__________________________
	< PLAY [NoisyAtomUbuntu14] >
	 --------------------------
			\   ^__^
			 \  (oo)\_______
				(__)\       )\/\
					||----w |
					||     ||
	 
	ok: [104.236.14.123]
	 ...
	 ...
	 ...
	 ...
	 ...
	 ____________
	< PLAY RECAP >
	 ------------
			\   ^__^
			 \  (oo)\_______
				(__)\       )\/\
					||----w |
					||     ||

	104.236.14.123             : ok=2    changed=0    unreachable=0    failed=0



## Step 2 - Create A Script To Create Your User With Hard Coded Passwords

There is already a file created in this directory which is called 'simple_playbook_2.yml. In the simple_playbook_2.yml 
file it contains a 'hosts', 'vars' and 'task' section. The 'hosts' and 'vars' section are the same as the preivous script.

This file takes it's user names from the /vars/project_variables.yml file. The users are called:
* noisy_atom_admin
* noisy_atom_cms

To reference those users you use a format similar to jinja 2 (i.e. {{name}}). The tasks are broken down into: 

* Add our admin and cms user to the system
* Setup passwords for users 'adminuser' and 'cmsuser' on the remote server
* Add our admin user to the 'sudo' group so that he may have sudo privillages
* Setup an public key for this remote machine by copying the public key of this machine to the remote machine.
* We need to have our id_rsa.pub file within your home folder.
* Add our admin user to the 'sudo' users file and allow them to not require passwords for sudo access.
* Since this could be on a fresh Digital Ocean Machine we should ensure we setup a proper bash shell!
* We need to stop a user loging in with root access via SSH.
* Change our default SSH port if it's set in the project_variables.yml file, otherwise leave the SSH port at 22

Each section in the 'tasks' section have a title and comments describing what they do.
To run the script do:

	/> ansible-playbook simple_playbook_2.yml



## Step 3 - Create A Script To Create Your User With Hard Coded Passwords And Restart SSH To Stop Root Login

There is already a file created in this directory which is called 'simple_playbook_3.yml. In the simple_playbook_3.yml 
file it contains a 'hosts', 'vars', 'handlers' and 'task' section. The 'hosts' and 'vars' section are the same as the 
preivous script. The handlers is the new section.

This script is the same as the previous except for the 'handlers' section. The 'handlers' section is for holding
functions that are used more than once by scripts. Handlers are described like:

	handlers:
	  - name: Restart ssh
		service: name=ssh state=restarted

This has a 'title' called 'name' which is just the name printed to your screen while running. It invokes the service 
module which restarts 'ssh' on the remote server. To invoke this handler from a 'task' you do:

    name: Change ssh port if we have configured it in our vars directory.
    lineinfile: dest=/etc/ssh/sshd_config
      regexp="^Port\s"
      line="Port {{ ssh_port }}"
      state=present
    notify: Restart ssh

The task above has a line 'notify: Restart ssh'. This specifies the name of the handler to call. The only difference to
this script than version 2 is the handler to restart SSH via the handler - and the section which changes the port number
that SSH uses. It also has a section to block 'root' from using SSH, but this is commented out at the time of writing.

To run the script do:

	/> ansible-playbook simple_playbook_3.yml



## Step 4 - Allow your script to take data from an encrypted 'vault' file.

There is already a file created in this directory which is called 'simple_playbook_4.yml. In the simple_playbook_4.yml 
file it has exactly the same content for tasks as the previous version. This file however includes different variable
files. It pulls variables from files within the /vars/ directory:

		vars_files:
			- vars/project_variables_no_passwords.yml
			- vars/protected_vars.yml

The variable file 'project_variables_no_passwords.yml' we are using does not contain:

* cmsuser_password
* adminuser_password
* db_password

The second file 'protected_vars.yml' is an encrypted file, which has been encrypted by ansible-vault. To run this 
ansible file you have to pass the ansible vault password. There are many ways to do this, but for the purpose of
this tutorial we will pass it as a command line parameter. Hence to run this ansible script use the normal 'ansible-playbook'
command but pass in the command line flag '--ask-vault-pass' e.g.

	/>  ansible-playbook simple_playbook_4.yml --ask-vault-pass
	: Vault password

Use the vault password:

	test2017
	

### View The Encrypted File

To view the encrypted file and edit it first set an environment variable to change the default editor ansible will
use ( unless you like 'vi'! ). Then run the ansible-vault edit command

	/>  export EDITOR=nano
	/>  ansible-vault edit vars/protected_vars.yml
	Vault password:	

Now use the vault password as before.


### Create Encrypted File

To create a new encrypted file and add values via an editor do:

	/>  ansible-vault create foo.yml
	New Vault password:
	
Add your new password for the vault. In the file your parameter lists should look like:	
	
		adminpassword: 'secret'
		cmspassword: 'secret2'
		dbpassword: a_secret

You can find out more information here: http://docs.ansible.com/ansible/latest/playbooks_vault.html



