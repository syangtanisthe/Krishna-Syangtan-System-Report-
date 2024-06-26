# Krishna-Syangtan-System-Report-
Student Number:200570156

#!/bin/bash

# Gather system information
userName=$(whoami)
dateTime=$(date '+%Y-%m-%d %H:%M:%S')
hostName=$(hostname)
source /etc/os-release
os="$NAME $VERSION"
uptime=$(uptime -p)

# Gather hardware information
cpu=$(sudo lshw -class processor | grep 'product' | awk -F ': ' '{print $2}')
cpuSpeed=$(sudo lshw -class processor | grep 'capacity' | awk -F ': ' '{print $2}')
ram=$(free -h --si | awk '/^Mem:/ {print $2}')
disks=$(lsblk -d -o MODEL,SIZE | grep -v 'MODEL')
video=$(sudo lshw -class display | grep 'product' | awk -F ': ' '{print $2}')

# Gather network information
fqdn=$(hostname --fqdn)
hostIp=$(hostname -I | awk '{print $1}')
gateway=$(ip r | grep default | awk '{print $3}')
dns=$(grep nameserver /etc/resolv.conf | awk '{print $2}')

# Network interface details
interface=$(ip -o -4 addr show | awk '{print $2 " " $4}' | head -n 1)
interfaceName=$(echo $interface | awk '{print $1}')
interfaceIp=$(echo $interface | awk '{print $2}')

# System status
users=$(who | awk '{print $1}' | sort | uniq | paste -sd "," -)
diskSpace=$(df -h --output=source,avail,pcent | grep '^/' | awk '{print $1 " " $2 " " $3}')
processCount=$(ps aux --no-heading | wc -l)
loadAverages=$(uptime | awk -F'load average: ' '{print $2}')
memoryAllocation=$(free -h)
listeningPorts=$(sudo ss -tuln | awk '{print $5}' | grep -v 'Address' | awk -F':' '{print $NF}' | sort -n | paste -sd "," -)
ufwRules=$(sudo ufw status numbered | sed 's/^/    /')

# Output the report
cat <<EOF

System Report generated by $userName, $dateTime

System Information
------------------
Hostname: $hostName
OS: $os
Uptime: $uptime

Hardware Information
--------------------
CPU: $cpu
Speed: $cpuSpeed
RAM: $ram
Disk(s): 
$disks
Video: $video

Network Information
-------------------
FQDN: $fqdn
Host Address: $hostIp
Gateway IP: $gateway
DNS Server: $dns

Interface Name: $interfaceName
IP Address: $interfaceIp

System Status
-------------
Users Logged In: $users
Disk Space: 
$diskSpace
Process Count: $processCount
Load Averages: $loadAverages
Memory Allocation:
$memoryAllocation
Listening Network Ports: $listeningPorts
UFW Rules: 
$ufwRules

EOF
