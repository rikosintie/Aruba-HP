[![made-with-python](https://img.shields.io/badge/Made%20with-Python-1f425f.svg)](https://www.python.org/)
![GitHub language count](https://img.shields.io/github/languages/count/rikosintie/nmap-python)
![Twitter Follow](https://img.shields.io/twitter/follow/rikosintie?style=social)

# Aruba-HP
Convert "sh arp" to a sorted list of IP, MAC addresses and manufactures.

## You do not need to know anything about python to use these tools! You only need to install python 3.6x or higher.

This is a re-write of the ARP-Sort and MAC2Manuf tools I wrote for Cisco switches. The Aruba-HP switch output is different enough that it seemed better to have separate tools than adding switches to based on the manufacture.

An Aruba-HP switch running a routing protocol maintains an "ARP-table" that maps a device's mac address to it's IP address. Here is a sample output:
sh arp
```
 IP ARP table

  IP Address       MAC Address       Type    Port
  ---------------  ----------------- ------- ----
  10.10.5.1        d4f4be-881d11     dynamic F9
  10.10.8.3        005056-7cdb44     dynamic L5
```
This table is useful for trouble shooting and I also use the mac address table when I'm replacing a core switch. I run a 'sh arp' before the cut and then one on the new switch and compare them to make sure all critical servers/devices are working. 

This script makes it easy (and fast) to compare the before and after since it only contains the IP/MAC and is sorted by IP address. 

I use Meld on Linux\Windows to compare files. On Windows, Notepad++ is also a good tool. Here is a link to a review of Linux Diff tools - 9 Best File Comparison and Difference (Diff) Tools for Linux. Tecmint is a great site for Linux information.

You may need to ping the broadcast mask on the core before running the "sh ip arp" to make sure all devices are in the table. Most devices ignore a broadcast ping for security reasons but I've found that the fire alarms and Environmental Montioring Systems (EMS) that I am interested in do respond to ping x.x.x.255 (for a /24).

## Usage 



Download the files in this repository and unzip them. 



If you have Git installed you can just use:

```

git clone https://github.com/rikosintie/Aruba-HP.git

```

to clone the scripts onto your hard drive.

If you don't have Git installed just click the "Clone or Download" button and select "Download Zip".


On the core switch run: 

```

term len 0 #turn off paging

show arp or show arp vlan xx

term len 30 #set page length to 30

```



Save the output in a file named `arp.txt`


## Run the script ##

To execute on windows if the python launcher is installed

```
python -3 arp-hp.py 
```

**On Linux**



```
python3 arp-hp.py
```


## Results

The script will strip off everyting except the IP address and MAC address.
```
Number of IP, MAC and Manufacture: 971 

10.10.1.4 000463-0a9be7 BoschSec
10.10.1.5 accc8e-48032a AxisComm
10.10.5.1 d4f4be-481d11 PaloAlto
10.10.8.3 005056-8cdb44 Vmware
10.10.8.4 005056-8ce3cf Vmware
10.10.8.8 02bf0a-0a0808 None
```


### MAC2Manuf
The second tool takes the output from "sh mac-address int#" and creates a table for each interface with the IP address, MAC Address and manufacture name. I uses a json file created by arp-hp.py to match the IP to the MAC address on the interface.

## Usage
On the edge switch run:
```
sh mac-address 1
sh mac-address 2
...
```

for all the interfaces you are interested in. 

Save the output in a file named "mac-addr.txt".

## Run the script ##



To execute on windows if the python launcher is installed



```
python -3 macaddr-hp.py 
```



**On Linux**





```
python3 macaddr-hp.py
```

## Results

```
Device Name: test-2920-48 
PingInfo Data
10.10.26.52 3417eb-ba532f
10.10.202.7 b4a95a-ab7608
10.10.202.46 38bb3c-bc311f
10.10.26.84 a4251b-30e9a8

Number of Entries: 4 

Device Name: test-2920-48 
Vlan   IP Address       MAC Address       Interface   Vendor
----------------------------------------------------------------------
  24   10.10.26.52      3417eb-ba532f         11      Dell
----------------------------------------------------------------------
 202   10.10.202.7      b4a95a-ab7608         11      Avaya
----------------------------------------------------------------------
 202   10.10.202.46     38bb3c-bc311f         13      Avaya
----------------------------------------------------------------------
  24   10.10.26.84      a4251b-30e9a8         14      Avaya
----------------------------------------------------------------------
```
