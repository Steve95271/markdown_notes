

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



# Command Substitution

Using output as input store in **variable**.

Such as use "grep" command to filtering information and store into variable.

```bash
# Get the free memory via the command, and store in the variable FREERAN
FREERAM=$(free -m | grep Mem | awk '{print $4}')
```



Instead of using the syntex **"VARIABLE=$(..some command will have output..)"** like this

Also can use backtick, such as:

```bash
FREERAM=`free -m | grep Mem | awk '{print $4}'`
```



# Exporting Variables

Variable create via terminal is temporary.

If wanna store the variable, it can be done by adding the variable into

​	**Global/All user:** /etc/profile

​	**Current user:** /home/currentUser/.bashrc

**NOTE:** The variables in current user's .bashrc will overwrite the variables in global file

**Syntex:**

​	export VariableName="VALUE"



# User Input

read command can take user input.

Syntax:

​	read VariableName

​	**-p**, prompt, output the string PROMPT without a trailing newline before attempting to read

​	**-s**, do not echo input coming from a terminal 

```bash
#!/bin/bash

echo "Enter your skills:"
read SKILL

echo "Your $SKILL skill is in high Demand in the IT Industry."

read -p 'Username: ' USR
# using -sp, will print "Password", but your input will not be seen
read -sp 'Password: ' pass

echo

echo "Login Successfull: Welcome USER $USR,"
```



# IF Else in Bash

Syntax and example

1. simple read input and if else

```bash
#!/bin/bash

read -p "Enter a number: " NUM
echo

if [ $NUM -gt 100 ]
then
   echo "We have entered in IF block."
   sleep 3
   echo "Your Number is greater than 100"
   echo
   date
else
  echo "You have entered number less than 100."
fi


echo "Script execution completed successfully."
```

2. Identify network interfaces

```bash
#!/bin/bash

value=$(ip addr show | grep -v LOOPBACK | grep -ic mtu)

if [ $value -eq 1 ]
then
  echo "1 Active Network Interface found."
elif [ $value -gt 1 ]
then
  echo "Found Multiple active Interface."
else
  echo "No Active interface found."
fi
```

