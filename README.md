# Active Directory Lab using VirtualBox

![Screenshot (30)](https://github.com/user-attachments/assets/019a0601-6031-4f04-a932-ce90041edd6b)

# Overview

In this project, I established and configured a Windows-based network to simulate a real-world Active Directory environment. The primary objective is to gain a comprehensive understanding of Active Directory operations while also exploring the broader aspects of Windows networking. The environment consists of two virtual machines created using Oracle's VirtualBox. One machine runs Windows Server 2019, serving as the Domain Controller (DC), while the other runs Windows 10 to emulate a client experience.

# Objectives

- Create two virtual machines (One running Windows Server 2019, and one running Windows 10)
- Set up the Network Interface Cards (NICs) on the DC to ensure both local and internet connectivity
- Install Active Directory Domain Services (AD DS) on the DC
- Create an Organizational Unit and establish a dedicated Domain Admin account
- Install RAS / NAT on the DC to allow the client machine to be on the private VNet but still have access to the internet
- Implement a DHCP server on the DC to dynamically allocate IP addresses
- Use PowerShell to create 1000 user accounts on the network
- Simulate a login process on the client machine using one of the newly added users

# Configuring the NICs

![Screenshot (32)](https://github.com/user-attachments/assets/05d92c4a-e2fd-499a-b3fc-23cb11c7d671)

Having dual Network Interface Cards (NICS) allows the domain controller to connect to the public internet, while also having an internal virtual network for isolated communication within the environemnt  

There are some things to note while configuring the NICs
- The "INTERNET" NIC does not need to be configured since it will be assigned an IP from my home router
- For the "INTERNAL" NIC the domain controller will serve as the Default Gateway since the "INTERNET" NIC has access to the internet, also when Active Directory is installed it automatically installs DNS, so the domain controller will use itself as the DNS server

# Installing Active Direcotry Domain Services (AD DS)

![Screenshot (33)](https://github.com/user-attachments/assets/cc51971d-09bb-42bb-a0c9-a544ba209f93)

This will store information about the users and computers, verify their credentials, and define their access rights
- After installing the AD DS role, create the domain mydomain.com

# Create dedicated Domain Admin account

![Screenshot (36)](https://github.com/user-attachments/assets/d46de3d0-9d05-4811-88dc-e3768b28bc36)

- Create an Organizational Unit named ADMINS to put the Admin account in
- Create user then made user a member of Domain Admins

# Install RAS / NAT

![Screenshot (39)](https://github.com/user-attachments/assets/de071c1f-7d74-48f8-93db-dd1c45e3c076)

This will allow the client machine to access the internet through the domain controller
- Install the Remote Access role
- Configure routing and remote access to use NAT and select the xINTERNETx network interface which is the NIC that has access to the internet

# Setup a DHCP server on the Domain Controller

![Screenshot (45)](https://github.com/user-attachments/assets/ab920e4d-1dfe-48d4-82e1-eb02ca115eec)


This will ensure the client machine recieves an IP address dynamically
- Install the DHCP Server role
- Setup the scope for the DHCP Server
- The IP range will be 172.16.0.100 - 172.16.0.200
- The Default Gateway will be the IP of the domain controller

# Use PowerShell script to create 1000 user accounts

In an organizational network, it's not uncommon to have hundreds, or even thousands of users. This script showcases an efficient alternative to adding a batch of users manually

![Screenshot (43)](https://github.com/user-attachments/assets/c3f61deb-2005-4dcd-90bc-acb83638568b)

**Variables**
- '$PASSWORD_FOR_USERS': stores a default password ("Password1) for all users
- '$USER_FIRST_LAST_LIST': Reads content from a file named "names.txt" which contains a list of users first and last names

**Password Conversion**
- '$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force': Converts the plain text password into a secure string format

**Organizational Unit Creation**
- 'New-ADOrganizationalUnit -Name _Users -ProtectedFromAccidentalDeletion $false': Creates a new organizational unit named "_Users" in the AD, which is not protected from accidental deletion

**User Creation Loop**
- The **foreach** loop iterates through each name in the **'names.txt'** file. It then extracts the first and last name in lowercase, and puts them in the variables **'$first'** & **'$last'**. After extraction, it creates a username by taking the first letter of the first name and appendding the last name in all lowercases. The loop will then output a message showing the creation of each user, and create a new AD user with specified details.

# Client Interaction

Using the Windows 10 VM, that's configured to connect to the internal VNet, I can sign in using any of the 1000 usernames to sign into the machine.

![Screenshot (44)](https://github.com/user-attachments/assets/c7c795b8-0ae7-4ff5-b8bc-4ec91821ee94)


# Conclusion

























