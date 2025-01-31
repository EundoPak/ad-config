<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop (RDP)
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

**Creating a new Resource group and a new V-net in Microsoft Azure**

![1](https://github.com/user-attachments/assets/b883b33c-557e-46b8-bb60-0b88be9b714d)
![2](https://github.com/user-attachments/assets/49846f6b-8ec1-4d88-9614-7c85ad1ed4f7)

**Creating a Domain Controller & Client-side VM**

Now let's add 2 VMs to the same resource group and v-net. We will first create our Domain Controller, select "Windows Server 2022 Data Edition" as it's image and create a local admin account for the DC. Then we will create another VM to join our DC, set the image for the 2nd VM as "Windows 10 Pro, version 22H2". In the Networking section make sure that both VMs are inside the same resource group, region, and v-net. 

![3](https://github.com/user-attachments/assets/dc960696-9530-4c85-a89c-966500900f3b)
![4](https://github.com/user-attachments/assets/0e92c209-4e8c-49fb-a7da-f00d4d6ec5cc)
![5](https://github.com/user-attachments/assets/b30c5b6a-c946-46b8-ad4e-0e16d7befb9d)
![6](https://github.com/user-attachments/assets/ae09fada-cc68-4220-9828-f291112842d9)

**Configuring DC-1's DNS address to static (never changes)**

The DC will also be acting as a DNS Server. When we manually configure the Client's DNS address to use our DC's address, we don't want the DC's address to ever change. We can set the private IP address of DC-1 to be static.

![7](https://github.com/user-attachments/assets/c03ba59b-6f48-4897-8e0c-ad022dc42bad)

**Connecting to DC-1 & configuring Windows Firewall**

Open Remote Desktop and connect to DC-1's public IP address using the local admin account. 

![8](https://github.com/user-attachments/assets/418a3c42-7527-4e2d-8f7a-8fdd42f1f093)

In the VM, right click on start menu and select Run, type in "wf.msc" to open up the Window's firewall configuration. Disable all firewalls (Domain, Private, and Public Profiles), this will allow anyone to connect to our Domain Controller without any issues. Click Apply and then click OK.

![9](https://github.com/user-attachments/assets/4b45a202-585a-4cd6-8ca7-90641efc2881)
![10](https://github.com/user-attachments/assets/f0aaa210-a9b0-4fd1-8ca6-6156812cd68b)

**Change DNS settings of Client-1 to point to DC-1's DNS settings (Connecting) & Restart Client-1's VM**

Configure Client-1's NIC DNS settings in Azure, select "Custom" and set the DNS server as DC-1's private IP address (10.0.0.4), click save. Client-1 was originally getting their DNS settings from the v-net, now it will be connected to our Domain Controller's DNS server. Restart Cient-1 VM within Azure to make sure the DNS settings are properly updated before launching! Select Client-1 -> click Stop -> click Start.

![11](https://github.com/user-attachments/assets/81b1efde-f5ce-4d4a-b39f-eab7ba3cdf4d)
![12](https://github.com/user-attachments/assets/0bcff1f2-81c7-47ac-9d2a-87a75e17621b)

**Client-1 -> DC-1 ping test (testing connectivity)**

Log in to Client-1's VM on RDP using Client 1's public IP address & local admin account, open up Powershell in the Windows search bar. Type the command "ping 10.0.0.4" in Powershell to test the connectivity between DC-1 and Client-1. 

![13](https://github.com/user-attachments/assets/d9edeb64-4fa9-444a-bd22-aae2655156cf)
![14](https://github.com/user-attachments/assets/2a067ba7-5b7f-4577-a2b5-b69a56facd4c)

**Example of bad connectivity and troubleshooting**

If the ping command fails and instead returns "Request timed out", there are 2 common reasons. First ensure that on DC-1's VM you DISABLE all firewalls to let anyone test connection. Second, check that BOTH VMs are in the same v-net. If they aren't in the same virtual network, DC-1 won't be able to communicate with Client-1 & vice-versa.

![16](https://github.com/user-attachments/assets/b93ad6dd-2319-47dc-acb2-bc1c396be450)


**Confirm Client 1's DNS Settings**

To make sure Client 1's DNS Settings is set to DC-1 we can use the command "ipconfig/all". Run the command on Powershell and observe the DNS Settings.

![15](https://github.com/user-attachments/assets/131b7a99-4a95-43f2-8935-dddd182c213e)

DC-1's private IP address needs to be static, if it's set to dynamic the IP address can change constantly when shutting down and reopening the server. If DC-1's IP address is set to "dynamic" and changes, VMs relying on the domain controller may have trouble locating it, leading to failures in login, policy application, or even complete domain disconnection. In short, we set DC-1's private IP address to be static for a reliable connection across all VMs in the virtual network. This concludes the tutorial for preparing the Active Directory infrastructure in Microsoft Azure.





