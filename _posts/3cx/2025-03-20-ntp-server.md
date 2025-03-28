---
title: "Running NTP in 3CX systems"
date: 2025-03-20 00:00:00 +0500
categories: [Guides, 3CX]
tags: [3CX, Debian, Windows]
---

# **Setting up NTP** 
<!-- A man onces said, be humble and you will go further.
Mna's was ignorant though. So I don't know what to think about such advices. -->

Some customers prioritize strict network security, restricting internet access to only the 3CX server. This blocks desk phones from accessing public NTP servers, potentially causing time synchronization issues.

If their network devices do not support an internal NTP server, setting up an NTP service on the same system running 3CX can resolve this issue.

## Requirements

- Root access
- Little know-how of how the Linux terminal works
- Active Internet connection

First login as root user.

## **Debian**

### *Install ntp*

First update apt 
```sh
apt update
```

then install ntp
```sh
apt install ntp
```

### *Verify NTP is running*

Check if ntp is active 
```sh
systemctl status ntp.service
```

It should show as `Active: active`

If not start the service
```sh
systemctl start ntp.service
```

### *Configure ntp*

Open `/etc/ntpspec/ntp.conf` with your preferred text edito. i.e, `vi /etc/ntpspec/ntp.conf`

Either delete or comment all of the `pool #.debian.pool.ntp.org iburst`  lines

Get your country's or region's pool domain link from [NTP pool project](https://www.ntppool.org/en)

Add the ntp pool domain name of your choosing <ntp.pool>

```sh
pool <0.ntp.pool> iburst
pool <1.ntp.pool> iburst
pool <2.ntp.pool> iburst
```

### *Configure Firewall*

First check the name of the LAN interface name, your local network is connected. Replace <ifname> with that name. i.e., eno2

Now write/copy the following command:
```sh
nft add rule inet filter input <ifname> udp dport 123 accept
```

Backup/Copy the default 3CX nftables configuration file just in case.
```sh
cp /etc/nftables.conf /etc/nftables.conf.bac
```
Make it persistent
```sh
nft list ruleset > /etc/nftables.conf
```

## **Windows**

### *Enable the NTP server*

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpServer" -Name "Enabled" -Value 1
```

### *Make the AnnounceFlags 5*

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\services\W32Time\Config" -Name "AnnounceFlags" -Value 5
```

### *Restart the windows time service to start with the new changes*

```powershell
Restart-Service w32Time
```

### *If firewall is running allow NTP Port:*

```powershell
New-NetFirewallRule `
-Name "NTP Server Port" `
-DisplayName "ntp-server" `
-Description 'Allow NTP Server Port' `
-Profile Any `
-Direction Inbound `
-Action Allow `
-Protocol UDP `
-Program Any `
-LocalAddress <3CX system local ip> `
-LocalPort 123
```