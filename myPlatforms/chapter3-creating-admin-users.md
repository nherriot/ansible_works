# Chapter 3 Notes - Getting Ansible To Create Admin User On Remote Server

You may recal that we need to remove root access from SSH to make sure we start to 'harden' our server. We should never use 'root' to
access our servers. We now need to update your machine to remove 'root' user access via SSH from your remote server. We need create
admin users on the remote server that have limited access to that machine. Once we do that 'ansible' will become the admin user to
access the machine.

In this document we will:
* peform a check to list the contents of the log on your remote machine.
* add a user using ansible add hock commands.
* manually SSH onto the machine as the new user.
* list the home directory of the new user by becoming the new user using ansible add hock commands (i.e. not the root user)
* remove a user using ansible add hock commands.

After this is over read chapter 4.


## Step 1 - A Final Check The System Still Works

We will run an ansible command (sometimes called Ad-Hock command) to view our system logs on the remote server. Normally you would have
to SSH onto the machine, CD to the directory /var/log and then do a 'more' or 'cat' on the system file to view logs. Let us do this with 
ansible directly.

Using the ansible 'add hock' we nee to tell it to run a command on the remote server. We need to run the command with 'root' privilages
on the server, with some arguments. We have to tell ansible to become the root user as presently we don't have any other users on our
remote machine. Hence arguments for the command are:
	-s = sudo
	-a = arguemtns
	-u = as user

	/> ansible NoisyAtomUbuntu14 -s -a "tail /var/log/syslog" -u root
	
	104.236.14.123 | SUCCESS | rc=0 >>
	Aug 24 16:17:01 NoisyAtomUbuntu14 CRON[30513]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 24 17:17:01 NoisyAtomUbuntu14 CRON[31648]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 24 18:17:01 NoisyAtomUbuntu14 CRON[348]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 24 18:36:44 NoisyAtomUbuntu14 ansible-command: Invoked with warn=True executable=None _uses_shell=False _raw_params=tail /var/log/messages removes=None creates=None chdir=None
	Aug 24 18:37:55 NoisyAtomUbuntu14 ansible-command: Invoked with warn=True executable=None _uses_shell=False _raw_params=tail /var/log/syslog removes=None creates=None chdir=None
	Aug 24 19:17:01 NoisyAtomUbuntu14 CRON[1843]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 20 06:17:01 NoisyAtomUbuntu14 CRON[1982]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 24 20:17:01 NoisyAtomUbuntu14 CRON[3046]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	Aug 24 21:17:01 NoisyAtomUbuntu14 CRON[4240]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
	
	
This shows the sys logs on the remote machine.


## Step 2 - Add A User Using Ansible Add Hock Commands

Next we will add an admin user and a cms user. You can use any user name you like for this. For this example I'm using the names:
* noisy_atom_admin
* noisy_atom_cms

We need to add those users with a password. However we can't pass our password in plain text. We need to encrypt the password before
sending it over to our Linux server. So there are a few steps here. We need to encrypte our password. Then use that encrypted name to 
create our user on the remote server.

### Step 2.1 - Encrypt A Password

To encrypt our password we user the Linux utility 'openssl'. For this example we will give our users the password 'hello'. Hence to
encrypt the word 'hello' do:

	/> openssl passwd -crypt hello
	/> kQMKsfd71mvis
	
### Step 2.2 - Create Our Users

Our arguments are:
name=noisy_atom_admin
password=kQMKsfd71mvis
group=admin
createhome=yes
generate_ssh_key=yes

where	-a = arguments
		-m = ansible module
		-u = the user ansible will connect to the server as.

	/> ansible NoisyAtomUbuntu14 -s -m user -a "name=noisy_atom_admin password=kQMKsfd71mvis  group=admin createhome=yes generate_ssh_key=yes " -u root
	104.236.14.123 | SUCCESS => {
		"changed": true, 
		"comment": "", 
		"createhome": true, 
		"group": 110, 
		"home": "/home/noisy_atom_admin", 
		"name": "noisy_atom_admin", 
		"password": "NOT_LOGGING_PASSWORD", 
		"shell": "", 
		"ssh_fingerprint": "2048 10:61:51:38:c1:3e:d3:83:dd:2b:90:7e:5f:96:2c:0d  ansible-generated on NoisyAtomUbuntu14 (RSA)", 
		"ssh_key_file": "/home/noisy_atom_admin/.ssh/id_rsa", 
		"ssh_public_key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDL7aE4ozR7uvO3QvxE9p1Wy3Ew1QVJlSaQBwT58rhQ2xsK2HrT9/OnOEAyDoE1/dmAOE8cLNV8kkhYfhqAIzZbboagz390kosbnOYYZ5h8FnjDM/7l3dMHc3fXk1A43+RvB0Xd+BmgYSEh4rbtMVeiW6jZTLtWPRJd0p4KaQxgPg9KOzMGWQg8PHanmBCGcVQy5r9xeMw45D52HAHp/8rQ6+f54bZMGWuiNxUFjWpe/K2+dfR+BSWxvEP1Ya43m/L6a4qsh1/lfx0QnlL61LgrnlCfEQWsrutRvH8kjInhrC3lBpytGqu2yeOBh7fZgWir5cMqHvBpKU0h5x4KVWiv ansible-generated on NoisyAtomUbuntu14", 
		"state": "present", 
		"system": false, 
		"uid": 1004
	}


Now create the second user 'noisy_atom_cms'


	/> ansible NoisyAtomUbuntu14 -s -m user -a "name=noisy_atom_cms password=kQMKsfd71mvis  group=admin createhome=yes generate_ssh_key=yes " -u root
	

## Step 3 - Manually SSH Onto The Machine As The New User

For this step we can check simply by logging onto the remote server as 'root' and then switching user to the new user accounts.
So from your terminal window do:

	/> ssh root@<your_ip_address>
	/> root@NoisyAtomUbuntu14:~# whoami
	root
	/> su noisy_atom_admin
	
	To run a command as administrator (user "root"), use "sudo <command>".
	See "man sudo_root" for details.
	noisy_atom_admin@NoisyAtomUbuntu14:/root$
	
	/> su noisy_atom_cms

	Password: 
	To run a command as administrator (user "root"), use "sudo <command>".
	See "man sudo_root" for details.
	noisy_atom_cms@NoisyAtomUbuntu14:/root$	
	
Finaly we can try and SSH directly from our remote server as the new users. So from a fresh teminal window do:

	/> ssh noisy_atom_cms@104.236.14.123
	
and:

	ssh noisy_atom_admin@104.236.14.123


## Step 4 - List The Home Directory Of The New User

Almost finished. We just need to list the home directory of our newly created users. We can use the old command in step 1 to get this 
information but just use the new user ID's.

	/> ansible NoisyAtomUbuntu14  -a "ls /home/noisy_atom_admin" -u noisy_atom_admin --ask-pas
	SSH password: 
	104.236.14.123 | SUCCESS | rc=0 >>

	/>ansible NoisyAtomUbuntu14  -a "ls /home/noisy_atom_cms" -u noisy_atom_cms --ask-pas
	SSH password: 
	104.236.14.123 | SUCCESS | rc=0 >>



## Step 5 -  Remove A User Using Ansible Add Hock Commands

Finally let us remove our new users to clean out the system.

	/> ansible NoisyAtomUbuntu14 -s -m user -a "name=noisy_atom_admin state=absent remove=yes" -u root


	/> ansible NoisyAtomUbuntu14 -s -m user -a "name=noisy_atom_admin state=absent remove=yes" -u root
