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

Next we will add an admin user and a cms user with admin name noisy_atom_admin and cms name noisy_atom_cms.


	/> ansible NoisyAtomUbuntu14 -s -m
