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

	---
	- hosts: NoisyAtomUbuntu14
	  user: root

	  vars_files:
	  - vars/project_variables.yml

	  tasks:
	  - name: ping the remote hosts
		ping:

This has the --- script line to let ansible know this is ansible commands. The next 2 lines identify the host to communicate with and
the user to connect as.
The vars_files tells ansible where to read it's global variables from.
Tasks tell ansible what commands to run. We have a named task called 'ping the remote hosts' which simply runs the ping module on the
remote server.


