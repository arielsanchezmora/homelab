# homelab
Notes for my homelab

This is an all-new VCF 9.1 lab. I am using William Lam's hardware recommendations and following his blog.

# Hardware BOM and expense
CyberPower CP1500PFCLCD PFC Sinewave UPS Battery Backup (Amazon link goes here)  
MikroTik CRS304-4XG-IN - check <a href="https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html" target="_blank">William Lam post</a> for his Amazon link  
Two "10Gtek 𝟭.𝟮𝟱/𝟮.𝟱/𝟱/𝟭𝟬𝗚-𝗧 𝗦𝗙𝗣+ 𝘁𝗼 𝗥𝗝𝟰𝟱 CAT.6a Copper Transceiver"  
Two "MINISFORUM AMD Ryzen 9 9955HX Barebone with MS-A2 Mini PC"  

# Cabling

## Power
The UPS has 6 battery powered outlets and 6 protected outlets.

Battery protected:
1. MS-A2 host 
2. MS-A2 host
3. Mikrotik Router
4. Asus Router

## Data

Mikrotik

# Network
1st step is to setup the base network. I purchased a MikroTik CRS304-4XG-IN in Sep 22, 2025 for $186.90. 

Relevant Mikrotik William Lam posts:
https://williamlam.com/2025/07/initial-mikrotik-router-switch-configuration-for-vcf-9-0.html#more-189493

Updated RouterOS using WinBox to 7.23.3 (2026-Jul-30 14:17)
