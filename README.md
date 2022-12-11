### Steps to install Nagios in Amazon EC2 Linux server

#### Step 1: Install Prerequisite Software

- Nagios requires the following packages are installed on your server prior to installing Nagios:

  1. Apache
  2. PHP
  3. GCC compiler
  4. GD development libraries

- You can use yum to install these packages by running the following commands (as ec2-user):

  ```bash
  sudo yum install httpd php
  sudo yum install gcc glibc glibc-common
  sudo yum install gd gd-devel
  ```

---

#### Step 2: Create Account Information

- You need to set up a Nagios user. Run the following commands:

  ```bash
  sudo adduser -m nagios
  sudo passwd nagios
  ```

- Type the new password twice.

  ```bash
  sudo groupadd nagcmd
  sudo usermod -a -G nagcmd nagios
  sudo usermod -a -G nagcmd apache
  ```

---

#### Step 3: Download Nagios Core and the Plugins

- Create a directory for storing the downloads.

  ```bash
  mkdir ~/downloads
  cd ~/downloads
  ```

- Download the source code of both Nagios and the Nagios plugins

  ```bash
  wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.4.9.tar.gz
  wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
  ```

---

#### Step 4: Compile and Install Nagios

- Extract the Nagios source code tar file

  ```bash
  tar zxvf nagios-4.4.9.tar.gz
  cd nagios-4.4.9
  ```

- Run the configuration script with the name of the group which you have created in the above step.

  ```bash
  ./configure --with-command-group=nagcmd
  ```

- Compile the Nagios source code.

  ```bash
  make all
  ```

- Install binaries, init script, sample config files and set permissions on the external command directory.

  ```bash
  sudo make install
  sudo make install-init
  sudo make install-config
  sudo make install-commandmode
  ```

---

#### Step 5: Customize Configuration

- Change E-Mail address with nagiosadmin contact definition you’d like to use for receiving Nagios alerts.

  ```bash
  sudo vim /usr/local/nagios/etc/objects/contacts.cfg
  ```

---

#### Step 6: Configure the Web Interface

- Run below command

  ```bash
  sudo make install-webconf
  ```

- Create a nagiosadmin account for logging into the Nagios web interface. Note the password you need it while login to Nagios web console.

  ```bash
  sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin       //Type the new password twice.
  sudo service httpd restart                                             //Restart Service
  ```

---

#### Step 7: Compile and Install the Nagios Plugins

- Extract the Nagios plugins source code tarball.

  ```bash
  cd ~/downloads
  tar zxvf nagios-plugins-2.3.3.tar.gz
  cd nagios-plugins-2.3.3
  ```

- Compile and install the plugins.

  ```bash
  ./configure --with-nagios-user=nagios --with-nagios-group=nagios
  make
  sudo make install
  ```

---

#### Step 8: Start Nagios

- Add Nagios to the list of system services and have it automatically start when the system boots.

  ```bash
  sudo chkconfig --add nagios
  sudo chkconfig nagios on
  ```

- Verify the sample Nagios configuration files.

  ```bash
  sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
  ```

- If there are no errors, start Nagios.

  ```bash
  sudo service nagios start
  ```

---

#### Step 9: Update AWS Security Group

- you need to open port 80 on the new AWS EC2 server to incoming traffic so you can connect to the new Nagios webpage

---

#### Step 10: Log in to the Web Interface

- access the Nagios web interface

  ```bash
  http://<ip-address-of-ec2-instance>/nagios/
  ```

- You’ll be prompted for the username (nagiosadmin) and password you specified earlier.
