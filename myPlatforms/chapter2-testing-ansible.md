# Chapter 2 Notes - Testing Ansible

This chapter 2 guide shows you how to test basic setup. It will test your remote server can receive execute and run ansible
commands. It then sets up your public private key on the remote server and runs through test on your system to prove it works.


## Step 1 - Test Ansible Works!

We need to send the equivalent of a ping command for ansible which shows our connection works. To do this we need to run the
ansible 'ping' command to your remote server. Ansible needs to know the remote server address, the user it should pretend to be
and the password of that user.
From chapter 1 you should have a host file in this directory that maps a name to an IP address. We are using the name:
	
	NoisyAtomUbuntu14

Run the command:

	/>  ansible  ubuntu_14_begin  -m  ping  -u  root  --ask-pas
		SSH password: 
		46.101.45.29 | SUCCESS => {
			"changed": false, 
			"ping": "pong"
		}

The command is 'ansible'. The module is 'ping'. The user we are using is -u. We also pass --ask-pas which tells ansible to
ask for the password. Normally ansible will not allow you to type passwords and requires public/private key on the remote server.


## Step 2 - Get Your Public Key Onto The Remote Server

In Chapter 1 you create a public/private key for your host machine. We need to copy the public key to the remote machine.
Install your public key onto your remote server. If you like you can see your new keys in dir:
	~/.ssh/id_rsa.pub
	
Install the keys with the ssh-copy-id command like:
	/>  ssh-copy-id root@104.236.14.12
	
Check your key is installed by trying to SSH to the machine:
	/>  ssh root@104.236.14.12

You will not be asked for your password! Great stuff!!!


## Step 3 - Test Ansible Ping

We are not going to test Ansible Ping again. This time we will NOT need to use '--ask-pas' as the SSH protocol will use the public
keys copied onto the server in Step 2 during encryption. Ansible can now communicate to that server as the 'root' user without
asking for password.

Run the command:

	/>  ansible  ubuntu_14_begin  -m  ping  -u  root
		SSH password: 
		46.101.45.29 | SUCCESS => {
			"changed": false, 
			"ping": "pong"
		}



