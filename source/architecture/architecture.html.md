---
title: Architecture
category: architecture
authors: abonas, alonbl, amuller, dneary, geoffoc, lhornyak, ovedo, sven, vszocs
wiki_category: Architecture
wiki_title: Architecture
wiki_revision_count: 45
wiki_last_updated: 2014-12-07
---

# oVirt Architecture

A standard oVirt network consists of three things, primarily:

*   One or more nodes, on which we will run virtual machines (VMs)
*   One or more storage nodes, which hold the images and ISOs corresponding to those VMs
*   ovirt-engine running on a server, which we will use to deploy, monitor, move, stop and create VM images
*   An identity service, to authenticate users and administrators for ovirt-engine

The nodes are Linux distributions with VDSM and libvirt installed, along with some extra packages to easily enable virtualisation of networking and other system services. The supportedd Linux distributions to date are Fedora 17 or oVirt node, which is basically a stripped-down distribution containing just enough stuff to allow virtualisation.

The storage nodes can use block or file storage, and can be local or remote, accessed via NFS. Storage technologies like Gluster are supported through the POSIXFS storage type. Storage nodes are grouped into storage pools, which can ensure high availability and redundancy. The [Vdsm Storage Terminology](Vdsm Storage Terminology) page has more details.

oVirt engine is a JBoss-based Java application (previously C#) which runs as a web service. This service talks directly to VDSM on the engine nodes to deploy, start, stop, migrate and monitor VMs, and it can also create new images on storage from templates.

The different diagrams and descriptions below represent the architecture of the oVirt project, and its different components.

### Overall architecture

The following diagram shows the different components in the oVirt project:

![](Overall-arch.png "Overall-arch.png")

These main components are:

1.  Engine - monitors the oVirt network, and allows system administrators to create and deploy new VMs
2.  Host agent (VDSM) - the oVirt engine communicates with VSDM to request VM related actions on the nodes
3.  Admin Portal - the web application sysadmins use to talk to the engine to perform advanced actions
4.  User Portal - a simplified application for simpler use-cases
5.  REST API - an API which allows applications to perform virtualisation actions, which is used by the command line tools and the SDK
6.  CLI/SDK - The command line interface and SDK provide a means to script actions
7.  Reports Engine - generates reports on VM resource usage, using Jasper Reports
8.  DWH (Data Warehouse) - The data warehouse component extracts and synthesizes data from the engine using Talend for later use in reports
9.  Guest Agent - The guest agent runs inside the VM, and provides information on resource usage to the oVirt engine. Communication is done over a virtualised serial connection.

The sections below will give a description and architectural aspects for each such component.

### Engine

The oVirt engine is a large scale, centralized management for server and desktop virtualization, based on leading performance, scalability and security infrastructure technologies. Some features provided by the engine:

1.  VM lifecycle management
2.  Authentication via LDAP providers (AD/IPA)
3.  Network management - adding logical networks, and attaching them to hosts
4.  Storage management - managing storage domains (NFS/iSCSI/Local), and virtual VM disks
5.  High Availability - restart guest VMs from failed hosts automatically on other hosts
6.  Live Migration - move running VM between hosts with zero downtime
7.  System Scheduler - continuously load balance VMs based on resource usage/policies
8.  Power Saver - concentrate virtual machines on fewer servers during off-peak hours
9.  Maintenance Manager - no downtime for virtual machines during planned maintenance windows.
10. Image Management - template based provisioning, thin provisioning and snapshots
11. Monitoring - for all objects in system – VM guests, hosts, networking, storage etc.
12. Export/Import - import and export VMs and templates using OVF files
13. V2V - convert VMs from VMware and RHEL/Xen environments to the oVirt environment

The following diagram shows the different layers in the oVirt engine component:

![](Engine-arch.png "Engine-arch.png")

#### Engine-Core Architecture

The following diagram shows the different components in the engine-core:

![](Engine-arch2.png "Engine-arch2.png")

The main components in the engine core are:

*   DB Broker - responsible for all the DB related actions
*   VDS Broker - responsible for all actions that require communicating with VDSM
*   LDAP Broker - responsible for authenticating and fetching user/group attributes from the LDAP directory (currently support AD, IPA and RHDS)
*   Backend Bean - a Singleton bean responsible for running actions, queries and monitoring of the different entities

### Host Agent (VDSM)

VDSM covers all functionality required by oVirt Engine.

1.  Configures host, networking and shared storage
2.  Uses libvirt for VM life cycle operations
3.  Written in Python
4.  The VDSM API is XML-RPC based (planned to move to REST API).
5.  Multithreaded, multi-processes
6.  Speaks with its guest agent via virtio-serial
7.  Adds customized clustering support for LVM that scales to hundreds of nodes
8.  Implements a distributed image repository over the supported storage types (local directory, FCP, FCoE, iSCSI, NFS, SAS)
9.  Multihost system, one concurrent metadata writer
10. Scales linearly in data writers

![](Vdsm-arch.png "Vdsm-arch.png")

#### Hooks mechanism

The “Hook” mechanism:

1.  Allows administrators to define scripts to modify VM operation eg. Add extra options such as CPU pinning, watchdog device, direct LUN access, etc.
2.  Allows oVirt to be extended for new KVM features before full integration is done
3.  An easy way to test a new kvm/libvirt/linux feature

The following diagram illustrates the Hook mechanism in the VM lifetime cycle:

![](Hook-arch.png "Hook-arch.png")

1.  The hook mechanism comes into place before VDSM initiates the VM startup using libvirt.
2.  The hook changes the VM definition, and VDSM passes this definition to libvirt to start the VM.

### Admin Portal

...

### User Portal

...

### REST API

RESTful API for integration with oVirt Engine:

1.  REST interface exposed for all API functions
2.  Stands for Representational State Transfer
3.  Modeling entity actions around HTTP verbs
    -   GET
    -   PUT
    -   POST
    -   DELETE

4.  Still uses 'actions' for some state changes
5.  Self describes – entity navigation and actions

REST Concepts:

1.  Client–server
2.  Stateless
3.  Cacheable
4.  Uniform interface

### CLI/SDK

SDK:

1.  Python based SDK to allow performing actions on the different entities
2.  Complete protocol abstraction
3.  Full compliance with the oVirt API architecture
4.  Auto-completion
5.  Self descriptive
6.  Intuitive and easy to use
7.  Auto-Generated

CLI:

1.  Python based CLI to allow querying and performing actions on the different entities
2.  Complete protocol abstraction
3.  Full compliance with the oVirt API architecture
4.  Highly descriptive help for each operation
5.  Intuitive and easy to use
6.  Auto-Generated

### Reports Engine

1.  Based on Jasper Reports
2.  Jasper allows to import/export reports definitions
3.  Rich reporting engine
    -   Report scheduling
    -   Filters
    -   Export to various formats
    -   Report creation studio

### DWH

The DWH component contains:

1.  ETL based on talendforge.org
2.  Periodic polling from operational DB
3.  Types of data
    -   Config with version tracking
    -   Statistics – aggregated hourly/daily

4.  API is view based

### Guest Agent

The guest agent provides additional information to oVirt Engine, such as guest memory usage, guest ip address, installed applications and sso. Architectural details:

1.  Python code, available for both linux and windows guests
2.  Communication is done over virtio-serial
3.  SSO for windows is based on a gina module for XP and a credential provider for windows 7
4.  SSO for RHEL 6 is based on a PAM module with support for both KDE and Gnome

![](Guest-agent-arch.png "Guest-agent-arch.png")

<Category:Architecture> <Category:Node>
