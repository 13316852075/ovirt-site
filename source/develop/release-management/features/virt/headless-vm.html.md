---
title: Headless VM 
category: feature
authors: SharonG
wiki_category: Feature
wiki_title: Features/Headless VM
feature_name: Headless VM
feature_modules: engine
feature_status: Merged
---

# Headless VM

### Summary

Support Headless VMs in Ovirt by adding an option to run a VM without any graphical console, for cases in which a graphical console is not needed for accessing this VM. This Headless VM will run without graphical and Video devices.

### Owner

*   Name: Sharon Gratch (sgratch)
*   Email: <sgratch@redhat.com>

### Benefit to Ovirt

Before this feature was implemented, OVirt Engine always added at least one graphical console to a running VM. Although this is right for most use cases, some VMs are required to run without graphical consoles from the start or after the initial setup. The reason for that can be due to host limitations or according to VM usage requirements (e.g. Real-Time VM).

This feature add a flexible, very simple to manage and reversible option to start a Headless VM at any time via Engine and still keep an option to access the VM for setup or administrative purposes if needed.

### Usage

*   Setting a VM as Headless can be done to new or existed VM at any time and it is a reversible option, meaning that a Headless VM can be switched to non-Headless and vice versa.

*   Setting the VM as Headless can be done via the UI by checking/un-checking a new "Headless Mode" checkbox field added to the Console tab of the VM dialog (in advanced mode). In case of a running VM, this configuration change will require a VM restart. 

*   A Headless VM Template or Pool or Instance Type can also be created on the same way as a VM, so in case the user wants to create or edit a VM to become Headless, he can also choose a Template, Pool or Instance type which are configured as Headless and "inherit" this property for that specific VM.

*   Invoking a graphic console via the Engine is disabled for a Headless VM and so are all options in the "Console Options" dialog.

*   In case of a new created VM, the user has two options to access this VM via Ovirt for managing:

  1. Enable the Ovirt Serial Console for this VM.

  2. Manage the VM via a required graphic console only on its first run by using the "Run Once" dialog, which enables 
     to run a Headless VM as non-Headless only for the first run. 
     A checkbox is added to the "Run Once" dialog and let the user choose how he wants to run this VM for the first 
     time only and with which Graphic Protocol (SPICE or VNC).  

*   In case of editing an already running VM to become Headless, the user should verify the following before restarting this VM and applying the Headless mode:

  1. How to access the VM in case needed. Options can be to enable the Ovirt Serial Console, or install SSH or any 
     other service for command line access.

  2. What is the IP of this VM in case needed since if the Guest-Agent is not installed then the Ovirt Engine won't 
     know The VM IP.

Note that all of the scenarios described above can be done via Ovirt WebAdmin or UserPortal and in the future also via the REST API and Ovirt API.

### Detailed Description

#### WebAdmin/UserPortal UI

*   The Console side-tab of the VM dialog (in advanced mode) was re-arranged and divided to "Graphical Console" and "Serial Console" properties and a "Headless Mode" checkbox was added under the "Graphical Console" section. In case the "Headless Mode" is checked, all other Graphical Console properties are disabled/grayed out. The SPICE USB controller is disabled only if the "Serial Console" is disabled for the VM. The "Serial Console" is handled as before and is not depended on the VM Headless mode.

*   In case of a Headless VM, the "Graphics Column" and the "General" sub-tab displays the "Graphics protocol" and "Video Type" values as "None". 

*   Invoking a graphic console and using the "Console Options" dialog are disabled.

*   Under the "VM devices" sub-tab no video or graphic devices are displayed in case of a Headless VM.

*   All those changes are also relevant for VM Template, VM Pool and Instance Type dialogs and the Headless Mode configuration is supported for all actions relevant for those entities including create a VM from a Template and create a Template from a VM.

*   An option of "Headless mode" was added to the Console section of the "Run Once" dialog and the user can choose how to run the first running of a VM (headless or not): as non-Headless with VNC graphic protocol, as Non-Headless with SPICE graphic protocol or as Headless.

#### Backend

No new property was added to the VM object and marking a VM as Headless is done by using existing properties. 
In case of Headless VM / Template / Pool / Instance Type:

1. The VM (VMStatic object) set the defaultDisplayType property to "none" value.

2. The VM (VMDynamic object) set the graphicsInfos property (spice_port, spice_ip, vnc_port, vnc_ip, spice_tls_port etc) to null values.

3. All Video and graphic devices are removed for that VM.

#### DB

No changes in the DB

### Status

*   Target Release: Ovirt 4.1
*   Status: All is merged except REST API support for this feature (Backward compatibility is fully supported).
*   REST API support Target Release: Ovirt 4.2

### Limitations

*   The ability to access the Headless VM and obtaining the VM IP are not automatically supported -
In case a VM is currently running in Ovirt in a Headless mode, without a serial console enabled and without Guest-Agent installed, the user won't be able to obtain an access or login to that VM and also won't be able to check for the VM IP via the Ovirt Engine. 
There is no warning for such cases in which no guest-agent is running or no serial console enabled and no ssh key installed and it's the user responsibility to manage it in advance (either by enabling the serial console or by manage the VM on it's first run via the "run Once" dialog which enables to run a Headless VM as non-Headless only for the first run).

*   Switching from Headless mode to non-Headless mode for a running VM requires a VM restart (since devices should be loaded or omitted) and this may be a limitation for stateless VMs.

### Dependencies / Related Features

