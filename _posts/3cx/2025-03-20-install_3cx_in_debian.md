---
title: "Install 3CX v20 in a clean Debian system"
date: 2025-03-20 00:00:00 +0500
categories: [Guides, 3CX]
tags: [3CX, Debian]
---

# **Guide for installing 3CX v20 in a clean Debain install**

> Its pretty straight forward if you know how linux distro's work. Add package manager source and install package.
> 
> ~~cough~ took me like 2-3 hours to finally try and get shit working becuase of an apt source update error called "File has unexpected size (#### != ####). Mirror sync in progress? [IP: ###.###.###.### 80]" ~cough~~

### **Requirements**
- Root access 
- Little know how of how linux terminal works
- Active Internet Connection

First login as root using: `su`

#### **1. Install dependencies**
```bash
apt install sudo wget gnupg2 dphys-swapfile -y 
``` 

#### **2. Add the PGP key to the Debian system**
```bash
wget -O- https://repo.3cx.com/key.pub | gpg --dearmor | sudo tee /usr/share/keyrings/3cx-archive-keyring.gpg > /dev/null
```

#### **3. Add the 3CX repository to the system's list of APT sources**
```bash
echo "deb [arch=amd64 by-hash=yes signed-by=/usr/share/keyrings/3cx-archive-keyring.gpg] http://repo.3cx.com/3cx bookworm main" | tee /etc/apt/sources.list.d/3cxpbx.list
```

#### **4. Update APT sources database**
```bash
apt update
```
You might get an error here that says something along the lines of `Mirror sync in progress?`. If so proceed to [Apt Update error](#apt-update-error-mirror-sync-in-progress)

#### **5. Install 3CX phone system**
```bash
apt install 3cxpbx
```
If the 3CXWizard does not start after installation, you may need to install dependencies. Enter: `apt install -f` and it should install missing dependencies. Once dependecies are installed it should start the Wizard.

If that does not start the Wizard type in `/usr/sbin/3CXWizard --cleanup`.

## Errors and their solutions

#### **Apt Update error: `Mirror sync in progress?`**
Most of what I saw searching through online for quick fixes on this was, "Just wait a few minutes or hours and try again."

Waited 20 minutes before trying to update apt sources again, did not work. And then waited 12+ hours before trying again. Did not work. So the method I came up with was to download the package and install it manually.

Cooked up a oneliner just for this issue:
```bash
wget -qO- http://repo.3cx.com/3cx/dists/bookworm/main/binary-amd64/Packages | awk '/Filename:/ {print "http://repo.3cx.com/3cx/" $2; exit}' | xargs wget -q && sudo dpkg -i 3cxpbx_*.deb && sudo apt install -f
```

Enjoy.