<p align="center">
<img src="https://camo.githubusercontent.com/e38ed1b3f8f0cff62ee2117afc871396adea4a1266dbebb482b5080db92184af/68747470733a2f2f692e696d6775722e636f6d2f705535413538532e706e67" alt="AD logo"/>
</p>

<h1>Microsoft Active Directory Laboratory</h1>
This tutorial outlines the prerequisites, installation, basic setup, and management of users in Microsoft Active Directory.<br />

<h2>Video Demonstration</h2>

- ### [YouTube: How To Deploy On-premises Active Directory Within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows 10 Pro</b> (22H2)
- Windows Server 2022 Datacenter: Azure Edition</b> 

<h2>List of Prerequisites</h2>

- Tenant/Subscription to Microsoft Azure
- Ability to use MS Remote Desktop or Windows App (on Mac) to log into a Virtual Machine.

---

## TABLE OF CONTENTS

- [Overview: What Is Active Directory](#overview-what-is-active-directory)
- [What Will We Accomplish?](#what-will-we-accomplish)
- [Important Notes](#important-notes)
- [Preparing AD Infrastructure in Azure](#preparing-ad-infrastructure-in-azure)
- [Deploying Active Directory](#deploying-active-directory)
- [Creating Users with PowerShell](#creating-users-with-powershell)
- [Group Policy and Managing Accounts](#group-policy-and-managing-accounts)

---

# Overview: What Is Active Directory?

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

Active Directory is a directory service created by Microsoft that gives organizations the ability to manage Windows domain networks. Administrators have the ability to manage networks, peripherals, software installations, upgrades, patches, rollbacks, as well as secure these surfaces. In addition, users and their respective groups, devices, and permissions can be managed all from one place, and these changes will be reflected on all nodes which are attached to the domain. It's a powerful tool that creates a cohesive work environment and makes management of large technology needs across an organization easier and more effective. 

---

# What Will We Accomplish?

In this lab, we are going to stand up an instance of Active Directory using two virtual machines in Microsoft's cloud platform, Azure. The first VM will be our domain controller, which is a server that will be running Windows Server 2022. The second VM will be a client computer which we will register to the domain created by the domain controller. Once this is done, we'll do some additional configuring of our domain controller, and then create a large batch of users in our organization using the PowerShell command line interface. Finally, with our users created, we have a "company" to work with, and we'll create Group Policy and manage user accounts to simulate a simplified version of how Active Directory would be deployed in a real-life organization. 

---

# Important Notes

Information here is current as of October of 2024. Azure and Active Directory are constantly changing and being updated by Microsoft. This tutorial is not evergreen, and changes will be made to these platforms which inevitably will not be reflected in the documentation below. You should still be able to generally work out where and how to perform the illustrated functions with help from other resources such as AI, YouTube, vendor support, etc. 

This tutorial does not cover how to use Azure to create an account, tenant (organization), subscription, or log into Azure. It also assumes that you are familiar with how to use Remote Desktop or the Windows App to log into a VM created in Azure. If you do not know how to do this, YouTube has many great resources that will walk you through the process. 

There is almost no difference in doing this on a Windows or Mac with the exception that Mac users will have to download the Windows App from the App Store to log into their VM's. Windows users will use Remote Desktop as normal. 

These builds do not assume the most secure posture possible, the goal is to set up a functioning instance of AD. It is beyond the scope of this lab to teach secure configuration of the entire domain controller, please refer to other resources further exploration of AD security measures. 

Expect that this lab, from start finish, will take you several hours, possibly longer depending on your experience level. As you become more familiar with these environments, your speed and accuracy will improve. It's recommended that you perform these exercises multiple times to develop a solid foundation in AD deployment. Of course, an IRL deployment in any given organization will be different and will depend on their particular resources, solutions, and risk appetite. 

All functions performed here in Azure can also be performed locally on your machine using VM hypervisors such as Virtualbox or VMWare to create the associated VM's given you have copies of the operating system images to install on them and something to create your virtual network connections such as ESXi. This lab does not cover local provisioning and completion of this environment. 

Windows App (Formerly Remote Desktop) For Mac Users: 
https://apps.apple.com/us/app/windows-app/id1295203466?mt=12

---

# Preparing AD Infrastructure in Azure

## 1. Create Windows Server Domain Controller VM (DC-1)

1. Log into your Azure account and navigate to Resource Groups in Azure and create a Resource Group named "Active_Directory." Note the region you are selecting, your Virtual Network and VM's will be set up in the same region. Once details have been selected, click on "Review + create" and then "Create" once validation passes. 

<p align="center">
<img src="https://i.imgur.com/Z8APBbg.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/NWVb6W8.png" alt="AD Desktop"/>
</p>

2. Using the search bar at the top, navigate to Virtual Networks and create a Virtual Network/Subnet and name it "AD-vnet." Again note that you are selecting the correct region, it should be the same as the Resource Group you created. You can expand or limit the IP address range by changing the CIDR notation under the IP Addresses blade before creation if you like, the default will work for this lab, so we'll just create it as-is. 

<p align="center">
<img src="https://i.imgur.com/dQAhYX0.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/6ioLRW0.png" alt="AD Desktop"/>
</p>

3. Create the Domain Controller VM (Windows Server 2022) named “DC-1." Navigate to Virtual Machines in the top search bar and begin creating the VM. Choose "Azure virtual machine" in the drop down. 

* Select the resource "Active_Directory" from the drop down. 
* Name the VM to "DC-1"
* Select the same region as your Resource Group and Virtual Network. 
* Availabilty Options: No infrastructure redundancy required
* Security Type: Standard
* Image: Windows Server 2022 Datacenter: Azure Edition

<p align="center">
<img src="https://i.imgur.com/DRpZOfx.png" alt="AD Desktop"/>
</p>

* Size: Recommend at minumum 2vcpus and 16GiB memory, 4 vcpus even better, but either will work. 
* Administrator Account: Username: labuser (or something you will remember)
* Password: Cyberlab123! (or something you will remember)
* Leave RDP port 3389 open so we can log into the VM. 
* Click next to Disks, and next again to the Network blade. 

<p align="center">
<img src="https://i.imgur.com/XebbkYo.png" alt="AD Desktop"/>
</p>

* Select the virtual network we created earlier called "AD-vnet"
* Review+Create, then Create the Virtual Machine and allow it time to provision. 

 <p align="center">
<img src="https://i.imgur.com/opzgFEU.png" alt="AD Desktop"/>
</p>

* After VM is created, set Domain Controller’s NIC Private IP address to be static. Select "Go To Resource" and then the following:
* Network Settings, then click on the NIC
* Click on "ipconfig"
* Next to "Allocation" select the "Static" radio button and save. 

 <p align="center">
<img src="https://i.imgur.com/QyqU8E7.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/QyqU8E7.png" alt="AD Desktop"/>
</p>

4. Log into DC-1 via Remote Desktop/Windows App and disable the Windows Firewall (for testing connectivity).
* Obtain DC-1's Public IP address and copy/paste it into RDP/Windows App.
* Login with credentials you used when you created the VM in Azure. 

<p align="center">
<img src="https://i.imgur.com/zHvd7d9.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/79srvSp.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/X4tdr6v.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/sjpzITq.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/p6imHHp.png" alt="AD Desktop"/>
</p>

* Disable Windows Firewall on DC-1. (Server Manager will probably open, you can minimize it.) Once logged in, type wf.msc in the search bar and hit enter to open the firewall service window and disable the firewall. 

<p align="center">
<img src="https://i.imgur.com/nbw9yaL.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/2Pj0Kj7.png" alt="AD Desktop"/>
</p>

## 2. Create Windows 10 Client VM (Client-1)

1. Create the Client VM (Windows 10) named “Client-1." Create the VM using all of the same parameters as DC-1, with the exception that we will be installing a Windows 10 Pro image on this machine. 

	* Create the VM using all of the same parameters as before, except:
	* Name this VM "Client-1"
	* Install a Windows 10 image on this machine.
	* Admin Credentials can be identical to DC-1 or you can change them to whatever you like. 
	* At the end of the Basics section, select the box confirming eligible Windows 10/11 license. 

<p align="center">
<img src="https://i.imgur.com/jS0f4Nr.png" alt="AD Desktop"/>
</p>

2. Attach this machine to the same region and virtual network as DC-1. 
	* After VM is created, set Client-1’s DNS settings to DC-1’s Private IP address

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3.  In Azure: 
	* Restart Client-1
	* Log into Client-1 via RDP (Windows App/Remote Desktop)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

4. Attempt to ping DC-1’s private IP address
	* Ensure ping has succeeded. If not, you may not have provisioned your virtual networks correctly in Azure for the VM's, or you have not completely disabled the Firewall on DC-1.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. From Client-1, open PowerShell as Admin and run ipconfig /all
	* The output for the DNS settings should show DC-1’s *private* IP Address

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

---

# Deploying Active Directory

## 1. Install Active Directory

1. Login to DC-1 and install Active Directory Domain Services

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Restart and then log back into DC-1 as user: mydomain.com\labuser

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

## 2. Create a Domain Admin user within the domain. 

1. In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Create a new OU named “_ADMINS”

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Create a new employee named “Jane Doe” (same password) with the username of “jane_admin” / Cyberlab123!

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

4. Add jane_admin to the “Domain Admins” Security Group

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. Log out / close the connection to DC-1 and log back in as “mydomain.com\jane_admin”

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

6. User jane_admin as your admin account from now on. 

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

## 3. Join Client-1 to your domain (mydomain.com)

1. Login to Client-1 as the original local admin (labuser) and join it to the domain (computer will restart)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Login to the Domain Controller and verify Client-1 shows up in ADUC

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Create a new OU named “_CLIENTS” and drag Client-1 into there

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

## 4. Setup Remote Desktop for non-administrative users on Client-1

1. Log into Client-1 as mydomain.com\jane_admin

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Open system properties

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Click “Remote Desktop”

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

4. Allow “domain users” access to remote desktop

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. You can now log into Client-1 as a normal, non-administrative user now
	* Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

---

# Creating Users with PowerShell

1. Login to DC-1 as jane_admin

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Open PowerShell_ise as an administrator

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Create a new File and paste the contents of the [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

4. Run the script and observe the accounts being created

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. When finished, open ADUC and observe the accounts in the appropriate OU　(_EMPLOYEES)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

6. Attempt to log into Client-1 with one of the accounts (take note of the password in the script)

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

---

# Group Policy and Managing Accounts

# 1. Configure Account Lockout Threshold in Group Policy

1. Open the Group Policy Management Console (GPMC)
	1. Log in to a machine with Group Policy Management Console installed (typically, the Domain Controller).
	2. Click Start, and type gpmc.msc in the search box, then press Enter. This opens the Group Policy Management Console.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Create or Edit a Group Policy Object (GPO)
	1. In the GPMC, navigate to the Group Policy Objects section.
	2. Right-click Group Policy Objects and select New to create a new GPO, or right-click an existing GPO and select Edit to modify it.
		* Give the new GPO a descriptive name if you're creating a new one, like "Account Lockout Policy".

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Navigate to the Account Lockout Policy Settings
	1. In the Group Policy Management Editor, expand the following:
		* Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>


4. Configure Account Lockout Policy Settings
	* You will see three primary settings that you need to configure:
	1. Account Lockout Duration:
		* Definition: The time in minutes that an account remains locked before it is automatically unlocked.
		* Configuration: Double-click on this setting, select Define this policy setting, and then set the duration (e.g., 30 minutes).
	2. Account Lockout Threshold:
		* Definition: The number of failed logon attempts that will trigger an account lockout.
		* Configuration: Double-click on this setting, select Define this policy setting, and then set the threshold (e.g., 3 invalid attempts).
	3. Reset Account Lockout Counter After:
		* Definition: The time in minutes after which the failed logon attempts counter is reset to 0, assuming there are no additional failed logon attempts.
		* Configuration: Double-click on this setting, select Define this policy setting, and then set the time (e.g., 15 minutes).

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. Link the GPO to an Organizational Unit (OU)
	1. Once the GPO is configured, you need to link it to the appropriate Organizational Unit (OU) or domain where you want the policy to apply.
    	2. In the GPMC, right-click the OU or domain where you want to apply the GPO and select Link an Existing GPO.
    	3. Choose the GPO you created or modified earlier and click OK.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

6. Update Group Policy
	1. You can wait for the Group Policy to propagate automatically, or you can force an update immediately.
		* On a client machine or server, open Command Prompt and type gpupdate /force, then press Enter.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

7. Verify the Policy
	1. To verify the policy, you can use the rsop.msc (Resultant Set of Policy) tool on a client machine to see the applied settings.
	2. Alternatively, you can also check the settings using the Group Policy Management Console.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

*Important Considerations:

* Account Lockout Threshold: Setting this too low (e.g., 1 or 2 attempts) can lead to unnecessary lockouts.
- Account Lockout Duration: Setting this too high can be inconvenient for users but increases security.
- Reset Account Lockout Counter After: Setting this too short could allow attackers to repeatedly attempt to log in without triggering a lockout.

By following these steps, you can successfully configure an account lockout policy in Active Directory using Group Policy.

# 2. Dealing with Account Lockouts

1. Log into DC-1
2. Pick a random user account you created previously

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Attempt multiple logins with a bad password to lock the account. 

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

4. Observe that the account has been locked out within Active Directory

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

5. Unlock the account

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

6. Reset the password

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

7. Attempt to login with the new password. 

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

## 3. Enabling and Disabling Accounts

1. Disable the same account in Active Directory

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Attempt to login with it, observe the error message

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

3. Re-enable the account and attempt to login with it.

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

## 4. Observing Logs

1. Observe the logs in the Domain Controller

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

2. Observe the logs on the client Machine

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

----

Congratulations! Having completed these labs with Active Directory, you have gained valuable hands-on experience with one of the most popular enterprise directory services and user management systems in the world. It's my sincere hope that you found this information helpful. 
