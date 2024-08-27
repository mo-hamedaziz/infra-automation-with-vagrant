# DevOps Infrastructure Automation with Vagrant

## Overview

This repository is a comprehensive guide to setting up and managing a DevOps environment using various tools and technologies. It covers the entire process of automating infrastructure using Vagrant, managing server configurations with Ansible, containerizing applications using Docker, and orchestrating containers with Docker Swarm.

### Diagram
![image](https://github.com/user-attachments/assets/6c1c8469-13a7-4323-8d51-ba24348e5a67)

## Prerequisites
- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Project phases
![image](https://github.com/user-attachments/assets/25843ad2-871e-4b85-b0a2-a4c33e43fbb4)

## Installation

1. **Install Vagrant and VirtualBox**
   - Download and install Vagrant from the official [website](https://www.vagrantup.com/downloads).
   - Download and install VirtualBox from the official [website](https://www.virtualbox.org/wiki/Downloads).

2. **Create and Initialize the Vagrantfile**
   - Create a `Vagrantfile` in your project directory.
   - Ensure that the static IP addresses assigned to the VMs are within the subnet of the VirtualBox network interface.

3. **Start the VMs**
   - Run `vagrant up` in your CLI to bring up the VMs. This process might take some time as it involves downloading and setting up the box images.
   - Use `vagrant halt` to turn off the VMs gracefully.
   - Use `vagrant destroy` to remove the VMs.

4. **SSH into the Control VM**
   - Run `vagrant ssh control` to establish an SSH connection to the control VM. The default password is `vagrant`.

5. **File Sharing**
   - Files in the `/vagrant` directory inside the VM are shared with the host machine (project directory).

6. **Configure /etc/hosts**
   - Add the node hostnames and IP addresses to the `/etc/hosts` file to enable communication between the nodes. Copy the file using:
     ```bash
     sudo cp /vagrant/hosts /etc/hosts
     ```
   - Test the connectivity by pinging the other nodes.

7. **Setup SSH Key Authentication**
   - Run `ssh-keygen` to generate SSH key pairs.
   - Distribute the keys using:
     ```bash
     ssh-copy-id node1
     ssh-copy-id node2
     ssh-copy-id node3
     ```
   - Login to the nodes using `ssh vagrant@node1`, `ssh vagrant@node2`, or `ssh vagrant@node3`.

8. **Ansible Setup**
   - Create an `ansible/` directory with the `myhosts` (inventory) and `playbook_docker.yml` (playbook) files.
   - Install Ansible on the control VM.
   - Test Ansible connectivity with:
     ```bash
     ansible nodes -i /vagrant/ansible/myhosts -m command -a hostname
     ```

9. **Deploy Docker with Ansible**
   - Run the playbook to install Docker on the nodes:
     ```bash
     ansible-playbook -i /vagrant/ansible/myhosts -K /vagrant/ansible/playbook_docker.yml
     ```
   - The `BECOME` password is `vagrant`.

10. **Verify Docker Installation**
    - SSH into `node1` and verify Docker installation by running:
      ```bash
      docker --version
      ```

11. **Deploy the Flask Web Application**
    - Create the `app.py`, `requirements.txt`, `Dockerfile`, and `docker-compose.yml` files in your project directory.
    - SSH into `node1` and run:
      ```bash
      docker compose -f /vagrant/docker-compose.yml up -d
      ```
    - Access the application by navigating to `<node1_ip_address>:5000` in your browser or using `curl node1:5000` from the control VM.

12. **Setup Docker Swarm**
    - Create a `docker-swarm/` directory with `swarm.yml` (playbook) and `myhosts` (inventory).
    - Run the playbook to set up Docker Swarm:
      ```bash
      ansible-playbook -i /vagrant/docker-swarm/myhosts -K /vagrant/docker-swarm/swarm.yml
      ```

13. **Deploy the Application in Docker Swarm**
    - Scale down the existing application:
      ```bash
      docker compose down
      ```
    - Build the Docker image:
      ```bash
      docker build -t vagrant-project/flaskwebapp .
      ```
    - Deploy the stack:
      ```bash
      docker stack deploy --compose-file docker-compose.yml myapp
      ```
    - Scale the service:
      ```bash
      docker service scale myapp_web=3
      ```
    - Test the load balancing by running multiple `curl` commands to `node1:5000`.

## Usage

- **Starting VMs:** `vagrant up`
- **SSH into Control VM:** `vagrant ssh control`
- **Stop VMs:** `vagrant halt`
- **Destroy VMs:** `vagrant destroy`
  
## Contributing
Contributions are welcome! Please fork the repository, create a new branch for your feature or bugfix, and submit a pull request.

## Contact
For any inquiries or questions, feel free to contact me at mohamedaziz.bchini@insat.ucar.tn .
