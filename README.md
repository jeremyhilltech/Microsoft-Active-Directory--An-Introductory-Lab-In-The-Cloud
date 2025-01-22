<p align="center">
<img src="https://camo.githubusercontent.com/e38ed1b3f8f0cff62ee2117afc871396adea4a1266dbebb482b5080db92184af/68747470733a2f2f692e696d6775722e636f6d2f705535413538532e706e67" alt="AD logo"/>
</p>

<h1>Microsoft Active Directory - An Introductory Lab In The Cloud</h1>
This tutorial outlines the prerequisites, installation, basic setup, and management of users in Microsoft Active Directory using compute power on Microsoft's Azure cloud service platform.<br />

<h2>How To Deploy On-premises Active Directory Within Azure Compute</h2>

- ### This video series to release in February 2025, check back for updates.

- ### [YouTube: A Quick Introduction to Active Directory And Context For This Lab](https://www.youtube.com)
- ### [YouTube: Preparing AD Infrastructure In Azure](https://www.youtube.com)
- ### [YouTube: Deploying Active Directory](https://www.youtube.com)
- ### [YouTube: Creating Users With PowerShell](https://www.youtube.com)
- ### [YouTube: Group Policy And Managing Accounts](https://www.youtube.com)
- ### [YouTube: Closing Comments And Observations](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop / Windows App (for Mac)
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
- [Important Notes And How To Use This Lab](#important-notes-and-how-to-use-this-lab)
- [Preparing AD Infrastructure in Azure](#preparing-ad-infrastructure-in-azure)
- [Deploying Active Directory](#deploying-active-directory)
- [Creating Users with PowerShell](#creating-users-with-powershell)
- [Group Policy and Managing Accounts](#group-policy-and-managing-accounts)
- [Closing Comments](#closing-comments)

---

# Overview: What Is Active Directory?

<p align="center">
<img src="https://i.imgur.com/r7TcRxI.png" alt="AD Desktop"/>
</p>

Active Directory is a directory service created by Microsoft that gives organizations the ability to manage Windows domain networks. Administrators have the ability to manage networks, peripherals, software installations, upgrades, patches, rollbacks, as well as secure these surfaces. In addition, users and their respective groups, devices, and permissions can be managed all from one place, and these changes will be reflected on all nodes which are attached to the domain. It's a powerful tool that creates a cohesive work environment and makes management of large technology needs across an organization easier and more effective. 

---

# What Will We Accomplish?

In this lab, we are going to stand up an instance of Active Directory using two virtual machines in Microsoft's cloud platform, Azure. The first VM will be our domain controller, which is a server that will be running Windows Server 2022. The second VM will be a client computer which we will register to the domain created by the domain controller. Once this is done, we'll do some additional configuring of our domain controller, and then create a large batch of users in our organization using the PowerShell command line interface. Finally, with our users created, we have a "company" to work with, and we'll create Group Policy and manage user accounts to simulate a simplified version of how Active Directory would be deployed in a real-life organization. 

---

# Important Notes And How To Use This Lab

You can jump straight to any section of this lab by clicking its corresponding link in the Table of Contents above. 

This is a lengthy lab and it's recommended that you tackle one section at a time. Information here is current as of October of 2024. Azure and Active Directory are constantly changing and being updated by Microsoft. This tutorial is not evergreen, and changes will be made to these platforms which inevitably will not be reflected in the documentation below. You should still be able to generally work out where and how to perform the illustrated functions with help from other resources such as AI, YouTube, direct vendor support, etc. 

This tutorial does not cover how to use Azure to create an account, tenant (organization), subscription, or log into Azure. It also assumes that you are familiar with how to use Remote Desktop or the Windows App (on Mac) to log into a VM created in Azure. If you do not know how to do this, YouTube has many great resources that will walk you through the process. 

This lab is demonstrated with the Windows App since it was built while logged in from a Mac. There is almost no difference in doing this on a Windows or Mac with the exception that Mac users will have to download the Windows App from the App Store to log into their VM's. Windows users will use Remote Desktop as normal. 

These builds do not assume the most secure posture possible, the goal is to set up a functioning instance of AD. In fact, by the end of the lab when you review security logs, you will most likely see that there have been outside login attempts on your VM's. This is normal and is not cause for panic. Because we're working in the cloud, we don't have to worry about any of our own equipment or information being compromised by bad actors. It is beyond the scope of this lab to teach secure configuration of the entire domain controller, please refer to other resources further exploration of AD security measures. 

Expect that this lab, from start finish, will take you several hours, possibly several days depending on your experience level. As you become more familiar with these environments, your speed and accuracy will improve. It's recommended that you perform these exercises multiple times to develop a solid foundation in AD deployment and management. Of course, an IRL deployment in any given organization will be different and will depend on their particular resources, solutions, and risk appetite. 

All functions performed here in Azure can also be performed locally on your machine using VM hypervisors such as Virtualbox or VMWare to create the associated VM's given you have copies of the operating system images to install on them and something to create your virtual network connections such as ESXi. This lab does not cover local provisioning and completion of that kind of environment, and extra security precautions should be taken should you decide to go this route. 

Windows App (Formerly Remote Desktop) For Mac Users: 
https://apps.apple.com/us/app/windows-app/id1295203466?mt=12

Let's begin! 

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
<img src="https://i.imgur.com/vejR3D3.png" alt="AD Desktop"/>
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
<img src="https://i.imgur.com/PP8zlfI.png" alt="AD Desktop"/>
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

* On the Network blade attach this machine to the same region and virtual network as DC-1, then create the VM. 

2. After Client-1 is created, set its DNS server to DC-1’s Private IP address
* Obtain DC-1's Private IP address by clicking on DC-1 from your Virtual Machines list.
* Once obtained, navigate to Client-1 virtual machine, select Network Settings, then select the NIC like we did earlier on DC-1.
* From here, select DNS Servers on the left, select Custom, enter DC-1's private IP address, and then save at the top. You have now assigned DC-1 as the Client-1's DNS server. 

<p align="center">
<img src="https://i.imgur.com/8KmZpGm.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/pBSH81P.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/mlYLEDO.png" alt="AD Desktop"/>
</p>

3.  In Azure: 
* Restart Client-1

<p align="center">
<img src="https://i.imgur.com/r896q2a.png" alt="AD Desktop"/>
</p>

* Log into Client-1 via RDP (Windows App/Remote Desktop) just like we did with DC-1, remember to use Client-1's public IP address and whatever credentials you set up specfically for this machine. 

4. Set up Client-1 and attempt to ping DC-1’s private IP address
* After startup, turn off all privacy settings and then accept. 
* Click "yes" to allow device to be discovered on the network (popup on the right side).
* If Edge starts up, click continue > start without your data > uncheck box > confirm and continue > uncheck box, confirm and start browsing, select appearance you prefer, select next, and finish. Do not worry if Edge fails to open a web page, it's because of how our DNS is routed to DC-1. 
* Open PowerShell by right-clicking the Start button and selecting PowerShell, open it, then attempt to ping DC-1 at it's private IP address: 

<p align="center">
<img src="https://i.imgur.com/pSZxTqB.png" alt="AD Desktop"/>
</p>

* You should see a successful ping operation, this verifies that our connection to DC-1 is good. 

<p align="center">
<img src="https://i.imgur.com/B9jB2Ag.png" alt="AD Desktop"/>
</p>

* Ensure ping has succeeded. If not, you may have provisioned your virtual networks incorrectly in Azure for the VM's, or you have not completely disabled the Firewall on DC-1. You may want to disable all firewalls on each tab of the Windows Defender Firewall Properties if your pings are failing. Disable all firewall settings for the Domain, Private, and Public tabs. Please note that this is for lab purposes only, we would leave these intact and do additional configuring if this were an operational AD instance. Your ping operation should be working. If not, check which Virtual Networks your VM's are on inside Azure. 

<p align="center">
<img src="https://i.imgur.com/zErirYa.png" alt="AD Desktop"/>
</p>

5. From Client-1, open PowerShell as Admin and run ipconfig /all
	* The output for the DNS settings should show DC-1’s *private* IP Address

<p align="center">
<img src="https://i.imgur.com/BRMyr0C.png" alt="AD Desktop"/>
</p>

---

# Deploying Active Directory

## 1. Install Active Directory

1. Login to DC-1 and install Active Directory Domain Services
* Open Server Manager from the Start menu
* At the bottom of server manager, you'll see three windows: File and Storage Services, Local Server, and All Servers. These boxes should have green lines through them, indicating they are online and configured correctly. If you see red, you will need to go into those items and see which services are not running and start them by right-clicking them and selecting Start. After starting these services, the color should update from red to green. Sometimes these server componenents do not automatically start up when Server Manager is loaded and they need to be started manually.
* Before services running

<p align="center">
<img src="https://imgur.com/EPp023Z.png" alt="AD Desktop"/>
</p>

*After services running

<p align="center">
<img src="https://i.imgur.com/g7a6K6V.png" alt="AD Desktop"/>
</p>

* To install Active Directory, open "Add roles and features," click Next for Role-based Installation, Next again until you arrive at Server Selection. Choose DC-1, click Next, then for Server Roles check the box for Active Directory Domain Service and click Next, click Next through Features, Next through AD DS, Next for confirmation and check the box for allowing restarts at the top, then click Install. You should see the following screens:

<p align="center">
<img src="https://i.imgur.com/gPFNxEe.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/chEBymr.png" alt="AD Desktop"/>
</p>

2. Promote DC-1 as a Domain Controller: 
* Set up the new forest as mydomain.com (This can be whatever you want, but for this lab mydomain.com is recommended.)
* Return to the Server Manager home screen, click on the flag at the top right, and then click on "Promote this server to a domain controller" and follow the steps to configure the Domain Controller. 

<p align="center">
<img src="https://i.imgur.com/2NuCjIH.png" alt="AD Desktop"/>
</p>

* Set DSRM password to whatever you like that you won't forget

<p align="center">
<img src="https://i.imgur.com/RjF7RCa.png" alt="AD Desktop"/>
</p>

*UNcheck box in DNS Options

<p align="center">
<img src="https://i.imgur.com/lH88rfe.png" alt="AD Desktop"/>
</p>

* Click Next through everything else until you can install.

<p align="center">
<img src="https://i.imgur.com/bCkIY6P.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/CpJf1O2.png" alt="AD Desktop"/>
</p>

3. The Domain Controller should automatically restart and then log back into DC-1 as user: mydomain.com\labuser

<p align="center">
<img src="https://i.imgur.com/pPCsnXs.png" alt="AD Desktop"/>
</p>

## 2. Create a Domain Admin user within the domain. 

1. In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”

<p align="center">
<img src="https://i.imgur.com/MGvsQzP.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/kvuedDe.png" alt="AD Desktop"/>
</p>

2. Follow the same process to create a new OU named “_ADMINS”

<p align="center">
<img src="https://i.imgur.com/hOzKS5X.png" alt="AD Desktop"/>
</p>

* Note: The reason for the underscores is that it's a simple way for us to keep track of folders that we have created vs folders present in the system when we started. If you refresh the mydomain.com tab, you'll see the folders populate at the top in alphabetical order before the rest of the system folders. 

3. Create a new employee named “Jane Doe” (same password) with the username of “jane_admin” / whatever password you want.

<p align="center">
<img src="https://i.imgur.com/0R7BPvP.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/oqrgZLt.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/pnSeUfs.png" alt="AD Desktop"/>
</p>

* Click finish

4. Add jane_admin to the “Domain Admins” Security Group
* Jane is not yet an admin, we must assign her to built-in domain admins security group. 

<p align="center">
<img src="https://i.imgur.com/e19FdPo.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/jwBmniU.png" alt="AD Desktop"/>
</p>

5. Log out / close the connection to DC-1 and log back in as “mydomain.com\jane_admin”
* We'll be using this account from here forward. 

<p align="center">
<img src="https://i.imgur.com/7o8QHff.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/KmfL0hM.png" alt="AD Desktop"/>
</p>

## 3. Join Client-1 to your domain (mydomain.com)

1. Login to Client-1 as the original local admin (labuser) and join it to the domain (computer will restart)
* Right click the start button and go to System:

<p align="center">
<img src="https://i.imgur.com/fJDi6jn.png" alt="AD Desktop"/>
</p>

* Click Rename this PC (Advanced) > Change > enter "mydomain.com" and click OK > enter jane_admin's credentials and click OK.

<p align="center">
<img src="https://i.imgur.com/2DfaFwR.png" alt="AD Desktop"/>
</p>

* Click OK on all windows and follow the prompts to restart after the Welcome to the domain message. 

2. Login to the Domain Controller and verify Client-1 shows up in ADUC
* If you are already logged into DC-1, reopen Active Directory Users and Computers and then click on the Computers file in the domain, you should Client-1 in there now. If you don't, right click the mydomain.com tab and refresh everything. 

<p align="center">
<img src="https://i.imgur.com/dp2wosd.png" alt="AD Desktop"/>
</p>

3. Using the same process as earlier, create a new Organizational Unit named “_CLIENTS” and then drag Client-1 into that folder. It should look like this when finished. 

<p align="center">
<img src="https://i.imgur.com/TJUsJzb.png" alt="AD Desktop"/>
</p>

## 4. Setup Remote Desktop for non-administrative users on Client-1

1. Log into Client-1 as mydomain.com\jane_admin

2. Open system properties and go to "Remote Desktop" 

<p align="center">
<img src="https://i.imgur.com/KUWxAru.png" alt="AD Desktop"/>
</p>

* Then to "Select User that can remotely access this PC"

<p align="center">
<img src="https://i.imgur.com/5mS0XHz.png" alt="AD Desktop"/>
</p>

3. Allow “domain users” access to remote desktop

* Type in Domain Users before you click on Check Names. It will underline when it's found the connection, then click OK and OK again to add them in. 

<p align="center">
<img src="https://i.imgur.com/4xU6SlV.png" alt="AD Desktop"/>
</p>

4. You can now log into Client-1 as a normal, non-administrative user now
* Normally you’d want to do this with Group Policy that allows you to change MANY systems at once. We will look at this later in the lab.

---

# Creating Users with PowerShell

* Now that we have created these permissions for users, it's time to make some! Let's get started. 

1. If you haven't already, login to DC-1 as jane_admin

2. Open PowerShell_ise as an administrator by searching Powershell in the search bar and right clicking on Powershell ISE.

<p align="center">
<img src="https://i.imgur.com/SgQP5vZ.png" alt="AD Desktop"/>
</p>

* With Powershell ISE open, click on Script on the top right and you will get a split screen.

<p align="center">
<img src="https://i.imgur.com/3kLFXGE.png" alt="AD Desktop"/>
</p>

3. Create a new File and paste the contents of the [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it
* In Powershell, create a new file. (The system automatically put us into a new file when we clicked the down arrow next to Script earlier. If you don't have an empty file ready for a script like the split screen image, click on New Script at the top left. It will look like this when ready: 

<p align="center">
<img src="https://i.imgur.com/5kKStkh.png" alt="AD Desktop"/>
</p>

* Hit Ctrl+S to save the file and save it to the Desktop. 

<p align="center">
<img src="https://i.imgur.com/aSH5sFV.png" alt="AD Desktop"/>
</p>
  
* Next, open the [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)link in a new tab which will take you to the Github repository.
* Next on the top right of the file, click on the double squares to copy the RAW file. 

<p align="center">
<img src="https://i.imgur.com/tqdnoPe.png" alt="AD Desktop"/>
</p>

4. Paste and Run the script in Powershell ISE and observe 10,000 user accounts being created!

<p align="center">
<img src="https://i.imgur.com/6TFtuGs.png" alt="AD Desktop"/>
</p>

* You will see users being created at the bottom, this will take a few minutes to complete.  

<p align="center">
<img src="https://i.imgur.com/Qg5BUvP.png" alt="AD Desktop"/>
</p>

5. When finished, open ADUC and observe the accounts in the appropriate OU　(_EMPLOYEES). You now see 10,000 user accounts.

<p align="center">
<img src="https://i.imgur.com/yOLD5rZ.png" alt="AD Desktop"/>
</p>

6. Attempt to log into Client-1 with one of the accounts (take note of the password in the script, in this case Password1)
* Back to RD/Windows App, log in with the new user credentials. You now have 10,000 users who can log in via Client-1 on your Active Directory domain! 

<p align="center">
<img src="https://i.imgur.com/i6KypHv.png" alt="AD Desktop"/>
</p>

* Success! 

<p align="center">
<img src="https://i.imgur.com/Pd1XlUp.jpeg" alt="AD Desktop"/>
</p>

---

# Group Policy and Managing Accounts

# 1. Configure Account Lockout Threshold in Group Policy

1. Open the Group Policy Management Console (GPMC)
	1. Log in to a machine with Group Policy Management Console installed (typically, the Domain Controller).
	2. Click Start, and type gpmc.msc in the search box, then press Enter. This opens the Group Policy Management Console.

<p align="center">
<img src="https://i.imgur.com/mwbVBTQ.png" alt="AD Desktop"/>
</p>

2. Create or Edit a Group Policy Object (GPO)
	1. In the GPMC, navigate to the Group Policy Objects section: Forest:mydomain.com > Domains > mydomain.com > Default Domain Policy
	2. Right-click Group Policy Objects and select New to create a new GPO, or right-click an existing GPO (Default Domain Policy) and select Edit to modify it.
		* Give the new GPO a descriptive name if you're creating a new one, like "Account Lockout Policy".

<p align="center">
<img src="https://i.imgur.com/jpVrXVt.png" alt="AD Desktop"/>
</p>

* You should now be in the Group Policy Management Editor

<p align="center">
<img src="https://i.imgur.com/9mBA3o5.png" alt="AD Desktop"/>
</p>

3. Navigate to the Account Lockout Policy Settings
	1. In the Group Policy Management Editor, expand the following:
		* Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.

<p align="center">
<img src="https://i.imgur.com/YCCyNqe.png" alt="AD Desktop"/>
</p>


4. Configure Account Lockout Policy Settings
	* You will see three primary settings that you need to configure, double click them to change and apply their parameters:
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
<img src="https://i.imgur.com/vK4p5wK.png" alt="AD Desktop"/>
</p>

* Close GPME

5. IF you made a new policy instead of altering the Default Domain Policy, link the GPO to an Organizational Unit (OU)
	1. Once the GPO is configured, you need to link it to the appropriate Organizational Unit (OU) or domain where you want the policy to apply. (Probably mydomain.com if you named yours the same.)
    	2. In the GPMC, right-click the OU or domain where you want to apply the GPO and select Link an Existing GPO.
    	3. Choose the GPO you created or modified earlier and click OK.

<p align="center">
<img src="https://i.imgur.com/XBDyOEf.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/DkgTruJ.png" alt="AD Desktop"/>
</p>

  * If you modified the Default Domain Policy, it will automatically be linked and will take the system 90 minutes to propogate the changes across the domain. See next step to force an update and apply the changes to the domain immediately. 

6. Update Group Policy
	1. You can wait for the Group Policy to propagate automatically, or you can force an update immediately.
		* On a *client* machine or server, log in as jane_admin, open Command Prompt and type gpupdate /force, then press Enter.

<p align="center">
<img src="https://i.imgur.com/NZq1wHA.png" alt="AD Desktop"/>
</p>

7. Verify the Policy
	1. To verify the policy, you can use the rsop.msc (Resultant Set of Policy) tool on a client machine to see the applied settings.
	2. Alternatively, you can also check the settings using the Group Policy Management Console, or the command line as Administrator: 

<p align="center">
<img src="https://i.imgur.com/iC3fWEm.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/I1yELIe.png" alt="AD Desktop"/>
</p>

*Important Considerations:

* Account Lockout Threshold: Setting this too low (e.g., 1 or 2 attempts) can lead to unnecessary lockouts.
- Account Lockout Duration: Setting this too high can be inconvenient for users but increases security.
- Reset Account Lockout Counter After: Setting this too short could allow attackers to repeatedly attempt to log in without triggering a lockout.

By following these steps, you can successfully configure an account lockout policy in Active Directory using Group Policy.

# 2. Dealing with Account Lockouts

1. Log into DC-1
2. Go to Active Directory Users and Computers and pick a random user account you created previously from the _EMPLOYEES file. 

<p align="center">
<img src="https://i.imgur.com/Dw4A414.png" alt="AD Desktop"/>
</p>

3. Attempt multiple logins on Client-1 with a bad password to lock the account. (More than 5 now that we set the Lockout Policy.)

<p align="center">
<img src="https://i.imgur.com/wD7f4MN.png" alt="AD Desktop"/>
</p>

5. Observe that the account has been locked out on RDP and within Active Directory, and then unlock the account. 

<p align="center">
<img src="https://i.imgur.com/uLgDpzD.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/i3uzqlW.png" alt="AD Desktop"/>
</p>

6. You can now log back in with your random user on Client-1 with the correct password. 

6. Password Resets
* Passwords can be reset simply by right clicking on users inside the ADUC and selecting Reset Password.
* Note that you can also unlock an account from here when doing a password reset instead of having to navigate into the User properties each time an account is locked. 

<p align="center">
<img src="https://i.imgur.com/dPNdmgx.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/IvhL97E.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/mGkfuJ9.png" alt="AD Desktop"/>
</p>

6. Attempt to login with the new password. Success! 

<p align="center">
<img src="https://i.imgur.com/t0SiSZM.png" alt="AD Desktop"/>
</p>

## 3. Enabling and Disabling Accounts

1. Disable the same account in Active Directory by going into properties or using the Find function in the ADUC and right clicking the user's name and simply select "Disable Account." You'll notice afterwards that their name is "greyed out" or will have a tiny down arrow next to their icon in the list, this indicates an inactive account on the domain. It can be hard to see. 

<p align="center">
<img src="https://i.imgur.com/McbM6t9.png" alt="AD Desktop"/>
</p>

2. Attempt to login with it, observe the error message

<p align="center">
<img src="https://i.imgur.com/At2qiiO.png" alt="AD Desktop"/>
</p>

3. Re-enable the account and attempt to login with it.

<p align="center">
<img src="https://i.imgur.com/1Fsb5vv.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/dgHjd66.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/btP7iHB.png" alt="AD Desktop"/>
</p>


## 4. Observing Logs

1. Observe the logs in the Domain Controller (They will show the failed login attempts, this is useful to see what the user behavior has been like before we go to the problem as the administrator.)
* Open Event Viewer by typing eventvwr.msc in the search bar.
* Maximize your window on the left go into Windows Logs > Security. Note in the logs a place where there was an Audit failure for Logon and view the General tab in the bottom. You will see that there is a section called Failure Information, and our reason was Unknown user name or password. These were our failed login attempts with our random user. 

<p align="center">
<img src="https://i.imgur.com/ZvIzmd9.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/30iiJyw.png" alt="AD Desktop"/>
</p>

2. Observe the logs on the client Machine
* While the user that we are logged in as does not have priveleges to see Security events on this machine, the admin does, and we can login as the admin from inside the Event Viewer.
* In the search bar type eventvwr.msc and when the program populates in the window above, look to the right and run as Admin.
* You'll enter the admin credentials for jane_admin and hit enter.
* You're now in the Event Viewer with full view of all events including the Security events, which will mirror what we saw on DC-1. 

<p align="center">
<img src="https://i.imgur.com/svQZPkU.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/hNGcMQK.png" alt="AD Desktop"/>
</p>

<p align="center">
<img src="https://i.imgur.com/9cvXXzy.png" alt="AD Desktop"/>
</p>

----

# Closing Comments
* Congratulations! Having completed these labs with Active Directory, you have gained valuable hands-on experience with one of the most popular enterprise directory services and user management systems in the world. It's my sincere hope that you found this information helpful. 
