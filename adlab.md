# **Setting Up a Basic Home Lab Running Active Directory Using Oracle VirtualBox**
## **Table of Content**

- [Objective](#objective) </br>
- [Materials and Methods](#materials-and-methods) </br>
- [Step 1 – Download Oracle Virtual Box](#step-1--download-oracle-virtual-box) </br>
- [Step 2 – Download windows 10 OS and Server 2019 ISO](#step-2--download-windows-10-os-and-server-2019-iso) </br>
- [Step 3 – Create a New Virtual Machine on VirtualBox](#step-3--create-a-new-virtual-machine-on-virtualbox) </br>
- [Step 4 – Configure DC Machine Basics](#step-4--configure-dc-machine-basics) </br>
- [Step 5 – Adding the Server 2019 ISO](#step-5--adding-the-server-2019-iso) </br>
- [Step 6 – Installing and Starting VM Server 2019](#step-6--installing-and-starting-vm-server-2019) </br>
- [Step 7 – Setting up IP Address on the DC](#step-7--setting-up-ip-address-on-the-dc) </br>
- [Step 8 – Install and Configure Active Directory (AD) Domain Services](#step-8--install-and-configure-active-directory-ad-domain-services) </br>
- [Step 9 – Set UP RSA and NAT](#step-9--set-up-rsa-and-nat) </br>
- [Step 10 – Configure DHCP Server and DNS](#step-10--configure-dhcp-server-and-dns) </br>
- [Step 11 – Configuring Domain Controller for Internet Browsing](#step-11--configuring-domain-controller-for-internet-browsing) </br>
- [Step 12 – Adding Users Using PowerShell](#step-12--adding-users-using-powershell) </br>
- [Step 13 – Creating Windows 10 VM (Client Workstation)](#step-13--creating-windows-10-vm-client-workstation) </br>
- [Step 14 – Joining the Windows 10 Client to the Domain](#step-14--joining-the-windows-10-client-to-the-domain) </br>
- [Results](#results) </br>
- [Discussion](#discussion) </br>
- [References](#references) </br>


## **Objective**
In this lab, I utilized Oracle VirtualBox to create an **Active Directory Home Lab**. The objective was to establish a foundational understanding of Active Directory’s role in network management and to gain hands-on experience in its setup and administration. This lab demonstrates how to configure a **Domain controller (DC)** and a **client workstatio**n in a secure, isolated environment.   

## **Materials and Methods**
### **Software Requirements:**
•	Oracle VirtualBox – Virtualization software to run VMs </br>
•	Windows Server ISO (e.g., Windows Server 2019) – for the domain Controller </br>
•	VirtualBox Extension Pack - for enhanced VM functionality </br>
•	Active Directory Domain Services (AD DS) – to configure the Domain Controller </br>
•	Windows 10 – for the client workstation VM </br>
### **Hardware Requirements:**
•	Computer with at least 8GB RAM </br>
•	Sufficient disk space to host virtual machines


## **Step 1 – Download Oracle Virtual Box** 
Link: https://www.virtualbox.org/wiki/Downloads
Download the VirtualBox 7.2.2 from the VirtualBox platform package by selecting the correct OS. Also, after installation make sure to download and install the VirtualBox Extension Pack which also found on the same page.

<img width="800" height="400" alt="virtualbox" src="image\img1virtualbox.png">

 
## **Step 2 – Download windows 10 OS and Server 2019 ISO** 
Link for Windows 10 IOS: https://www.microsoft.com/en-us/software-download/windows10
Link for Server 2019 ISO: https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
Follow the both links to download required OS, ISOs (windows 10 iso and server 2019 iso), make sure you select iso while downloading also 64 bit if it ask. Save both files in the same location so it will be easier to find during the setup process (I choose in desktop).  

## **Step 3 – Create a New Virtual Machine on VirtualBox** 
Now, Open Oracle VirtualBox and click New to create a new Virtual Machine (VM). 

<img width="600" height="550" alt="new vm" src="image/img2 new vm.png">

After clicking the new, we will name it as a DC (for Domain Controller) and for version choose “Other Windows (64-bit)”. 

<img width="850" height="450" alt="naming VM and choosing OS version" src="image/img3 naming vm and choosing OS Version.png">

Next, we will configure the hardware settings for the VM by assigning the 2-4 GB of RAM and a virtual hard disk of 50-60 GB (Dynamically allocated). In my case I assigned 4096 MB of memory and 2 CPUs. Click “Next” through the remaining options, then select Finish to complete the VM creation.

<img width="800" height="350" alt="memory and cpu selection" src="image/img4 memory and cpu selection.png">


## **Step 4 – Configure DC Machine Basics**
Open the settings for the **DC machine** and go to the **General** section. Under the Advanced tab, change the **Shared Clipboard** option from Disabled to **Bidirectional**, and do the same for **Drag’n’Drop**. Enabling these features allows us to copy and paste text between our host computer and the virtual machine, as well as drag files directly from our desktop into the VM.

<img width="850" height="480" alt="general setting" src="image/img5 general setting.png">

Next, navigate to the Network Settings, since this machine will serve as a Domain Controller, it needs two network interface cards (NICs). The first adapter is already set to **NAT**, which connects to our home internet. Leave this as is. Then, add a second adapter by enabling **Adapter 2** and selecting **Internal Network** from the dropdown menu. Once both adapters are configured, click **OK** to save the settings.

<img width="850" height="500" alt="adaptor setting" src="image/img6 adaptor setting.png">
 
## **Step 5 – Adding the Server 2019 ISO**
Next, open the -DC VM. At this point, we may encounter an error because the Windows Server 2019 ISO has not yet been attached.

<img width="850" height="550" alt="error" src="image/img7 error because of iso.png">

To fix this, go to the **VM settings** and navigate to the **Storage** section. Under **Controller: IDE**, we will see a CD icon. Click on it and select the **Windows Server 2019 ISO** file that we downloaded earlier. This will mount the ISO to the virtual machine and allow the installation process to begin when the VM is started.

<img width="850" height="500" alt="adding iso server" src="image/img8 adding iso server.png">

## **Step 6 – Installing and Starting VM Server 2019** 
Now start the **DC VM** and begin installing **Windows Server 2019**. Follow the installation prompts until we reach the setup for the Administrator account. Create a strong password for the Administrator user and then click **Finish** to complete the installation. Once the server installation is complete, we will need to unlock the machine by pressing **Ctrl + Alt + Delete**. Since this key combination does not work directly in VirtualBox, the easier method is to go to the top menu bar and select  **Input → Keyboard → Insert Ctrl+Alt+Delete**. This will unlock the VM and allow us to log in with the Administrator credentials we just created.

<img width="700" height="500" alt="ctrl+alt+dlt" src="image/img9 ctrl+alt+dlt.png">
 
## **Step 7 – Setting up IP Address on the DC**
The next step is to configure the IP addressing for the Domain Controller. Since the DC has two NICs, one is used for the **Internet** and the other for the **Internal Network**. The Internet adapter automatically receives an IP address from the home router, while the Internal adapter must be configured manually. To begin, click on the **Network** icon at the bottom-right of the VM screen and select **Network and Internet Settings**. From there, click **Change adapter options** to view both adapters.
To identify which adapter connects to the Internet and which connects to the internal network, right-click each adapter and select **Status → Details**. The adapter showing an IPv4 address such as **10.0.2.15** indicates that it is connected to the Internet (our home DNS Server). The adapter showing an address in the **169.254.218.74** range is the Internal Network adapter assigned by VirtualBox. Rename these adapters accordingly, for example naming the Internet adapter **INTERNET** and the internal one **INTERNAL**.

<img width="850" height="450" alt="2 adapters" src="image/img10 2 adpaters.png">

Since the **INTERNAL** adapter does not have a DHCP server, we need to assign it a static IP. Right-click on the** INTERNAL** adapter, go to **Properties**, select **Internet Protocol Version 4 (TCP/IPv4)**, and then click **Properties** again. Choose **Use the following IP address** and manually configure the **IP Address, Subnet Mask,** and **Preferred DNS Server**. Do not assign a default gateway here, because the Domain Controller itself will act as the gateway. For the DNS configuration, use either the server’s own IP address or the loopback address (127.0.0.1) since installing Active Directory will also install DNS, allowing the server to resolve queries for itself.

<img width="850" height="450" alt="assigning IP" src="image/img11 assigning IP.png">

## **Step 8 – Install and Configure Active Directory (AD) Domain Services** 
With the server now running, the next step is to install **Active Directory Domain Services (AD DS)** and create a domain. Open **Server Manager** on the DC VM and click **Add Roles and Features**. Proceed through the wizard by clicking **Next** until we reach the **Server Roles** section, then select **Active Directory Domain Services**. A pop-up window will appear asking to add required features; click **Add Features** and continue with the wizard. Click Next until we reach the end, then select **Install**.

<img width="850" height="450" alt="adding domain services" src="image/img12 ad domain service.png">

After installation completes, we will notice a caution flag at the top of the Server Manager window. This indicates that post-deployment configuration is required because AD DS has been installed but a domain has not yet been created. Click **Promote this server to a domain controller**.

<img width="850" height="450" alt="flag caution" src="image/img13 flag cautaion.png">

Now after clicking **Promote this server to a domain controller**, select **Add a new forest**, and enter a domain name (for example, mydomain.com). Continue through the prompts, set a Directory Services Restore Mode (DSRM) password, and complete the wizard. The server will automatically restart.

<img width="850" height="450" alt="mydomain" src="image/img14 mydomain.png">

Once restarted, log in using the new domain credentials. Instead of the local Administrator account, we should now see **MYDOMAIN\Administrator**. At this stage, it is best practice not to rely on the built-in Administrator account, so we will create a new domain admin user. Open **Active Directory Users and Computers** (Start → Windows Administrative Tools → Active Directory Users and Computers), and expand your new domain (mydomain.com). Right-click the domain, select **New → Organizational Unit (OU)**, and create an OU named _ADMINS. Uncheck the option to protect the container from accidental deletion. This OU will be used to manage and store administrative accounts.

<img width="850" height="550" alt="creating new domain account" src="image/img15 creating new domain account.png">

<img width="850" height="550" alt="naming admin" src="image/img16 naming admins.png">

Next, within the _ADMINS OU, right-click and select **New → User**. Provide the details for the new user, such as first name, last name, and a username. A common convention is to prefix administrative usernames with **a-** (e.g., a-alsabhj) to distinguish them from regular users. 

<img width="850" height="550" alt="mydomain" src="image/img17 creating username for admin.png">

Create a password for the account (for lab purposes, a simple password like Password1 can be used, but in production it should always meet strong password requirements). For this lab, uncheck **User must change password at next logon** and check **Password never expires**.

<img width="850" height="550" alt="choosing password for admin" src="image/img18 choosing password for admin user.png">

After that you can notice that account is created but until as an Admin yet even though we named “a- “so we make a domain admin we will right click in “properties” of that user “Alisha Bhujel” (Properties-> Member Of -> Click “Add”). Under ‘Enter the object names to select’ we will add “Domain Admins” and click ‘check Names -> OK -> Apply -> OK’. 

After creating the account, right-click the user and go to **Properties → Member Of**. Click **Add**, type **Domain Admins**, and click **Check Names** to confirm. Once applied, the user will now have domain admin privileges. Finally, sign out of the current session and log in with your newly created domain admin account by selecting **Other User** and entering the new credentials. This ensures that all further administrative tasks are performed under your dedicated domain admin account rather than the built-in Administrator.

<img width="850" height="550" alt="list of user name" src="image/img19 listed of username under admin.png">

<img width="850" height="550" alt="assigning user into admin" src="image/img20 assigning user into admin.png">


Now, we will find our own Domain Admin Account. Next, Go ahead and log out of the current user profile and log back with our created new admin account. Sign out this account and again login with admin account by clicking “other users” instead of login as an administrator account. (That which I have created as “a-alsabhj”). 

<img width="650" height="550" alt="alogin page" src="image/img21 login as a admin.png">

## **Step 9 – Set UP RSA and NAT** 
In this step, we are setting up the Remote Access Server (RAS) and configuring Network Address Translation (NAT). This allows our Windows 10 client machines to stay on the private virtual network while still being able to access the internet through the domain controller. To do this, we open Server Manager and go to **Add Roles and Features**. After clicking Next three times, we arrive at the Select Server Roles page, where we select **Remote Access**. We continue through the wizard until we reach the Select Role Services page and then choose **Routing**. Once we select routing, it will automatically enable **DirectAccess and VPN (RAS)**, which we leave selected. We then proceed by clicking Next until we reach the installation page and finish installing the new role and feature.

<img width="800" height="500" alt="DNS server installing" src="image/img22 DNS Server installing.png">

<img width="850" height="450" alt="routing" src="image/img23 routing.png">

After installation, we return to Server Manager, open Tools, and select **Routing and Remote Access**. Here, we right-click on **-DC (local)** and choose Configure and Enable Routing and Remote Access. In the configuration wizard, we select **Network Address Translation (NAT)** instead of Remote Access and continue to the next step. From the list of network interfaces, we select the adapter named **INTERNET**, which we configured earlier, since this interface connects to the external internet. We then click Next and complete the setup. 

<img width="850" height="550" alt="routing" src="image/img24 selecting configure and enable routing access.png">

<img width="850" height="550" alt="selecting NAT" src="image/img25 selecting NAT.png">

<img width="850" height="550" alt="choosing internet" src="image/img26 choosing internet.png">

<img width="850" height="550" alt="local dc" src="image/img27 -dc local.png">

Once configured, the domain controller now runs Routing and Remote Access with NAT enabled, ensuring that internal client machines can access the internet through it.


## **Step 10 – Configure DHCP Server and DNS** 
The next step is to set up a DHCP Server on our domain controller. This will allow our Windows 10 client machines to automatically receive IP addresses within a defined range, along with a subnet mask, so they can connect to the internet. The process is similar to what we see in office or school networks. To begin, open Server Manager and select **Add Roles and Features**. After clicking Next three times, select **DHCP Server**, choose Add Features, and then continue by clicking Next twice before installing the role. 

<img width="850" height="550" alt="selection of DHCP server" src="image/img27 DHCP Server Selection (2).png">

Once installed, go back to the Server Manager Dashboard, click Tools, and select **DHCP** to configure the DHCP scope. Inside DHCP, you will see options for IPv4 and IPv6. Right-click on **IPv4** and select New Scope.

<img width="850" height="550" alt="adding new scope" src="image/img28 new scope.png">

For the scope name, we will define an IP address range of **172.16.0.100–172.16.0.200**. On the IP Address Range page, enter **172.16.0.100** as the start address and **172.16.0.200** as the end address, with a subnet mask of **/24**.

<img width="850" height="550" alt="ip range" src="image/img29 ip range 100-200.png">

Continue by clicking Next three times without making changes, leaving the default lease duration of eight days. The lease duration determines how long a client can use an assigned IP address before it must be renewed—for example, cafés may use a shorter lease time of only a few hours.
Since NAT and routing are already configured on the domain controller, clients will use it as their default gateway to access the internet. Therefore, on the router configuration step, enter **172.16.0.1** as the gateway address and make sure to click Add before moving forward. Continue through the wizard and click Finish. 

<img width="850" height="450" alt="adding IP address" src="image/img30 Add IP Addess.png">

To finalize, right-click on **dc.mydomain.com** under DHCP, select Authorize, and then right-click again to Refresh. 

<img width="850" height="450" alt="authorizing IPv4&6" src="image/img31 Authorize IPV4&6.png">

At this point, you should see both IPv4 and IPv6 activated, indicated by the green upward arrows.


## **Step 11 – Configuring Domain Controller for Internet Browsing**  
In this step, we will configure the Domain Controller (DC) to allow internet browsing. Since this is a lab environment, we will adjust the settings directly from the Local Server. To do this, open **Local Server** from the Server Manager dashboard and locate the option for **IE Enhanced Security Configuration**. Disable this feature by turning it off for both Administrators and Users, then click **OK** to apply the changes. This configuration makes it easier to browse the internet from the Domain Controller during our lab setup.

<img width="850" height="550" alt="turning off IE configuration" src="image/img32 turn off IE Configuration.png">


## **Step 12 – Adding Users Using PowerShell** 
In this step, we will use a PowerShell script to create multiple Active Directory users at once, instead of manually creating each account. For this, we will use the script provided by Josh Madakor..
Link: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create Users.ps1
On the Domain Controller, open Internet Explorer, paste the link, and download the ZIP file. Save and extract the files to the desktop. Once extracted, open the names.txt file and add your own name at the top of the list, then save and close the file.

<img width="850" height="550" alt="giving name" src="image/img33 giving own name.png">

Next, open **Windows PowerShell ISE** as an administrator (Start → Search for Windows PowerShell ISE → Right-click → Run as administrator).

<img width="850" height="550" alt="running powershell" src="image/img34 running powershell.png">


Once inside PowerShell ISE, click the folder icon, browse to the extracted directory, and open the **1_CREATE_USERS.ps1 script**. 

<img width="850" height="550" alt="opening file" src="image/img35 opening file.png">


Before running it, change the execution policy to allow script execution by typing: Set-ExecutionPolicy Unrestricted. Select **Yes to All** when prompted. (Note: this setting is not recommended in real-world environments but is acceptable for our lab.)

<img width="850" height="550" alt="command line" src="image/img36 command line.png">


Finally, use the cd command to change to the directory where the script is located (for example: cd C:\Users\aalsabhj\Desktop\AD_PS-master\) and run the **1_CREATE_USERS.ps1** script. This will automatically generate and create multiple Active Directory user accounts from the names file.
Now, to get the code to generate the names, we have to go to the directory within PowerShell. I used cd to change the directory, and then went to the file location: i.e., cd C:\users\a-alsabhj\desktop\AD_PS-mater\1_CREATE_USERS.ps1

<img width="850" height="600" alt="ls" src="image/img37 ls.png">

<img width="850" height="450" alt="list of user" src="image/img38 list of users.png">

## Step 13 – Creating Windows 10 VM (Client Workstation) 
Next, we will create our client workstation by setting up a new virtual machine for Windows 10. The process is the same as creating the Domain Controller, except this time we will select the Windows 10 ISO file during the installation.

<img width="750" height="450" alt="new vm" src="image/img39 new vm (client).png">

<img width="850" height="550" alt="cleint setting1" src="image/img40 setting1 (client).png">

<img width="850" height="450" alt="cleint setting2" src="image/img41 setting2 (client).png">


Once the installation is complete, open the Command Prompt on the client machine and type ipconfig to confirm that it is connected to the internal network. In my case, the default gateway did not appear, which required troubleshooting. To resolve this, I went to **Server Manager → Tools → DHCP** on the Domain Controller. Under **IPv4**, I selected **Server Options**, added a new option for **Router**, and entered the IP address of the Domain Controller (172.16.0.1). After applying the changes, I restarted and refreshed the IPv4 configuration on the DHCP server. This ensured that the Windows 10 client could correctly receive its gateway information and communicate with the Domain Controller.

<img width="850" height="450" alt="dhcp server" src="image/img42 dhcp server&apos;.png">

<img width="850" height="500" alt="selecting router" src="image/img43 select router.png">


## **Step 14 – Joining the Windows 10 Client to the Domain**
After installing Windows 10 and verifying the network, we open the Command Prompt on the client machine and run the ipconfig command to ensure that it is connected to the internal network. At this point, we have successfully set up routing through the NICs, and our infrastructure is complete.

<img width="850" height="550" alt="ipconfig" src="image/img44 ipconfig.png">


Next, we will test our network by pinging a website like google.com. At this point, we have successfully set up routing through the NICs, and our infrastructure is complete.

<img width="850" height="550" alt="pinging google" src="image/img45 ping google.png">


The next step is to rename the workstation to “Client1” for easier identification. To do this, right-click the Windows icon, go to **Settings → Advanced System Settings**, change the computer name to **Client1**, and then click **Apply** and restart the system.

<img width="850" height="550" alt="rename" src="image/img46 rename pc.png">


Once the client restarts, we move back to the Domain Controller, open **Start → Windows Administrative Tools → Active Directory Users and Computers**, and under **mydomain.com → Computers**, we can now see “CLIENT1” listed.

<img width="850" height="550" alt="client" src="image/img47 client1.png">

This confirms that our client machine has successfully joined the domain, and any accounts created under the Users container in Active Directory will now be able to log in to this workstation. With this step completed, our Active Directory environment is fully functional😉.

<img width="850" height="600" alt="final" src="image/img48 final.png">


## **Results** 
Upon completion of the lab, the virtual machines were successfully configured and fully operational. The Domain Controller functioned as expected, with Active Directory installed and accessible through the Active Directory Users and Computers console. Test user accounts were created and managed using both the GUI and PowerShell scripts without any issues. The Windows 10 client successfully joined the domain and could authenticate using domain accounts. Network services including DHCP, NAT, and routing were correctly configured, allowing internal clients to obtain IP addresses and access the internet through the domain controller. Overall, the lab demonstrated a fully functional Active Directory environment with a working client-server infrastructure.

## **Discussion**
The lab successfully demonstrated how to set up a basic Active Directory environment using Oracle VirtualBox. By configuring a Domain Controller, DHCP, NAT, and a Windows 10 client, it became clear how Active Directory centralizes user and computer management in a network. The exercise highlighted the importance of proper network configuration, including IP addressing, routing, and DNS, to ensure clients can communicate effectively with the domain controller. Using PowerShell to create multiple user accounts also emphasized the value of automation in system administration, reducing manual effort and minimizing errors. Overall, the lab reinforced key concepts in network and domain management, providing hands-on experience that reflects real-world IT environments.

## **References**
•	Oracle VirtualBox – https://www.virtualbox.org/wiki/Downloads </br>
•	Microsoft Windows 10 ISO Download – https://www.microsoft.com/en-us/software-download/windows10 </br>
•	Microsoft Windows Server 2019 Evaluation – https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019 </br>
•	Josh Madakor – Active Directory PowerShell Scripts - https://www.youtube.com/watch?v=MHsI8hJmggI&list=PLqBeiU46hx1H--SNfTrohTOWeqkK-M2Y0
