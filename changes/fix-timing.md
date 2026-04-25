---
name: Change
about: Track an infrastructure change
title: "[CHANGE] "

---
**Title:** Fix timing issues
**Status:** Ready 
**Planned:** <!-- YYYY-MM-DD -->
 
## What and why
Im noticing WARNS when working with lxc's regarding locale.
This becomes a problem when i start to aggregate logs in SIEM.  
This has nothing to do with PVE. Time There is fine. 
<!-- One or two sentences -->
 
## Steps
- [ ] Batch fix running CT's with 
```
for ctid in $(pct list | awk 'NR>1 && $2=="running" {print $1}'); do pct exec $ctid -- bash -c "ln -sf /usr/share/zoneinfo/Europe/Copenhagen /etc/localtime && echo 'Europe/Copenhagen' > /etc/timezone"
done
```
- [ ] Add correct localization to boilerplate. 
## Rollback
<!-- What to undo if it breaks -->
 
## Notes
<!-- Parking lot -->