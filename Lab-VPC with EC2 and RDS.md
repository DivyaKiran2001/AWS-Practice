**Building Your First Amazon Virtual Private Cloud (VPC)**
SPL-13 - Version 4.2.35

© 2025 Amazon Web Services, Inc. or its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited. All trademarks are the property of their owners.

Note: Do not include any personal, identifying, or confidential information into the lab environment. Information entered may be visible to others.

Corrections, feedback, or other questions? Contact us at AWS Training and Certification.

**Lab overview**
In this lab, you create a basic Amazon Virtual Private Cloud (Amazon VPC) without using the VPC Wizard. Amazon VPC lets you provision a logically isolated section of the Amazon Web Services (AWS) cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways. You can use both IPv4 and IPv6 in your VPC for secure and easy access to resources and applications.

The VPC that you build includes a web server and an Amazon RDS database. Once you have created both, you connect your address book application running on your web server to your Amazon RDS for MySQL instance. Once you successfully configure your address book application with your RDS instance, you are be able to add and remove contacts from the address book.

**Objectives**
By the end of this lab, you should be able to do the following:

Create an Amazon Virtual Private Cloud (VPC)
Create a public and private subnets
Create an Internet gateway
Create a Route Table and add a route to the Internet
Create a security group for your web server to only allow HTTP traffic to your web server
Create a security group for your MySQL RDS instance to only allow MySQL traffic from your public subnet
Deploy a web server and a MySQL RDS instance
Configure your application to connect to your MySQL RDS instance
Icon key
Various icons are used throughout this lab to call attention to different types of instructions and notes. The following list explains the purpose for each icon:

 Note: A hint, tip, or important guidance.
 Warning: An action that is irreversible and could potentially impact the failure of a command or process (including warnings about configurations that cannot be changed after they are made).
 Caution: Information of special interest or importance (not so important to cause problems with the equipment or data if you miss it, but it could result in the need to repeat certain steps).
 Copy edit: A time when copying a command, script, or other text to a text editor (to edit specific variables within it) might be easier than editing directly in the command line or terminal.
 Expected output: A sample output that you can use to verify the output of a command or edited file.
 Refresh: A time when you might need to refresh a web browser page or list to show new information.
 Task complete: A conclusion or summary point in the lab.
Start lab
To launch the lab, at the top of the page, choose Start Lab.

 Caution: You must wait for the provisioned AWS services to be ready before you can continue.

To open the lab, choose Open Console .

You are automatically signed in to the AWS Management Console in a new web browser tab.

 Warning: Do not change the Region unless instructed.

Common sign-in errors
Error: Choosing Start Lab has no effect
In some cases, certain pop-up or script blocker web browser extensions might prevent the Start Lab button from working as intended. If you experience an issue starting the lab:

Add the lab domain name to your pop-up or script blocker’s allow list or turn it off.
Refresh the page and try again.
Lab environment
The following diagram shows the basic architecture of the lab environment:

**The network diagram**

**Image description:**

The following diagram depicts the data flow from an external user to an internet gateway. A web server hosted is in a public subnet and a MySQL RDS instance is hosted across two private subnets within a VPC.

The following list details the major resources in the diagram:

A VPC with one public subnet and two private subnets spanning across two Availability Zones.
An EC2 instance acting as a web server in the public subnet.
A MySQL RDS instance hosted across the two private subnets.
An Internet gateway allowing inbound traffic from external users.
The network traffic flows from an external user, through the internet gateway, to the web server hosted in the public subnet. If the application running on the web server requires data from the MySQL RDS instance, traffic flows from the web server to the RDS instance hosted across the private subnets within the VPC.

Amazon Virtual Private Cloud (Amazon VPC) allows you to provision a logically isolated section of the Amazon Web Services (AWS) cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways. You can use both IPv4 and IPv6 in your VPC for secure and easy access to resources and applications.

**Task 1: Create a VPC**
A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC. You can configure your VPC by modifying its IP address range, create subnets, and configure route tables, network gateways, and security settings.

In this task, you create a base VPC.

At the top of the AWS Management Console, in the search bar, search for and choose VPC.

Choose Create VPC.

On the Create VPC page:

For Resources to create, choose VPC Only.
For Name tag - optional, enter My VPC.
For IPv4 CIDR, enter 10.0.0.0/16.
Choose Create VPC.

 Task complete: You successfully created a base VPC.

**Task 2: Create Your Public Subnet**
A subnet is a range of IP addresses in your VPC. You can launch AWS resources into a specified subnet. Use a public subnet for resources that must be connected to the internet, and a private subnet for resources that won’t be connected to the internet.

In this task, you create a public subnet.

