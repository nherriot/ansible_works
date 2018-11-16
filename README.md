# ansible_works

<img src="ansible-wide.png" width="256" title="Ansible Logo">

This is a simple tutorial for using ansible. It has all the parts that are missing in so many ansible tutorials.
It contains 4 playbooks and 4 readme files that go from installing ansible on a machine, using add hock commands
to test out your remote server with.

The tutorial starts with installation, creations of SSH keys, getting those keys onto your remote machine and
then testing this out from the command line.

The final part of the tutorial deals with reading an encrypted file using ansible vault and using this file to
pull variables into a script. It uses those variables in an ansible playbook.

Readers are urged to have a remote server to test this with. I used digital oceans for testing my scripts.
(i.e. IaaS - Infrastructure as a Service)

Open up the /myPlatforms folder inside there are 4 documents called 'chapter1...', 'chapter2...' etc.. And 4 yml files that corrospond to each chapter. The reader can follow each chapter and should end up with a perfectly running ansible setup and test files to use against a target remote machine.

Enjoy!

