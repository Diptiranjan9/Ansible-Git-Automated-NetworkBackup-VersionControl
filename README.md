# Ansible with Git: Automating Network Backup and Version Control

## Abstarct

This project provides a detailed and practical guide on automating network configuration backups using ***Ansible*** across multiple OEM devices, including **Cisco, Juniper, and Fortinet**. It leverages the power of ***Git*** for version control, ensuring efficient tracking, management, and rollback of network configurations.

While alternative tools like ***Rancid and Oxidized*** offer similar functionalities, this solution stands out for its robustness, speed, and adaptability to modern network environments. By combining automation and version control, this project empowers network administrators to streamline operations, reduce errors, and enhance network reliability. 

## Requirements

- **Linux Server:** Use a supported Linux distribution such as Ubuntu, CentOS, or Debian.

- **Administrator User Account:** Create an administrator-level user account on each device to ensure proper access for configuration backups.
  - Enable SSH and NETCONF on Cisco and Juniper switches.
  - Enable HTTPS access on Fortinet firewalls.
- **Python Environment:** Install Python 3.12 or later. A **requirements.txt** file is provided to simplify the installation of required Python packages in a virtual environment.
- **Git Installed:** Install Git on the Linux server for version control of network backup configurations. 


## Server Configuration Guide

### Setup Ansible Enviornment

To set up a Python virtual environment for Ansible, you can follow these recommended steps. Ensure that **Python3 and Pip3** are installed on your operating system. Once installed, you can create the virtual environment by referring to the [official guide](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/). 

Activate the Python Virtual Environment. To activate the Python virtual environment, use the following command:

```
source /path/to/venv/folder/bin/activate
```  

Install the Python [requirements](https://github.com/Diptiranjan9/Ansible-Git-Automated-NetworkBackup-VersionControl/blob/main/python-packages-requirement.txt) file included in this repository. This file includes the Ansible package and other required dependencies.

```
pip install -r python-packages-requirement.txt
```
After installing the packages, you can check the Ansible version and verify if the Ansible Galaxy collections are installed using the following commands:

```
ansible --version
ansible-galaxy collection list
```

**Note:-**
During this installation, Ansible pulls the latest Juniper packages into its Galaxy collection. To install older versions, you will need to add and install an additional collection from the list.

```
ansible-galaxy collection install juniper.device
```

Edit your Ansible configuration to disable host key checking by adding the following setting:
```
ANSIBLE_HOST_KEY_CHECKING=False
```
Alternatively, you can set this environment variable globally by adding it to your ```.bashrc``` or ```.zshrc``` file:

```
echo "export ANSIBLE_HOST_KEY_CHECKING=False" >> ~/.bashrc
# or for zsh
echo "export ANSIBLE_HOST_KEY_CHECKING=False" >> ~/.zshrc
```

After making the change, reload your shell configuration to apply the updates:

```
source ~/.bashrc   # For bash
source ~/.zshrc    # For zsh
```

### Setup Git Enviornment

- Install Git on your operating system by following the official guide [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

- You must decide which Git server to use: GitHub, GitLab, or your own private Git server.
- Given that I am backing up sensitive network devices, including firewalls and switches, I cannot afford to upload my configuration files to a public server due to security concerns. While the choice is ultimately yours, it's important to consider the risks of storing sensitive data on public platforms. In my case, I am using a private Git server to ensure data confidentiality and control. Follow the official Git [guide](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server) to initialize your own secure repository.

Once you create your own Git repository, you can clone it to a local directory where you want to store all your device backup configuration files.

```
mkdir /path/to/store/your/backup/config/
cd /path/to/store/your/backup/config/
git clone git@gitserverip:/path/to/projectname.git

OR:- You can create your own repository on GitHub and clone this repository.
git clone https://github.com/githubid/projectname.git #Github repo url

cd projectname
touch readme
git add .
git commit -m "Add readme file"
git push -u origin main
```

### Cloning the Ansible Playbook, Hosts, and Vars Files from a GitHub Repository

```
mkdir /path/to/store/AnsibleRepo
cd /path/to/store/AnsibleRepo
git clone https://github.com/Diptiranjan9/Ansible-Git-Automated-NetworkBackup-VersionControl.git
```
Once the repository is cloned, you can verify it using the ```tree``` command to view the files.

```
tree
.
├── README.md
├── playbook_host
│   ├── devicebackup.yml
│   └── hosts
└── python-packages-requirement.txt
```

Now you can modify your hosts file to include the appropriate usernames, passwords, and IP addresses. Test it first using the ```ansible-playbook``` command to verify that the playbook is running correctly.

Check the path of the ansible-playbook command:

```
which ansible-playbook
```

Run the following command to test the playbook:

```
/path/to/ansible-playbook /path/to/store/AnsibleRepo/playbook_host/devicebackup.yml -i /path/to/store/AnsibleRepo/playbook_host/hosts
```
Check the Git logs to ensure that commits and pushes are functioning correctly for version control. Use the following commands:
```
cd /path/to/store/your/backup/config/projectname
git log 
git status
```

Now you can schedule a cron job based on your requirements. For example, to run the task every day at midnight (00:00 hours), you can use the following cron job entry:

```
0 0 * * * /path/to/ansible-playbook /path/to/store/AnsibleRepo/playbook_host/devicebackup.yml -i /path/to/store/AnsibleRepo/playbook_host/hosts
```

## Key Takeaways:

- **Automation with Ansible:** Automate network device backups (e.g., firewalls, switches) for reliable, regular backups with minimal manual intervention.
- **Version Control:** Use Git to track changes in configuration files, ensuring version control and collaboration, while keeping sensitive data on private repositories for security.
- **Scheduled Backups:** Automate playbook execution with cron jobs to schedule daily backups (or other intervals) without manual triggers.
- **Security Practices:** Use private Git servers for sensitive configurations and disable host key checking in Ansible to streamline execution.
- **Efficient Workflow:** Combining Ansible and Git streamlines configuration management, ensuring consistent backups, easy recovery, and collaboration.


