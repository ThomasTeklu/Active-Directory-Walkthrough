<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Walkthrough (Azure Deployment)</h1>
This tutorial outlines the implementation of Active Directory within Azure Virtual Machines. In addition to implementation, a walkthrough of specific features utilizable within Active Directory is provided to enable the reader to become practically (that is, technically) comfortable with the environment. Through completion of this tutorial, one should attain a better understanding of Active Directory overall, as well as consituent elements such as domain controllers, client devices, administrative value, security policies, account management, etc. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computing/Networks & IP Addresses)
- Remote Desktop
- Active Directory Domain Services
- PowerShell
- Command Prompt

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2> General Deployment and Configuration Steps</h2>

- Domain Controller and Client Setup
- Installation of Active Directory
- Internal Configuration
- Account Management

# Domain Controller and Client Setup

### Virtual Machines

To set up this Active Directory environment, we'll need to create two virtual machines within Azure: one serving as a domain controller (DC), and one as a client. Open up Azure and create a resource group. Within the resource group, create a virtual network and a subnet. The default settings will suffice without any modification.

After that, within that same resource group, create the virtual machine that will serve as the DC and name it accordingly (ex: DC-1, Domain-Controller, etc). Remember that in order for it to function as a DC, the "image" needs to be specifically Micorsoft Server OS. Either one of the two options displayed in the image below will work:

