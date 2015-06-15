---
title: Opening guests
authors: dougsland
wiki_title: Opening guests
wiki_revision_count: 5
wiki_last_updated: 2011-12-04
---

# Opening guests

## Spice

First install spice-xpi extension for Firefox

        # yum install spice-xpi
        <restart your firefox>
        Go to Virtual Machine Tab, right click on VM and select Console

*   Already tested with:
    -   Firefox 3.6.24 - spice-xpi-2.4-4.el6.x86_64 - RHEL6
    -   -   -   fell free to contribute here \*\*\*\*

## VNC

*   Setting manually the vnc password

1) Get VM id and displayPort

        # vdsClient -s 0 list

2) Setting vnc password to VM

        # vdsClient -s 0 setVmTicket <vmid> <password> 0 keep

3) Now try to use vnc client

        # vncviewer <RHEVH>:<displayPort>