Create Your Public Subnet
In the left navigation pane, under Virtual private cloud, choose Subnets.

Choose Create subnet.

On the Create subnet page:

For VPC ID, select My VPC from the drop-down menu.
For Subnet name, enter Public 1.
For Availability Zone, select the first AZ in the list.
For IPv4 subnet CIDR block, enter 10.0.1.0/24.
Choose Create subnet.

Select Public 1.

In the Actions menu, select Edit subnet settings.

On the Edit subnet settings page:

For Auto-assign IP settings, select Enable auto-assign public IPv4 address.

 Note: Enabling auto-assign public IPv4 address provides a public IPv4 address for all instances launched into the selected subnet.

Choose Save.

 Note: Even though your subnet is labeled Public 1, it is not yet a public subnet. A public subnet must have an Internet Gateway, which you attach in the next task.

 Task complete: You successfully created a public subnet.

**Task 3: Create an Internet Gateway**
An Internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the Internet. It therefore imposes no availability risks or bandwidth constraints on your network traffic. An Internet gateway serves two purposes: to provide a target in your VPC route tables for Internet-routable traffic, and to perform network address translation (NAT) for instances that have been assigned public IPv4 addresses.

In this task, you create an Internet gateway so that traffic can access your web server.

In the left navigation pane, under Virtual private cloud, choose Internet gateways.

Choose Create internet gateway.

On the Create internet gateway page:

For Name tag, enter My IG.
Choose Create internet gateway.

In the Actions menu, select Attach to VPC.

In the Available VPCs search box, select My VPC.

Choose Attach internet gateway.

 Note: This attaches the Internet gateway to your VPC. Even though you created an Internet gateway and attached it to your VPC, you still have to tell instances within your public subnet how to get to the Internet.

 Task complete: You successfully created an Internet gateway.

**Task 4: Create a Route Table, Add Routes, And Associate Public Subnets**
A route table contains a set of rules, called routes, that are used to determine where network traffic is directed. Each subnet in your VPC must be associated with a route table; the table controls the routing for the subnet. A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table.

To use an Internet gateway, your subnet’s route table must contain a route that directs Internet-bound traffic to the Internet gateway. You can scope the route to all destinations not explicitly known to the route table (0.0.0.0/0 for IPv4 or ::/0 for IPv6), or you can scope the route to a narrower range of IP addresses; for example, the public IPv4 addresses of your company’s public endpoints outside of AWS, or the Elastic IP addresses of other Amazon EC2 instances outside your VPC. If your subnet is associated with a route table that has a route to an Internet gateway, it’s known as a public subnet.

In this task, you create a route table for internet-bound traffic, add a route to the route table to direct Internet-bound traffic to your Internet gateway, and associate your public subnet with your route table.

In the left navigation pane, under Virtual private cloud, choose Route tables.
 Note: There is currently one default route table associated with the VPC, My VPC. This routes traffic locally. Create an additional Route Table to route public traffic to your Internet Gateway.

Choose Create route table.

On the Create route table page:

For Name - optional, enter Public Route Table.
For VPC, select My VPC from the drop-down menu.
Choose Create route table.

Choose the Routes tab in the lower half of the page.

 Note: There is one route in your route table that allows traffic within the 10.0.0.0/16 network to flow within the network, but it does not route traffic outside of the network. Add a new route to enable public traffic.

Choose Edit routes.

Choose Add route.

On the Edit routes page:

For Destination, enter 0.0.0.0/0.
For Target, select Internet Gateway in the drop down and then select the My IG Internet Gateway ID.
Choose Save changes.

Choose the Subnet associations tab.

In the Explicit subnet associations section, choose Edit subnet associations.

Select Public 1.

Choose Save associations.

 Note: The subnet is now public because it is connected to the Internet via the Internet Gateway.

 Task complete: You successfully created a route table for internet-bound traffic, added a route to the route table to direct Internet-bound traffic to your Internet gateway, and associated your public subnet with your route table.

**Task 5: Create a Security Group for your Web Server**
A security group acts as a virtual firewall for your instance to control inbound and outbound traffic. When you launch an instance in a VPC, you can assign up to five security groups to the instance. Security groups act at the instance level, not the subnet level. Therefore, each instance in a subnet in your VPC could be assigned to a different set of security groups. If you do not specify a particular group at launch time, the instance is automatically assigned to the default security group for the VPC.

In this task, add a security group so that users can access your web server via HTTP.

In the left navigation pane, under Security, choose Security groups.

Choose Create security group.

On the Create security group page:

For Security group name, enter Web server.
For Description, enter My Web Server Security Group.
For VPC, select My VPC from the drop-down menu.
In the Inbound rules section:

