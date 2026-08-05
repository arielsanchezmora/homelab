# VCF Homelab
Notes for my homelab

This is an all-new VCF 9.1 lab. I am using William Lam's hardware recommendations and following his blog. The main blog post is the [VCF 9.1 lab in a box.](https://github.com/lamw/vcf-91-in-box)

# Hardware BOM

UPS: CyberPower CP1500PFCLCD PFC Sinewave [UPS Battery Backup](https://amzn.to/4xcXysT) please note this is my Amazon affiliate link and this is my first time using this UPS, YMMV   
Internet Router: ASUS  
VCF Lab Router: MikroTik CRS304-4XG-IN - check [William Lam post](https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html) for his Amazon link  
10GB SFP to RJ45 connectors: Two "10Gtek 𝟭.𝟮𝟱/𝟮.𝟱/𝟱/𝟭𝟬𝗚-𝗧 𝗦𝗙𝗣+ 𝘁𝗼 𝗥𝗝𝟰𝟱 CAT.6a Copper Transceiver" recommended by William in his posts, for example this [BOM post](https://williamlam.com/2025/07/vcf-9-0-hardware-bom-for-silicon-valley-vmug.html)  
VCF Hosts: Two slightly different "MINISFORUM AMD Ryzen 9 9955HX Barebone with MS-A2 Mini PC" recommended by William in this [hardware review](https://williamlam.com/2025/06/vmware-cloud-foundation-vcf-on-minisforum-ms-a2.html)     


# Cabling

## Power
The UPS has 6 battery powered outlets and 6 protected outlets.

Battery protected:
1. MS-A2 host 
2. MS-A2 host
3. Mikrotik Router
4. Asus Router

## Data

The Mikrotik router is being cabled in the same manner as in the [William Lam's VCF 9 Lab Network diagram post](https://williamlam.com/2025/09/my-vcf-9-lab-network-diagram.html) and I am using the same 10G RJ45 to SFP connectors William recommended and detailed in the BOM

Port
| Port | Connects to |  
| --- | --- |  
| Port 1 | ASUS uplink router LAN 1 (1GB) |  
| Port 2 | MS-A2 host SFP port 1 (10GB with adapter) |  
| Port 3 | MS-A2 host SFP port 1 (10GB with adapter) |  

# Network
1st step is to setup the base network. I purchased a MikroTik CRS304-4XG-IN in Sep 22, 2025 for $186.90. 

Relevant Mikrotik William Lam posts:
https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html#more-189493

Updated RouterOS using WinBox to 7.23.3 (2026-Jul-30 14:17)
