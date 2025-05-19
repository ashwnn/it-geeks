### ITG-DC01

This is the domain controller for the ITG domain. It is a Windows Server 2022 machine with the following specifications:

- **CPU**: 5 vCPUs  
- **RAM**: 10 GB  
- **Disk**: 50 GB  

---

#### Installation Steps

1. **Download the Windows Server 2022 ISO** from the official Microsoft website.

2. **Create a new virtual machine** in your hypervisor (e.g., VMware, VirtualBox) with the following settings:
   - **Name**: ITG-DC01  
   - **Type**: Windows Server 2022  
   - **CPU**: 5 vCPUs  
   - **RAM**: 10 GB  
   - **Disk**: 50 GB  

   In my case I am using libvirt w/ QEMU/KVM, so I created a new VM via the cockpit web interface.

3. **Mount the ISO** to the virtual machine.

4. **Start the virtual machine** and follow the installation prompts:
   - Select your language, time, and keyboard preferences.
   - Click "Install now" and choose **"Windows Server 2022 Standard (Desktop Experience)"**.
     - I chose the standard edition over datacenter edition because I don't need the extra features of datacenter edition, as well as moving VMs is much easier with standard edition.
   - Accept the license terms and select **"Custom: Install Windows only (advanced)"**.
   - Choose the disk where you want to install Windows Server and click **"Next"**.

5. **Complete the installation** by setting up the administrator account and password.

   > NOTE: By default the password policy is set to require a complex password, so you will need to set a password that meets the complexity requirements. In my case I set the password to `Admin!123`

   <img src="https://1m.cx/u/IEl6.png" width="600"/>

6. **Post-installation Network Setup**  
   Since I am using libvirt and virtio networking I need to install the virtio drivers to enable the network interface.
   - Download the [virtio drivers ISO](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/)
   - Mount the ISO to the virtual machine.  
     <img src="https://1m.cx/u/c3ER.png" width="600"/>
   - Open Device Manager and locate the network adapter (most likely under Other Devices)  
     <img src="https://1m.cx/u/hULp.png" width="600"/>
   - Right-click the network adapter and select **"Update driver"**.  
     In my case I am using Windows Server 2022, so I selected the drivers under:  
     `NetKVM\2k22\amd64`

7. **Configure the server**:
   - Set the server name to `ITG-DC01`.  
     <img src="https://1m.cx/u/gkju.png" width="600"/>
   - Set a static IP address for the server. Given I am on the network: `10.69.0.1` with a subnet mask of `255.255.0.0`:
     - IP Address: `10.69.0.10`
     - Subnet Mask: `255.255.0.0`
     - Default Gateway: `10.69.0.10` (since the server is also the gateway)
     
     > NOTE: DNS settings will be configured later once the DNS role is installed.  
     
     <img src="https://1m.cx/u/hEkN.png" width="600"/>

   - Configure Active Directory Domain Services (AD DS):
     - Open Server Manager and install the **AD DS Role**  
       <img src="https://1m.cx/u/Guto.png" width="600"/>
     - After the role is installed, **promote the server to a domain controller**:
       - Select **"Add a new forest"** and enter the root domain name as `itg.ca`.
       - Set the Directory Services Restore Mode (DSRM) password. In my case I will use `Admin!123`  
         <img src="https://1m.cx/u/r3ZR.png" width="600"/>
       - Complete the wizard and allow the server to restart.

   - Configure DHCP service:
     - Open Server Manager and add the **DHCP role**  
       <img src="https://1m.cx/u/vK3G.png" width="600"/>