Choose Add rule.
For Type, select HTTP from the drop-down menu.
For Source, select Anywhere-Ipv4 from the drop-down menu.
At the bottom of the screen, choose Create security group.

 Task complete: You successfully created a security group for your web server.

**Task 6: Launch a Web Server in your Public Subnet**
In this task, you launch a web server that runs an address book application.

At the top of the AWS Management Console, in the search bar, search for and choose EC2.

In the left navigation pane, under Instances, choose Instances.

Choose Launch instances .

In the Launch an instance page:

For Name and tags, enter Web Server.

For Application and OS Images (Amazon Machine Image), choose Amazon Linux 2023 kernel-6.1 AMI.

For Instance type, choose t3.micro.

For Key pair (login) section, choose Proceed without a key pair.

For Network settings section, choose Edit.

For VPC, choose My VPC.

 Note: Public 1 auto-populates under the subnet section.

For Firewall (security groups), choose Select existing security group.

For Common security groups, choose Web server.

Scroll down and expand  Advanced Details.

Copy and paste this script into the User data - optional text box:


#!/bin/bash -ex

# Update system packages
dnf -y update

# Install required packages for Amazon Linux 2023
dnf -y install httpd php-fpm php-mysqli mariadb105

# Enable and start services
systemctl enable httpd
systemctl enable php-fpm
systemctl start php-fpm
systemctl start httpd

# Change to web root directory
cd /var/www/html

# Get AWS region using IMDSv2 (Instance Metadata Service v2)
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
AWS_REGION=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/dynamic/instance-identity/document | grep -oP '(?<="region" : ").*?(?=")')

# Download and extract application files
wget https://$AWS_REGION-tcprod.s3.$AWS_REGION.amazonaws.com/courses/spl-13/v4.2.35.prod-7b724738/scripts/app.tgz

# Extract application files
tar xvfz app.tgz

# Ensure Apache can write to the config file
chown apache:root /var/www/html/rds.conf.php

# Configure PHP-FPM to use TCP socket
sed -i 's/listen = \/run\/php-fpm\/www.sock/listen = 127.0.0.1:9000/' /etc/php-fpm.d/www.conf

# Disable the default PHP module to avoid conflicts
echo "# LoadModule php_module modules/libphp.so" > /etc/httpd/conf.modules.d/10-php.conf

# Configure Apache for PHP-FPM
cat > /etc/httpd/conf.d/php.conf << 'EOF'
<FilesMatch \.php$>
    SetHandler "proxy:fcgi://127.0.0.1:9000"
</FilesMatch>
DirectoryIndex index.php index.html
EOF

# Restart services to apply configuration
systemctl restart php-fpm
systemctl restart httpd
 Note: This script is run the first time the instance is launched. It installs a web server on your EC2 instance, and runs an app that can be configured to point to your MySQL RDS instance. After you configure your RDS instance, it presents an address book that you can edit.

Choose Launch instance.

Choose View all instances.

This brings you to the Instances window where you can see your web server details.

Wait for your web server to fully launch. It should display the following:

Instance State:  Running.
Status check:  3/3 checks passed
You can choose the refresh  icon to refresh your instances status.

Select Web Server instance.

 Copy edit: Copy the Public IPv4 address address of the instance to your clipboard.

Open a new web browser tab and paste the IP address into the browser.

Press Enter to go the web page.

 Caution: Make sure that the URL begins with http:// and not https://.

 Note: If you receive an error, please wait 60 seconds and refresh  the page to try again. It can take a couple of minutes for the EC2 instance to boot and run the script that installs software.

 Expected output: An application should appear:

WebServer

 Note: Currently, you do not have a database. Once you create your RDS instance, you connect it to your web server.

 Task complete: You successfully launched a web server that runs an address book application.

**Task 7: Create Private Subnets for your MySQL Server**
To deploy your RDS database, your VPC must have at least two subnets. These subnets must be in two different Availability Zones in the AWS Region where you want to deploy your DB instance. In this task, you create two private subnets for your Amazon RDS instance.

At the top of the AWS Management Console, in the search bar, search for and choose VPC.

In the left navigation pane, under Virtual private cloud, choose Subnets.

Choose Create subnet.

On the Create subnet page:

For VPC, select My VPC from the drop-down menu.
For Subnet name, enter Private 1.
For Availability Zone, select the first AZ in the list.
For IPv4 subnet CIDR block, enter 10.0.2.0/24.
Choose Add new subnet.

On the Create subnet page:

For Subnet name, enter Private 2.
For Availability Zone, select the second AZ in the list.
For IPv4 subnet CIDR block, enter 10.0.3.0/24.
Choose Create subnet.

 Task complete: You successfully created two private subnets for your Amazon RDS instance.

