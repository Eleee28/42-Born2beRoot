<p align="center">
    <img src="https://github.com/ayogun/42-project-badges/blob/main/badges/born2berootm.png" alt="born to be root bagde">
</p>

<h1 align="center">
    42 Born to Be Root
</h1>

Statement of the project (in [Spanish](es.subject.pdf) / [English](en.subject.pdf))

## About the project
Born2BeRoot focuses on the management if Linus systems, particularly Debian and Rocky. It covers user account configuration, strong password policies, and essential services like SSH and firewalls, while also utilizing automation scripts for system monitorization. This project aims to privide practical experience establishing secure Linux environments.

## General Information

### Debian vs Rocky
Debian and Rocky are both Linux distributions with distinct characteristics:

| Feature | Debian | Rocky |
| ------  | ----- | ----- |
| Origins | One of the oldest and most established distro | Newer, born as a replacement for CentOS (discontinued by Red Hat) |
| Community vs Commercial | Community-driven and maintained by volunteers worldwide | Has both community and commercial backing |
| Stability vs Cutting-edge | Stable and reliable, often used in production environments where stability is crucial | Aims to be a stable, enterprise ready distribution, it may offer newer packages and features |
| Package managment | Both use ``apt`` (Advanced Package Tool) |

### SELinux
**SELinux** (Security-Enhanced Linux) is a security module that supports access control security policies. It is available in both Debian and Rocky.

### AppArmor
**AppArmor** is the primary mandatory access control system for Debian. It is easier to set up and manage compared to SELinux.

### LVM (extend information to what is asked in the proyect)
Logical Volume Management (LVM) is a form of storage virtualization that offers system administrators a more flexible approach to managing disk storage space.

### Differences between Aptitude and Apt
**Apt** is the default command-line tool for managing packages on Debian-based systems. It does not provide a graphical interface.

- **Installation**: Specify the package name after ``apt install``. The package manager reads the ``/etc/apt/sources.list`` file and the contents of the ``/etc/apt/sources.list.d`` folder to retrieve the necessary packages and dependencies.

**Aptitude** is another tool that offers a command-line and text-based front-end for package management. It is not installed by default, so you need to install it using ``apt``.

| Apt | Aptitude |
| --- | -------- |
| Command-line interface | Visual interface |
| Will not fix issues it faces | Will suggest a resolution that can help dealing with issues|
| Need for a solid knowledge of Linux systems and package management | More user-friendly |

## Installation and Configuration

### Manual Disk Partitioning Information

**Types of partitions**
- **Primary**: it is the only partition in which an OS can be installed. There can only be 4 primary partitions per hard drive (3 primary and 1 extended).
- **Secondary / Extended**: there can only be one per disk and it only serves to contain logical partitions.
- **Logical**: occupies a portion (or all) of the extended/primary partition, which has been formatted with a specific type of file system (ext4) and assigned a drive. There can be a maximum of 23 logical partitions in an extended partition (linux redices this to 15).

**Mounting points**
- ``/`` : root directory
- ``/boot`` : static files of the boot loader
- ``/home`` : user home directories
- ``/tmp`` : temporary files
- ``/var`` : variable data
- ``/srv`` : data for ervices provided by this system

> The **swap space** can be used for two purposes, to extend the virtual memory beyond the  installed physical memory (RAM), and also for suspend-to-disk support.

### Sudo Installation
``sudo`` (superuser do) is a tool that allows users to execute commands with the security privileges of another user (default is the superuser).

**Installation Steps**
1. Switch to the root user: ``su``
2. Install ``sudo``: ``apt install sudo``
3. Reboot the machine to apply changes: ``sudo reboot``
4. Verify the installation: ``sudo -V``

