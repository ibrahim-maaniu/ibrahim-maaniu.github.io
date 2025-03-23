---
title: "Adding relay configuration to RustDesk client"
date: 2025-03-23 00:00:00 +0500
categories: [Guides, rustdesk]
tags: [rustdesk]
---

# Adding relay configuration to rustdesk client

<!-- Just wanted to make a guide for this since I am trying to get people to use rustdesk. + I host a relay server on a SG VPS which helps with connections -->

### Requirements

- An installed RustDesk client
- Relay server details
- Active internet connection

### Steps

First you ask relay server owner for details.

The owner may give you a 124+ character string that basically lets the RustDesk client decrypt(?) into the details it needs, which is the easiest way.

Or the owner might give you the IP address for the server and a string that is considered as the key for connection authencation.

1. You copy the string into your clipboard
2. Open RustDesk client
3. Go to settings but clicking on the 3 dots button next to ID 
4. Proceed to open Relay server settings. Network > ID/Relay server

#### Long String 

- Click on the clipboard button to use the string

#### IP Address and Key

- Enter IP address and key as follows:
    - ID Server: `<ip>`
    - Relay Server: `<ip>`
    - API Server: `https://<ip>`
    - Key: `<key>`

You are all set now.