**Task 8: Create a Security Group for your Database Server**
Now that your private subnets are configured, you secure the types of traffic that can access your MySQL database. In this task, you create a security group to only allow MySQL traffic from your Web server.

In the left navigation pane, choose Security Groups.

 Copy edit: Copy the Security group ID value of your Web server security group and paste it into your text editor.

Choose Create security group.

On the Create security group page:

For Security group name, enter Database.
For Description, enter My Database Security Group.
For VPC, select My VPC from the drop-down menu.
In the Inbound rules section:

Choose Add rule.

For Type, select MySQL/Aurora from the drop-down menu.

 Warning: Make sure to select MYSQL/Aurora and not MSSQL.

For Source, choose Custom and then paste the web server security group ID that you copied to your text editor.

At the bottom of the screen, choose Create security group.

This allows your web server to communicate with the database.

 Task complete: You successfully created a security group to only allow MySQL traffic from your Web server.

**Task 9: Create a Database Subnet Group**
A DB subnet group is a collection of subnets (typically private) that you create in a VPC and that you then designate for your DB instances. Each DB subnet group should have subnets in at least two Availability Zones in a given region. When creating a DB instance in a VPC, you must select a DB subnet group.

Amazon RDS instances require a database subnet group. In this task, you create a database subnet group.

At the top of the AWS Management Console, in the search bar, search for and choose Aurora and RDS.

In the left navigation pane, choose Subnet groups.

Choose Create DB Subnet Group.

On the Create DB subnet group page, in the Subnet group details section:

For Name, enter My Subnet Group.
For Description, enter My Subnet Group.
For VPC, select My VPC from the drop-down menu.
In the Add subnets section:

For Availability zones, select the first and second Availability Zones in the list.
For Subnets, select:
10.0.2.0/24
10.0.3.0/24
At the bottom of the screen, choose Create.

 Task complete: You successfully created a database subnet group.

**Task 10: Create an Amazon RDS Database**
In this task, you launch an Amazon RDS database running MySQL.

In the left navigation pane, choose Databases.

Choose Create database.

On the Create database page, in the Engine options section:

For Engine options, select MySQL.

For Engine version, select default MySQL 8.0.X.

Choose Enable RDS Extended Support.

In the Templates section, select Dev/Test.

In the Availability and durability section, select Single-AZ DB instance deployment (1 instance).

In the Settings section:

For DB instance identifier, enter myDB.
For Master username, enter admin.
For Credentials management, choose Self managed option.
For Master password, enter AdministratorPassword value from the left side of these instructions.
For Confirm password, enter AdministratorPassword value from the left side of these instructions.
In the Instance configuration section:

For DB instance class, choose Burstable classes.
Ensure db.t3.micro is selected from the drop-down menu.
In the Storage section, expand Additional storage configuration and then de-select Enable storage autoscaling.

In the Connectivity section:

For Public access, choose No.
For Existing VPC security groups, select the Database security group and de-select the default security group.
In the Monitoring section, de-select Enable Enhanced monitoring.

Scroll down and expand the Additional configuration section:

For Initial database name, enter myDB.

De-select Enable automated backups.

 Note: This turn off backups, which launches the database a little bit quicker for your lab.

De-select Enable auto minor version upgrade.

At the bottom of the screen, choose Create database.

If you receive a pop up that says Suggested add-ons for mydb, choose Close.

Choose refresh  every 60 seconds until the instance has a status of  Available.

 Task complete: You successfully deployed a MySQL database.

**Task 11: Connect Your Address Book Application to Your Database**
In this task, you connect the address book application (in your Public subnet) to your database (in your Private subnet).

Obtain Your MySQL Database Endpoint
Before you can connect your address book application to your database, you need to know the endpoint of the RDS instance. This is the address of your RDS instance.

Choose your mydb instance.

 Copy edit: In the Connectivity & security section, copy the Endpoint and paste it into your text editor.

The RDS endpoint should look similar to the example below.

mydb.ciljcs3yv1rb.us-west-2.rds.amazonaws.com

Connect to Your Database
Return to the browser tab that is displaying your web server, and then:

For Endpoint, paste the MySQL endpoint that you copied earlier.
For Database, enter myDB.
For Username, enter admin.
For Password, enter AdministratorPassword value from the left side of these instructions.
Choose Submit.

Once connected, you should see an address book with two entries.

Try adding and then removing a contact from the address book.

 Expected output:

address-book

 Note: The address book information is saved in the Amazon RDS for MySQL database.

 Task complete: You successfully connected the address book application (in your Public subnet) to your database (in your Private subnet).