> In order to provide a user with the ``sudo`` ability, they need to be added to a ``sudo`` enabled group, ro the username needs to be added to the sudoers file with a set of permissions. [See Users and Groups](#users-and-groups-configuration)

### Users and Groups Configuration
To manage users and groups, execute the following commands as root:

- To create a new standard user: ``adduser <name>``
- To remove a user account (``-r`` to remove home folder and files): ``userdel [-r] <name>``
- To create a new group: ``addgroup <name>``
- To add a user to a group: ``adduser <user> <group>``
- To remove a user from a group: ``deluser <user> <group>``
- Verify group creation and user addition: ``getent group <name>`` or ``cat /etc/group``

### SSH Installation and Configuration
**Secure Shell (SSH)** is a network communication protocol used for secure remote login and command execution.

**Installation Steps**
1. Update the package list: ``sudo apt update``
2. Install SSH client (usually installed by default): ``sudo apt install openssh-client``
3. Install SSH server: ``sudo apt install openssh-server``
4. Check if SSH server is running: ``service ssh status``

**Configuration Files:**
- Client configuration: ``/etc/ssh/ssh_config``
- Server configuration: ``/etc/ssh/sshd_config``

**Port Configuration:**
To change SSH to run on a different port:
1. Edit both configuration files to include: ``Port <port>``
2. To prevent root login via SSH, set (server file): ``PermitRootLogin no``

- Start the SSH service: ``service ssh start``
- To restart the service: ``service ssh restart``
- Remote Login (if ``-p`` is not added, default is port 22): ``ssh <remote_user>@<remote_host> [-p <port>]``

### Firewall (UFW) Installation and Configuration
A firewall monitors and controls incoming and outgoing network traffic based on predetermined security rules. UFW (Uncomplicated Firewall) is a user-friendly interface for managing firewall rules in Linux.

**Installation Steps:**
1. Update the package list: ``sudo apt update``
2. Install UFW: ``sudo apt install ufw``

**Activate/Deactivate Firewall:**
- To enable the firewall: ``ufw enable``
- To disable the firewall: ``ufw disable``

**Other commands:**
- View the current firewall configuration: ``ufw status``
- Allow SSH connections through a port: ``ufw allow <port>``
- Display rules in numbered form: ``ufw status numbered``
- Delete rule (by number): ``ufw delete <n>``

### Configuration of Strong Passwords Policy

**Sudo Password Policy:**
To modify the configuration of ``sudo``, edit the ``/etc/sudoers`` file using ``visudo``.

> ``Visudo`` opens a text editor that validates the syntax before saving to prevent configuration errors from blocking ``sudo``. See the list of supported Defaults parameters ([SUDOERS OPTIONS](https://manpages.debian.org/bookworm/sudo/sudoers.5.en.html#SUDOERS_OPTIONS)).

~~~ bash
Defaults passwd_tries=3       # Limits sudo authentication to 3 tries for incorrect passwords
Defaults badpass_message="Wrong password, please try again"   # Displays a personalized message when the password is incorrect
Defaults log_input, log_output           # Logs input/output for commands executed with sudo
Defaults iolog_dir="/var/log/sudo"       # Defined the logs directory
Defaults requiretty                      # TTY mode is required for security
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"   # Restrict  directories used by sudo
~~~

> **What is TTY mode?** <br>
TTY mode is a security feature that prevents users from running commands with sudo without a TTY. When requiretty is set, sudo must be run from a logged-in terminal session (a tty). This prevents sudo from being used from daemons or other detached processes like cronjobs or webserver plugins. It also means you can't run it directly from an ssh call without setting up a terminal session. This can prevent certain kinds of escalation attacks.

**Strong Passwords Policy:**
To enforce a strong password policy, use PAM (Pluggable Authentication Modules):
1. Install the package: ``sudo apt install libpam-pwquality``
2. Edit ``/etc/pam.d/common-password`` and add the following after ``retry=3``:
~~~ bash
minlen=10          # Minimum length for new password
ucredit=-1         # At least one upper-case
lcredit=-1         # At least one lower-case
dcredit=-1         # At least one digit
maxrepeat=3        # Maximum number of repeated characters
reject_username    # password cannot contain the username
difok=7            # Number of characters of new password that cannot be in old password
enforce_for_root   # Apply this policy to root user
~~~

**Password Expiration Policy:**
To set password expiration:
1. Edit ``/etc/login.defs`` and set:
~~~ bash
PASS_MAX_DAYS 30    # Password expires every 30 days
PASS_MIN_DAYS 2     # Minimum 2 days before password modification
PASS_WARN_AGE 7     # Warning 7 days before password expiration
~~~

To check or change password settings, user the ``chage`` command: ``sudo chage -l <user>``

- To change a user's password (as root): ``passwd <user>``

## Hostname
The **hostname** is a name that identifies a server within a computer network. They serve a similar purpose to domain names and allow a machine to be identified without needing the IP address.

The **Fully Qualified Domain Name** (FQDN) designates the name of a network accessible from the Internet.

- To see the actual hostname: ``hostname`` or ``cat /etc/hostname``

You can change the hostname by modifying the previous file (reboot the system afterwards) or by using ``hostname <name>`` (no need to reboot, it will only apply the changes in the current session).

- To change the FQDN, edit: ``/etc/hosts``

## Monitoring Script
A **script** is a sequence of commands stored in a file so that when the file is executed, the function of each command is performed.
Script (``monitoring.sh``) developed in bash taht will display system information.

~~~ bash
#!/bin/bash

# Architecture
arch=$(uname -a)

# Physical CPU
phCPU=$(grep "physical id" /proc/cpuinfo | wc -l)

# Virtual CPU
vCPU=$(grep "processor" /proc/cpuinfo | wc -l)

# Memory usage
memTotal=$(free --mega | awk '$1 == "Mem:" {print $2}')
memUsed=$(free --mega | awk '$1 == "Mem:" {print $3}')
memPerc=$(free --mega | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')

# Disk usage
diskTotal=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{diskT += $2} END {printf ("%.1f\n"), diskT/1024}')
diskUsed=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{diskU += $3} END {printf("%.1f"), diskU/1024}')
diskPerc=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{diskU += $3} {diskT+= $2} END {printf("%d"), diskU/diskT*100}')

# CPU load
cpuLoadAux=$[100-$(vmstat 1 2 | tail -1 | awk '{print $15}')]
cpuLoad=$(printf "%.1f" ${cpuLoadAux})

# Last boot date
boot=$(who -b | awk '$1 == "system" {print $3 " " $4}')

# LVM active
lvm=$(if [ $(lsblk | grep "LVM" | wc -l) -gt 0 ];
	then echo yes;
	else echo no;
	fi)

# TCP Connections
tcp=$(ss -a -t | grep "ESTAB" | wc -l)

# User log
users=$(who | wc -l)

# Network
ip=$(ip address | grep -1 enp0s3 | awk '$1 == "inet" {print $2}' | awk -F '/' '{print $1}') # same as hostname -I | awk '{print $1}'
mac=$(ip link | grep -1 "enp0s3" | awk '$1 == "link/ether" {print $2}')

# Sudo commands
sudoCom=$(journalctl -q _COMM=sudo | grep "COMMAND" | wc -l)


wall "     #Architecture: $arch
     #CPU physical: ${phCPU}
     #vCPU: ${vCPU}
     #Memory Usage: ${memUsed}/${memTotal}MB (${memPerc}%)
     #Disk Usage: ${diskUsed}/${diskTotal}Gb (${diskPerc}%)
     #CPU load: ${cpuLoad}%
     #Last boot: ${boot}
     #LVM use: ${lvm}
     #TCP Connections: ${tcp} ESTABLISHED
     #User log: ${users}
     #Network: IP ${ip} (${mac})
     #Sudo: ${sudoCom} cmd"
~~~

## Crontab
**Crontab** is a background process manager. The indicated processes will be executed at the time specified in the crontab file.

1. Configure crontab by editing the crontab file (as root user): ``sudo crontab -u root -e``
2. Add ``*/10 * * * * sh /script_path`` at the end of the file (execute script every 10 min)

- To stop the cron service: ``sudo /etc/init.d/cron stop``
- To start the cron service: ``sudo /etc/init.d/cron start``

## VM Signature
To obtain the signature of a virtual machine:

1. Navigate to where the .vdi is stored
2. Run: ``shasum <vm>.vdi`` to obtain the signature

**Shasum** is a command that allows identifying the integrity of a file by checking the SHA-1 hash of the file.

> Beware that if the VM is opened after the signature is generated, it will change.

## Bonus part

### Lighttpd

**Lighttpd** is a web server designed to be fast, secure, flexible and faithful to standards. It is optimized for environments where speed is very important since it consumes less CPU and RAM than other servers.

- Packages installation: ``sudo apt install lighttpd``
- Allow connections through port 80: ``sudo ufw allow 80``
- Add the rule to include port 80 (VirtualBox interface): ``Settings > Network > Advanced > Port Forwarding``

### WordPress

**WordPress** is a content management system focused on creating any type of website.

- Install wget and zip: ``sudo apt install wget zip``

**Wget** is a command line tool used to download files from the web.

**Zip** is a command line utility for compressing and decompressing files in ZIP format.

- Go to the folder 'www': ``cd /var/www/``
- Download the latest version of WordPress: ``sudo wget https://es.wordpress.org/latest-es_ES.zip``
- Unzip the downloaded file: ``sudo unzip latest-es_ES.zip``
- Rename the old folder: ``sudo mv html/ html_old/``
- Rename the new folder: ``sudo mv wordpress/ html``
- Set permissions: ``sudo chmod -R 755 html`` 

> User will have read, write and execute permissions; group and others will only have read and execute permissions.

### MariaDB

**MariaDB** is a database used for various purposes such as data storage or logging applications.

- Install the packages: ``sudo apt install mariadb-server``
- Use a script provided by the pre-installed package to restrict access to the server (default config leaves an insecure installation): ``sudo mysql_secure_installation``
- Security configuration:
    - Don't switch to unix_socket authentication as the root acount is already protected
    - Don't change root password
    - Remove anonymous users. By default there is an anonymous user that allows anyone to log in to mariadb without needing to create their own account (it is designed for testing and to make installation more fluid)
    - Disallow root login remotely, to prevent someone from guessing the root password. We can only connect to root via SSH
    - Remove test database and access to it, any user who has access to it is also deleted
    - Reload privilege tables now so that the changes to the security configuration are applied immediately
- Access mariadb: ``mariadb``
- Create a database for WordPress: ``create database db_name;``
- Make sure it is created: ``show databases;``
- Create a user within the database: ``create user 'user_name'@'localhost' identified by 'passwd';``
- Link the user to the database (grant necessary permissions): ``grant all privileges on db_name.* to 'user_name'@'localhost';``
- Update the permissions: ``flush privileges;``
- Exit mariadb: ``exit``

### PHP
**PHP** is a programming language used to develop dynamic web applications and interactive websites. It runs on the server side.

- Install the packages: ``sudo apt install php-cgi php-mysql``

**WordPress Configuration:**
- Go to html directory: ``cd /var/www/html``
- Copy the config file and rename it: ``cp wp-config-sample.php wp-config.php``
- Edit the new file changing: 'database_name_here', 'username_here' , 'password_here'
- Enable fastcgi-php mode in lighttpd to improve the performance and speed of web server applications: ``sudo lighty-enable-mod fastcgi``
- Enable fastcgi-php to improve the performance of PHP-based web apps: ``sudo lighty-enable-mod fastcgi-php``
- Update and apply the configuration: ``sudo service lighttpd force-reload``
- Go to the browser (physical machine), type ``localhost`` and follow the installation steps

### Docker
**Docker** is an open-source platform that enables developers to automate the deployment, scaling, and management of applications by packaging them into lightweight, portable containers that run consistently across different environments.

- Install packages: ``sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin``
- Verify correct installation: ``sudo docker run hello-world``

**To use docker without root user:**
- Create docker group: ``sudo addgroup docker``
- Add the user to the group: ``sudo adduser <user> docker``

**Useful commands**
- Execute a container: ``docker run <container>``
> Options: <br>
--privileged : allow the container to access all the host's resources <br>
--name="name" : give a name to the container <br>
--restart=always : the container starts every time the host is restarted <br>
--net=host : allows accessing the resources from the host's network <br>
-v : to mount data volumes (map disk space between host and container)

- Free space of unused containers: ``docker system prune --all --volumes``
- View the containers that are executing: ``docker ps``
- View all installed containers: ``docker ps -a``
- Delete container: ``docker rm <container>``
- Stop container: ``docker stop <container>``
- Start container: ``docker start <container>``

### Node-RED
**Node-RED** is a flow-based development tool that allows users to visually wire together devices, APIs, and online services for building IoT and automation workflows using a browser-based interface.

- Install using Docker: ``docker run -p 1880:1880 -v /home/ejuarros/dockers/nodered/data:/data --name=nodeRed --restart=always nodered/node-red:3.1.3``
- Allow port 1880: ``sudo ufw allow 1880``

- Open ``loclahost:1880`` in the browser
