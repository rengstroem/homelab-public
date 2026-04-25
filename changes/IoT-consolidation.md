---
about: Consolidate IoT trusted and IoT-nontrust into one AP
title: "Merge IoT AP"
---
**Change:** Merge IoT AP  
**Status:** Draft   
**Planned:** N/A
 
## What and why
I two VLANs for trusted and non-trusted IoT stuff. Each with own AP. 
Router is starting to slow down. Need to remove an AP to free up power. 
Keep the VLANs but only have one SSID. 
I should be able to keep VLAN structure and then bind IoT devices to their appropriate VLAN. 
 
## Steps
- [ ] Figure out to have multiple VLANs to an SSID. 
- [ ] Remove and rename AP's.
- [ ] Reconnect devices.
- [ ] Test functionality of trusted and non trusted devices. 
## Rollback
<!-- What to undo if it breaks -->
 
## Notes
I could use client isolation.. But that might destroy ability for chromecast etc. to work. Would multicast or this IoT auto thing from unifi fix this? 