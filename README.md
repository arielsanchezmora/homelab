# VCF Homelab
These are the notes of my homelab build.

This is an all-new VCF 9.1 lab. I am using William Lam's hardware recommendations and following his blog. The main blog post I am following is the [VCF 9.1 lab in a box.](https://github.com/lamw/vcf-91-in-box) but if I reference another post I'll detail it below.

# Hardware BOM

| Component | Affiliate Link |  
| --- | --- |  
| UPS | CyberPower CP1500PFCLCD PFC Sinewave [UPS Battery Backup](https://amzn.to/4xcXysT) please note this is my Amazon affiliate link and this is my first time using this UPS, YMMV |   
|Internet Router | ASUS | 
| VCF Lab Router | MikroTik CRS304-4XG-IN - check [William Lam post](https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html) for his Amazon link |  
| 10GB SFP to RJ45 connectors | Two "10Gtek 𝟭.𝟮𝟱/𝟮.𝟱/𝟱/𝟭𝟬𝗚-𝗧 𝗦𝗙𝗣+ 𝘁𝗼 𝗥𝗝𝟰𝟱 CAT.6a Copper Transceiver" recommended by William in his posts, for example this [BOM post](https://williamlam.com/2025/07/vcf-9-0-hardware-bom-for-silicon-valley-vmug.html) |  
| VCF Hosts | Two slightly different "MINISFORUM AMD Ryzen 9 9955HX MS-A2 Mini PC" recommended by William in this [hardware review](https://williamlam.com/2025/06/vmware-cloud-foundation-vcf-on-minisforum-ms-a2.html). Actual links to BOM are in [this post](https://williamlam.com/2025/07/vcf-9-0-hardware-bom-for-silicon-valley-vmug.html), but please notice he upgraded the vSAN drives to Samsung 990 PROs after some performance issues detailed in this [blog post](https://williamlam.com/2026/03/maximizing-vsan-esa-performance-on-minisforum-ms-a2.html) |   


# Cabling

## Power
The UPS has 6 battery powered outlets and 6 protected outlets.

Battery protected:
1. MS-A2 host1 
2. MS-A2 host2
3. Mikrotik Router
4. Asus Router

## Data

The Mikrotik router is being cabled in a similar manner as in the [William Lam's VCF 9 Lab Network diagram post](https://williamlam.com/2025/09/my-vcf-9-lab-network-diagram.html) and I am using the same 10G RJ45 to SFP connectors William recommended and detailed in the BOM

Port
| Port | Connects to |  
| --- | --- |  
| Port 1 | MS-A2 host SFP port 1 (10GB with adapter), trunk with vlans 30,40,50,60,70 |  
| Port 2 | MS-A2 host SFP port 1 (10GB with adapter), trunk with vlans 30,40,50,60,70 |  
| Port 3 | Future connection to Sodola 10G switch, trunk with vlans 30,40,50,60,70 |  
| Port 4 | Admin Laptop ethernet, untagged and only on vlan 30 | 
| Port 5 | ASUS uplink router LAN 1 (1GB) on the Asus internal dhcp network|  

# DNS and NTP  

Those of you with VMware experience will know first hand that DNS and NTP are crucial components on which VMware relies on heavily. I've decided to deploy the new "VCF Infrastructure Services" appliance William Lam has released (not a VMware product, this is William's brain child for the vCommunity):  

https://williamlam.com/2026/07/vcf-infrastructure-services-vis-appliance-for-vcf-9-1-lab-poc.html

To help install the environment, I have an older i5/64GB Intel NUC running a fresh unlicensed esx 9.1 install (with allowLegacyCPU=true as a shit+o boot option) on 172.30.0.8 (which I haven't seen used elsewhere). I added vlan 30 (which is the management vlan used throughout) in the VM Network default portgroup, and make sure the time on the host is correct and the certificate is valid, as this can make the ovftool deployment fail, amongst other things.  

I will deploy VIS v1.0.3 there and assign the IP address 172.30.0.9 which I see in the deployment script located in the documentation:  

https://lamw.github.io/vcf-infrastructure-service-appliance/deploy.html#standalone-esx-deployment-with-ovf-tool

The ovf tool is located [here](https://developer.broadcom.com/tools/open-virtualization-format-ovf-tool/latest) but if you are in Windows like me, note the deployment script is in Linux format (.sh) so you may want to use the WSL and browse to /mnt/c to access your Windows file structure. I used the following to add the extracted ovftool path inside WSL.  

```echo 'export PATH="$PATH:/path/to/your/folder"' >> ~/.bashrc  

Deployment script to ESX is in the scripts folder in Github:  

https://github.com/lamw/vcf-infrastructure-service-appliance/tree/main/scripts

I modified mine a little to match my environment, if you want to see it, it's located in this same folder as deploy_vis_esx_asm.sh   


# Network
1st step is to setup the base network. I purchased a MikroTik CRS304-4XG-IN in Sep 22, 2025 for $186.90. 

Relevant Mikrotik William Lam posts:
https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html#more-189493

Updated RouterOS using WinBox to 7.23.3 (2026-Jul-30 14:17)  

Perhaps because of the newer firmware, some of the screens looked just a bit different. This is how step 9 looked for me:  

<img width="685" height="446" alt="image" src="https://github.com/user-attachments/assets/202ae5d5-1e83-42e5-8b8a-e50146c03eef" />

<img width="855" height="306" alt="image" src="https://github.com/user-attachments/assets/26bd6932-519e-4ae1-9cbd-543948907ad9" />

In step 10 of the article, lamw didn't include the actual dhcp server dialogs but it's easy to figure it out:  

<img width="721" height="1031" alt="image" src="https://github.com/user-attachments/assets/3241c51c-d2d3-4c9d-861e-1872bfda93ea" />

<img width="672" height="751" alt="image" src="https://github.com/user-attachments/assets/c0353efa-9d9a-4d2a-884f-50e8f6ae1e78" />


# Host details

## BIOS update

Use this blog post for [updating firmware](https://williamlam.com/2025/07/quick-tip-updating-firmware-on-minisforum-ms-a2.html). Latest is currently v1.03. 

Originally came with v1.02  
![ms-a2 v1.02.png](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20v1.02.png)

This is how the v1.03 upgrade looks like following William's instructions
![MS-A2 Bios upgrade](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20bios%20upgrade.png)  

Once it reboots you can confirm v1.03, and all settings will be reset, including the secure boot you had disabled
![ms-a2 v1.03](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20v1.03.png)  

I changed 4 settings:

1. Disable TPM as it's not compatible with ESXi
![ms-a2 disable TPM](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20disable%20tpm.png)  

2. Set MS-A2 to silent mode (don't worry, it can still spin up when needed)  
![Set silent mode](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20bios%20silent%20mode.png)  

3. Set tjmax to 78
![Set tjmax](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20tjmax.png)  

4. Set nvme to be first boot so kickstarter script will work properly
![boot to nvme first](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20boot%20options.png)  


## Hardware details

This [blog post](https://williamlam.com/2025/06/vmware-cloud-foundation-vcf-on-minisforum-ms-a2.html) goes in depth on the virtues of the MS-A2 platform. I have 2 hosts with slightly different configuration. This will become important when I create the Kickstart files. 

From the [main vcf in a box 9.1 post](https://github.com/lamw/vcf-91-in-box) :

_To identify the NVMe device label for the ESX installation (e.g. --disk=<ID>) and NVMe tiering device (e.g. NVME_TIERING_DEVICE=), boot the ESX installer initially, switch to the shell console (ALT+F1), and log in as root with a blank password (just press Enter). Enable SSH using /etc/init.d/SSH start, identify the IP address, SSH to the in-memory ESX host, and run vdq -q to list all storage devices._

For the "identify the IP address" part, assuming you have DHCP and IPv4, I used the following command:

```esxcfg-info | grep "IPv4 Address"```
![MS-A2 installer shell](https://github.com/arielsanchezmora/homelab/blob/main/images/ms-a2%20installer%20shell.png)  

### Host 1:

Using ```esxcfg-info | more```

This confirms BIOS  
      |----BIOS Vendor..............................................American Megatrends International, LLC.   
      |----BIOS Version.............................................1.03 (5.36)   
      |----BIOS ReleaseDate.........................................2026-04-01T00:00:00   
      |----BIOS Asset Tag...........................................Default string   
      |----BIOS Firmware Type.......................................1   
      |----Product Name.............................................MS-A2   
      |----Vendor Name..............................................Micro Computer (HK) Tech Limited   
      |----Family...................................................MINISFORUM   

This confirms the cores and hyperthreading settings  
 \==+CPU Info :   
         |----Num Packages..........................................1   
         |----Num Cores.............................................16   
         |----Num Licensable Cores..................................16   
         |----Num Threads...........................................32   
         |----Hyperthreading Active.................................true   
         |----Hyperthreading Supported..............................true   
         |----Hyperthreading Enabled................................true   
         |----Hyperthreading Mitigated..............................true   

Power policy  
\==+CPU Power Management Info :   
         |----Current Policy........................................Balanced   
         |----Hardware Support......................................ACPI P-states, ACPI C-states   

64GB of RAM  
         |----Physical Mem..........................................66229465088 bytes   

Installed drives:  
"Name":  "t10.NVMe____Samsung_SSD_990_PRO_2TB_________________3B6D415144382500"  
"Name":  "t10.NVMe____Samsung_SSD_990_EVO_Plus_1TB____________88AB425157382500"  
"Name":  "t10.NVMe____SHGP312D500GM____________________________C1D9E326002EE4AC"  

### Host 2:

Using ```esxcfg-info | more```

This confirms BIOS  
      |----BIOS Vendor..............................................American Megatrends International, LLC.  
      |----BIOS Version.............................................1.03 (5.36)  
      |----BIOS ReleaseDate.........................................2026-04-01T00:00:00  
      |----BIOS Asset Tag...........................................Default string  
      |----BIOS Firmware Type.......................................1  
      |----Product Name.............................................MS-A2  
      |----Vendor Name..............................................Micro Computer (HK) Tech Limited  
      |----Family...................................................MINISFORUM  

This confirms the cores and hyperthreading settings  
      \==+CPU Info :
         |----Num Packages..........................................1  
         |----Num Cores.............................................16  
         |----Num Licensable Cores..................................16  
         |----Num Threads...........................................32  
         |----Hyperthreading Active.................................true  
         |----Hyperthreading Supported..............................true  
         |----Hyperthreading Enabled................................true  
         |----Hyperthreading Mitigated..............................true  

Power policy  
      \==+CPU Power Management Info :
         |----Current Policy........................................Balanced  
         |----Hardware Support......................................ACPI P-states, ACPI C-states  

128GB of RAM  
         |----Physical Mem..........................................134948941824 bytes  

Installed drives:  
"Name":  "t10.NVMe____SHGP312D500GM____________________________8C3BE326002EE4AC"  
"Name":  "t10.NVMe____Sabrent_Rocket_4.0_2TB__________________C704206058A77964"  
"Name":  "t10.NVMe____Samsung_SSD_990_EVO_Plus_2TB____________20A9A05157382500"  

Remember the 3 SSDs will be used in this manner:

NVMe 1: ESXi installation, ESX-OSData and VMFS volume  
NVMe 2: NVMe Tiering  
NVMe 3: vSAN ESA  
