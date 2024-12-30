<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- DC and Client Setup
- Installation of Active Directory
- Internal Configuration
- Account Management

<h2>Deployment and Configuration Steps</h2>

# DC and Client Setup

To set up this Active Directory Environment, we'll need to create two virtual machines: one serving as a domain controller (DC), and one as a client. Open up Azure and create a resource group. Within the resource group, create a virtual netowrk and a subnet. 

After that, again within that same resource gropu, create the virtual machine that will serve as the DC. Remember that in order for it to function as DC, the "image" needs to be specifically Micorsoft Server OS. Either one of the two in the image below will work.

![image](https://github.com/user-attachments/assets/837e62ab-c68d-4af1-af74-a541ae6da5b2)

As a side note, as much as you can, try to deploy all of these resources to the same regional area.

After taking note of your DC credentials, you need to set the DC's NIC private IP address to be static. To do this we'll need to open the VM within Azure then go to its Network settings.

![image](https://github.com/user-attachments/assets/43e6f9a4-ec14-402f-81a3-6b6c5c13debc)

Then you need to click on your network interface settings.

![image](https://github.com/user-attachments/assets/b5305161-8250-43c1-bca4-b29a4a8f27d7)

After that within the "IP Configurations" pane, you should see an IP address named "ipconfig1". Click on it to manage its settings. Now you should see the option to change the private address from dynamic to static. Go ahead and do such now then hit "Save" at the bottom to finish up.

![image](https://github.com/user-attachments/assets/204ffdf6-489f-471c-991d-f4ce02ce6ed1)


Now that we've established the DC's private IP address as static, we're going to modify a certain setting that will allow us to test for connectivity in a little bit. Let's remote into the DC itself and open up the Control Panel. After that, click on "System and Security" -> "Windows Defender Firewall" -> "Advanced Settings". From within here, there should be a link titled "Windows Defender Firewall Properties". 

![image](https://github.com/user-attachments/assets/4b30beff-928a-4806-8d30-f0e2d8d6b36c)

Click on it and a small window should pop it containing settings of the Windows firewall. In order to allow for connectivity testing, we're going to disable the firewall. This is rarely something that you would do, but for certain scenarios like ours, it'll be acceptable. 

In order to disable the firewall, go through each of the tabs within the window that contain a "Firewall state" section (Domain Profile, Private Profile, and Public Profile), and turn the state off for each of them. 

![image](https://github.com/user-attachments/assets/111227f5-8f02-44b9-b8c5-30367a53378c)

Click "Apply" then "OK" to confirm these edits. You should then see your Windows Defender Firewall window looking like this:

![image](https://github.com/user-attachments/assets/865814c3-c0c9-4ad0-b7ac-71dad6fbf3ef)

You can now close out of that window. We'll move on to setting up our client machine now.

Back in Azure, create another virtual machine, this time with a Windows 10 Pro "image"/OS. Also, make sure to configure it to be in the same geographical location as well as the same virtual network as the DC.

The next step will be to configure the client's DNS settings to the DC's private IP address. Of course for this you will need to locate the private IP address of the DC.



<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
