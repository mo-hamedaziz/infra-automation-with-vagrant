1. Install vagrant and virtual box
2. Create and initiate the Vagrantfile
   - The static ip addresses given to the created VMs are not arbitrary, they're actually within the subnet of the virutalbox virtual network interface.
3. Use `vagrant up` command in the cli to bring up the VMs
    - It will take some time to download and setup the box images
4. Use `vagrant ssh control` to establish a ssh connection to the control VM (the default password is *vagrant*)
5. Inside the VM, if use go in the `/vagrant` directory, you'll find the same files that are in your host machine (project directory). These files are shared between the host and all the provisioned VMs.
6. We need to ensure that the control node can ping all the other nodes. To do so we need to add node hostnames and IP addresses to the `/etc/hosts` file. First, add this hosts file to your project directory, then after logging in the control VM using ssh, execute this command: `sudo cp /vagrant/hosts /etc/hosts`.
![alt text](image.png)
The ping command is now working successfully
7. To make (node) hosts ssh accessible (from control hosts) we need to execute these two command:
   1. `ssh-keygen` to generate ssh key pairs
   2. `ssh-copy-id node1 && ssh-copy-id node2 && ssh-copy-id node3` to copy the local SSH public key to the respective remote server.
8. To login to one of the nodes (node1 for example), we just need to run `ssh vagrant@node1`
![alt text](image-1.png)
We are inside node1 VM
9. In your host machines (project folder) create a directory called `ansible/`. Within that directory, create two files: `myhosts` (that will be our inventory file) and `playbook_docker.yml` (that will be our main playbook for this project)
10. Populate the two files