![image](https://github.com/user-attachments/assets/837e62ab-c68d-4af1-af74-a541ae6da5b2)

**NOTE:** Deploy all of these resources to the same regional area.

After taking note of your DC credentials (Administrator Account credentials from the creation of the virtual machine), you need to set the DC's Network Interface Card's (NIC) private IP address to be **static**. To do this we'll need to open the VM within Azure then go to its Network settings:

![image](https://github.com/user-attachments/assets/43e6f9a4-ec14-402f-81a3-6b6c5c13debc)

Then you need to select your network interface settings:

![image](https://github.com/user-attachments/assets/b5305161-8250-43c1-bca4-b29a4a8f27d7)

After that within the "IP configurations" pane, you should see dark blue text named "ipconfig1". Click on it to manage its settings. Now you should see the option to change the private address from Dynamic to Static. Select "Static" then hit "Save" at the bottom to finish up:

![image](https://github.com/user-attachments/assets/204ffdf6-489f-471c-991d-f4ce02ce6ed1)


Now that we've established the DC's private IP address as static, we're going to modify a certain setting that will allow us to test for connectivity in a little bit. 

### Firewall Settings

Let's remote into the DC virtual machine itself and open up the Control Panel. 

**NOTE:** Upon openning/connecting a virtual machine for the first time, one of the initial configuration questions that will be asked is whether or not you want to "allow you PC to be discoverable by other PCs and devices on this network". Select "Yes". 

After that, open the path of "System and Security" -> "Windows Defender Firewall" -> "Advanced Settings". Here, there should be a link titled "Windows Defender Firewall Properties". 

![image](https://github.com/user-attachments/assets/4b30beff-928a-4806-8d30-f0e2d8d6b36c)

Click on it to open a Windows Firewall settings window. In order to allow for connectivity testing, we're going to disable the firewall. This is admittedly a bit abnormal, but for our purposes, it'll be acceptable. 

In order to disable the firewall, go through each of the tabs within the window that contain a "Firewall state" option (Domain Profile, Private Profile, and Public Profile), and turn the state off for each of them. 

![image](https://github.com/user-attachments/assets/111227f5-8f02-44b9-b8c5-30367a53378c)

Click "Apply" then "OK" to confirm these choices. Your Windows Defender Firewall window should look like this:

![image](https://github.com/user-attachments/assets/865814c3-c0c9-4ad0-b7ac-71dad6fbf3ef)

Close the window. We'll now set up our client machine.

### Client Configuration

Back in Azure, create another virtual machine, this time with a Windows 10 Pro "image"/OS. As mentioned in a prior note, make sure it's in the same geographical location as well as the same virtual network as the DC. This VM will serve as the client device/machine.

The next step will be to configure the client's DNS settings to the DC's private IP address. First, locate the private IP address of the DC. Then, go to the network settings within the client virtual machine module. Open the network interface settings, then click on "DNS Servers": 

![image](https://github.com/user-attachments/assets/9e152613-6887-4f72-8112-b04b6cbacd62)

Change the setting from "Inherit from virtual network" to "Custom", then enter in the private IP address of your DC:

![image](https://github.com/user-attachments/assets/5067db87-8b02-4271-a8d5-8a45190cb6fa)

Don't forget to hit "Save" at the top when you're finished. Now we're ready to move to the last part of the Setup Section. 

### Connectivity Test

From the Azure Portol, restart your client machine. Then remote log in to it using its Azure Admin Account credentials. Using the command prompt, ping your DC's private IP address and ensure that it succeeded: 

![image](https://github.com/user-attachments/assets/528d556c-1351-446e-8340-ae3e35dfea70)

Finally, open PowerShell and run **ipconfig /all**. The output for the DNS Server(s) should have your DC's private IP address:

![image](https://github.com/user-attachments/assets/a64c0075-e20f-48a7-b02e-ca61d7f7f08b)

With that, you have successfully completed your domain controller and client setup. In the next portion of the tutorial, you will be guided through the installation of Active Directory itself.

# Installation of Active Directory

Remote into your DC. Since the domain controller is running Windows Server OS, the initial view will be different from other OS's. You should immediately see the Server Manager window upon login:

![image](https://github.com/user-attachments/assets/32481eb9-60da-4a4d-b76e-39b1dae3d715)

Next, to turn this "blank" (so to speak) server into an Active Directory server, first click on "Add roles and features". For the "Before You Begin", "Installation Type", and "Server Selection" pages, select "Next". When you then arrive at the "Server Roles" page, you will see a long list of potential services that you could configure to run on your server. We are concerned with **Active Directory Domain Services**, so that is what we'll select:

![image](https://github.com/user-attachments/assets/09da0585-46b7-4246-8eb5-9505ab770b1e)

Upon selecting its check mark to enable the features, a pop-up window will appear asking to add additional features that are needed for Active Directory. Click "Add Features" then "Next" back in the original window. For the "Features" and "AD DS" pages select "Next" and within the Confirmation page, check the option for automatic restart:

![image](https://github.com/user-attachments/assets/d836a7dd-086b-4fa9-a6f4-fbcd15709722)

Select "Install". Upon completion, you should see the flag icon at the top right has a hazard sign next to it:

![image](https://github.com/user-attachments/assets/1229a7df-4375-4dc4-85fa-0f806d2d2180)

Click on it and then on the "Promote this server to a domain controller" prompt. From there, select the "Add a new forest" option and specify your desired domain name (i.e. [yourdomainname].com):

![image](https://github.com/user-attachments/assets/fbdc21a2-56e7-4f8a-9159-f5c4c3f0f51a)

Hit "Next" to move on to "Domain Controller Options", then set your Directory Services Restore Mode password. 

Select "Next" for the following pages until you arrive at "Prerequisites Check". If all checks out, you should see a small banner at the top of the window saying as much:

![image](https://github.com/user-attachments/assets/bfa743ff-76c2-45c4-ab19-4e6c915d028e)

That's your green light to go ahead and hit "Install". Upon completion, your virtual machine will restart to enforce the changes and you will subsequently be logged out. 

Upon logging back in, your initial credentials won't work. You'll have to add your domain name and a backslash to the front of your username (mydomain.com\ExampleUsername). Your password will remain as it was. Log in as described above, and we'll move on to Internal Configuration.

# Internal Configuration

Our first step in this section will be to create a Domain Admin user within the domain.

Open up Active Directory Users and Computers within the DC either by searching for it in the Windows search bar, or by clicking the start menu, then locating to Windows Administrative Tools:

![image](https://github.com/user-attachments/assets/4026b7fe-b68f-491b-99e9-8ee0aa5b9512)

Right click **on** your domain name itself then select "New" then "Organizational Unit". Name it "_EMPLOYEES" (include the underscore in the front). Using the same technique, create another OU named "_ADMINS". 

Within the Employees container (remember, right clicking on it) create a "user". We'll name them "Jane Doe" with a username of "jane_admin". The password will be up to you, just remember to keep track of it:

![image](https://github.com/user-attachments/assets/cb62fd21-c3aa-45ec-a55b-e2df1c423a0c)

Select "Next" to create password. Deselect the check mark next to "User must change password at next login":

![image](https://github.com/user-attachments/assets/0c26c3d0-d045-4feb-b973-55c32b043ae3)

Select "Next" then "Finish" and you should now have a new user named "Jane Doe" within your employees container. Now to actually give this account admin privileges, we need to add it to the "Domain Admins" Security Group. 

Right click on the account and hit "Properties". Within the Properties window, go to the "Member Of" tab and select "Add":

![image](https://github.com/user-attachments/assets/f3e340e7-9cec-4edc-8cd3-135aee637cfb)

You'll be taken to another window to select which groups you want to add this account to. In the "Enter the object names" field enter "Domain Admins" and hit enter. Automatically you'll be taken back to the "Members Of" tab and you should see "Domain Admins" within the "Members Of" panel now: 

![image](https://github.com/user-attachments/assets/48a8089b-cbe4-4c8f-afc0-affaf05adb7c)

To wrap this step up, click "Apply" and "OK" at the bottom of the window.

For organizational measures, right click on Jane Doe and select the "move" option. Move her to the newly-created _ADMINS organizational unit. Open it to confirm the move was succesful. 

Log out and log back in as "mydomain.com\jane_admin" and use jane_admin as your admin account from here on out.

To sum up so far, you established Active Directory services on your server, created your domain, and created your admin account for the DC. Having focused on our server-side configuration so much, let's move towards setting up the client-side as well.

### Joining Client to Domain

Log in to the client machine as the original local admin. Then, right click on the start icon in the bottom left corner. From there, click "System" and within the window that pops up, select "System Protection". 

Switch to the "Computer Name" tab and that's where you will see the option to "rename this computer or change its domain or workgroup":

![image](https://github.com/user-attachments/assets/7adc325b-b972-4ba0-91e6-09e8dee166a8)

From there, select the "Domain" option under "Member of" and enter in your domain name in the field. When you click "OK", a Windows Security panel will appear, asking you to use an account with permission to join the domain. This is where your DC admin account will come in handy. 

Use your DC admin credentials (with the domain specification at the beginning) to authorize joining the domain. When that's done, you'll get a "Welcome to the domain" message. Close that and your device will automatically restart to enforce the changes just made.

Verify that your client device was joined to the domain by logging back into your DC and checking the "Computers" container in Active Directory Users and Computers (ADUC). Create a new OU named "_CLIENTS" and put your client device in there.

With that, you have successfully joined your client machine to your domain.

### Setup Remote Desktop

Now you will enable Remote Desktop for non-administrative users on the client device. 

Log into the client device, this time as your domain admin account. Right click on the start icon then select "System". Inside of that, select "Remote Desktop" and within the "Remote Desktop Users" window that pops up select "Add". 

You will then enter "Domain Users" in the object names field and hit enter. Select "Ok". 

You've now enabled any domain user (even non-administrative ones) to remotely log into the client device.

### Creating Users

Log in to your DC as jane_admin. Open PowerShell_ise **as an administrator** and within the new file, paste the contents of [this script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1). Run the script and observe the accounts being created, the process of which can be seen by clicking on the image below:

[![Alt text](https://i.imgur.com/MCj3oVf.png)](https://imgur.com/2NlibYS)


When finished, open up ADUC and observe the accounts in the appropriate OU (_EMPLOYEES).

As a final test, attempt to log into the client device with one of the accounts (remember to take note of the password from the top of the imported script).


# Account Management

The last part of this Active Directory tutorial concerns enabling and unlocking accounts, as well as reseting passwords. First things first, get logged in to your DC if you're not already. Locate to the employees OU from within ADUC and select a random user. Take note of its username (again, password is at the top of the script linked to in the "Creating Users" subsection) and then attempt to log into the client device with a bad password 10 times. 

Notice that nothing is happening. You're simply not able to log in and offered another chance to try. Typically you would want some type of prevention against this to ensure that brute force attacks and the like are unsuccessful. That's what we'll move on to setting up right now. 

### Configuring Account Lockout Group Policy

Within the DC, type "gpmc.msc" in the Windows seach bar, then press Enter. This will open the Group Policy Management Console:

![image](https://github.com/user-attachments/assets/c7e9824c-c574-434d-b3b0-61b463cc93c4)

Expand the Forest, then the Domains, then your domain until you see "Group Policy Objects". Right click on it then select "New" to create a new Group Policy Object (GPO):

![image](https://github.com/user-attachments/assets/8b8a6173-d041-40ba-aabc-f16e2c7cf705)

Give the new GPO the name "Account Lockout Policy". Right click that newly created GPO and select "Edit" to open the Group Policy Management Editor. 

Expand the following: "Computer Configuration" -> "Policies" -> "Windows Settings" -> "Security Settings" -> "Account Policies" -> "Account Lockout Policy".

There are three main settings that you'll configure here: 
1. Account Lockout Duration
2. Account Lockout Threshold
3. Reset Account Lockout Counter After

To edit any of them, double click on them. Start with Account Lockout Duration (time that an account remains locked before it's automatically unlocked again). Select "Define this policy setting" and set it to **30 minutes**. Hit "Apply" then "OK".

Note that upon changing this setting, you'll automatically be offered other suggested value changes for the two remaining categories:

![image](https://github.com/user-attachments/assets/a8093460-018b-4c83-900f-132510d5c42d)

Hit "OK" to move on as you'll change them right after anyways. For Account Lockout Threshold (amount of failed login attempts that trigger a lockout), define **3 invalid login attempts**, and for Reset Account Lockout Counter After (time until the failed login attempts counter is reset) define **15 minutes**.

After setting those values, you'll need to link the GPO to an OU or domain wherein you want the policy to apply. Go back to the Group Policy Manager Window and right-click on your domain, then select "Link an Existing GPO". Within that window, select the one you just created: Account Lockout Policy.

**NOTE**: While within the Group Policy Management window, underneath the "Group Policy Objects" branch, there is a policy called "Default Domain Policy". Since there is a parameter within this policy that interferes with our desired settings, we're going to disable it. Right click on "Default Domain Policy" then open "GPO Status" and select "All Settings Disabled":

![image](https://github.com/user-attachments/assets/6d150a1a-e97f-4055-b180-8dd7e7efef62)


We'll now force a Group Policy update by opening Command Prompt and typing in "gpupdate /force", then hitting enter. To verify the policy update, you can use the rsop.msc tool on a **client machine** to see the applied settings.

Log into your client machine using your admin account, go to the Windows search box, type in "rsop.msc" and hit enter. Using the same expansion path as before ("Computer Configuration" -> "Policies" -> "Windows Settings" -> "Security Settings" -> "Account Policies" -> "Account Lockout Policy"), check to see if the settings you configured are in effect.

![image](https://github.com/user-attachments/assets/963975ae-c0ab-4a47-ab4e-7df46bf5da15)

With that, you've just configured your domain's account lockout policy! Now let's see how to deal with account lockouts themselves.

### Account Lockouts

Using the same random account that you attempted to log in 10 times with, try to log in again to the client machine, this time with a bad password **three times**. Because of the GPO that we set up, you should receive a message saying that this account has been locked: 

![image](https://github.com/user-attachments/assets/219b3968-7517-4cbf-b792-668d4213b37f)

If you do, you're GPO was successfully configured. Now you'll log into the DC and unlock the account from within ADUC. 

Within your domain, right click on the employees OU and select "find". Type in the name of the account you locked out and hit enter. The account should pop up in the bottom section of the window. In turn, right click on the account and select "properties". Then navigate to the Account tab. Within it, you'll see a blank check box saying "Unlock account" next to it along with a small description of the account's status: 

![image](https://github.com/user-attachments/assets/284177ea-f63d-4f40-8915-823707cb7e98)

Select the box then "Apply" and "OK" to finish unlocking it. Return to the client device and log in with the proper password to test the unlock.

### Enabling and Disabling Accounts

Returning to the DC, open up ADUC and find the account within the employees OU. You can use the same "find" feature as used in the "Account Lockouts" section. Right click on the account and, this time, select "Disable Account":

![image](https://github.com/user-attachments/assets/a8fd6210-cf73-49d8-8c96-847fd394f800)

Attempt to log in with it and take note of the message that appears. After that, go back to ADUC and re-enable the account using the same method you used to disable (only difference being that "Disable Account" will be "Enable Account"); log in with the account afterwards to ensure it's been enabled again.

Last but not least, we'll dive a little into observing logs.

### Observing Logs

From within the client user account, type "eventvwr.msc" into the Windows search bar. Run it as an administrator and user your domain controller admin account credentials to authorize. 

Within the Event Viewer, in the left side column open "Windows Logs" then select "Security" to view the relevant log events. 

Remember, a part of our account lockout activities was to purposefully enter in incorrect passwords to force lock our account. Eventually, if you scroll down through the logs, you should find three "Audit Failure" logs with the Task Category of "Logon" grouped together: 

![image](https://github.com/user-attachments/assets/4d932a40-1092-4564-9a07-c0ab35d1f3ca)

If you scroll further within the logs, you'll find the same thing, this time with the 10 initial attempts that we began with: 

![image](https://github.com/user-attachments/assets/d51446e1-abfb-401d-ab1a-fbf9dfc7e821)

With that, you've seen how to observe certain action types within our event viewer.

## Conclusion

Congratulations for reaching the end of this tutorial! If all has gone according to plan, you should've gotten a better grasp with regards to both how to establish and configure your own Active Directory environment, as well as its inner workings. I would suggest going through it mutltiple times with increasingly less assistance from the tutorial steps to further strengthen intuition. Until next time!












