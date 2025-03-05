# Add Script execution permission

**When creating a .sh script file in Linux, it is necessary to add an execution permission.**

- Using the command: chmod +x [scriplfile.sh]



# Variable in script

**In the script, we can use variables that make some commands reusable.**

example:

```bash
#!/bin/bash

# Variable Declaration
PACKAGE="httpd wget unzip"
SVC="httpd"
URL='https://www.tooplate.com/zip-templates/2098_health.zip'
ART_NAME='2098_health'
TEMPDIR="/tmp/webfiles"

# Installing Dependencies
echo "########################################"
echo "Installing packages."
echo "########################################"
sudo yum install $PACKAGE -y > /dev/null
echo

# Start & Enable Service
echo "########################################"
echo "Start & Enable HTTPD Service"
echo "########################################"
sudo systemctl start $SVC
sudo systemctl enable $SVC
echo

# Creating Temp Directory
echo "########################################"
echo "Starting Artifact Deployment"
echo "########################################"
mkdir -p $TEMPDIR
cd $TEMPDIR
echo

wget $URL > /dev/null
unzip $ART_NAME.zip > /dev/null
sudo cp -r $ART_NAME/* /var/www/html/
echo

# Bounce Service
echo "########################################"
echo "Restarting HTTPD service"
echo "########################################"
systemctl restart $SVC
echo

# Clean Up
echo "########################################"
echo "Removing Temporary Files"
echo "########################################"
rm -rf $TEMPDIR
echo

sudo systemctl status $SVC
ls /var/www/html/

```



# Commandline arguments

We can pass arguments to script when executing script with command line.

The number of arguments is from 0 to 9(I guess we can only pass 10 arguments).

- such as: **$0..9**
- **IMPORTANT:** argument **\$0** will be the filename, so when using arguments in script, start with **\$1**



The following script is for setting website. Inside the script, variables URL and ART_NAME have been commented. In the wget, unzip and cp commands are using arguments. In this case, we can set up any website we want using this script.

```bash
#!/bin/bash

# Variable Declaration
PACKAGE="httpd wget unzip"
SVC="httpd"
#URL='https://www.tooplate.com/zip-templates/2098_health.zip'
#ART_NAME='2098_health'
TEMPDIR="/tmp/webfiles"

# Installing Dependencies
echo "########################################"
echo "Installing packages."
echo "########################################"
sudo yum install $PACKAGE -y > /dev/null
echo

# Start & Enable Service
echo "########################################"
echo "Start & Enable HTTPD Service"
echo "########################################"
sudo systemctl start $SVC
sudo systemctl enable $SVC
echo

# Creating Temp Directory
echo "########################################"
echo "Starting Artifact Deployment"
echo "########################################"
mkdir -p $TEMPDIR
cd $TEMPDIR
echo

wget $1 > /dev/null
unzip $2.zip > /dev/null
sudo cp -r $2/* /var/www/html/
echo

# Bounce Service
echo "########################################"
echo "Restarting HTTPD service"
echo "########################################"
systemctl restart $SVC
echo

# Clean Up
echo "########################################"
echo "Removing Temporary Files"
echo "########################################"
rm -rf $TEMPDIR
echo

sudo systemctl status $SVC
ls /var/www/html/
```



# Double quotes and Single quotes

When using **echo** to print a string that contains a variable, It's needed to use "double quote". Single quote doesn't work.

The Dollor sign($) is a special character. When you want to print the following string with echo, we need to add a backward slash.

```bash
VIRUS="covid19"

# The $9 will not be print
echo "Due to $VIRUS virus company have lsot $9 milllion."

# We need to add blackwards slash(\) at the left of $9
echo "Due to $VIRUS virus company have lsot \$9 milllion."
```

