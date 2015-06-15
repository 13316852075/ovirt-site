---
title: Cinder Integration
category: feature
authors: derez, sandrobonazzola
wiki_category: Feature|Cinder_Integration
wiki_title: Features/Cinder Integration
wiki_revision_count: 62
wiki_last_updated: 2015-06-11
feature_name: Cinder Integration
feature_modules: engine/vdsm
feature_status: Design
---

# Cinder Integration

### Summary

OpenStack Cinder(/ceph) Integration

### Owner

*   [Daniel Erez](User:DanielErez) (<derez@redhat.com>)
*   [Maor Lipchuk](User:MaorLipchuk) (<mlipchuk@redhat.com>)
*   [Nir Soffer](User:NirSoffer) (<nsoffer@redhat.com>)
*   [Federico Simoncelli](User:FedericoSimoncelli) (<fsimonce@redhat.com>)

### Detailed Description

Managing OpenStack Cinder volumes provisioned by ceph storage through oVirt engine. For initial phase, the integration should ensure support for creating/deleting volumes from a Cinder storage domain, while monitoring relevant statuses using [CoCo](CommandCoordinator) mechanism. The engine/vdsm should allow running VMs with attached ceph volumes via librbd library using libvirt support ([using libvirt with ceph rbd](http://ceph.com/docs/master/rbd/libvirt/)). As for security, when required, running VMs can authenticate using [CEPHX](http://ceph.com/docs/v0.69/rados/operations/auth-intro/) protocol (secret management will be handled in engine/vdsm).

### Dependencies / Related Features

*   [https://github.com/woorea/openstack-java-sdk woorea](https://github.com/woorea/openstack-java-sdk woorea) openstack-java-sdk should be updated and expanded to include cinder-model/cinder-client modules (needed for having an interface to interact with cinder rest-api commands.

### Documentation / External references

*   Ceph documentation: <http://ceph.com/docs/master/>
*   Ceph for Cinder: <http://techs.enovance.com/6572/brace-yourself-devstack-ceph-is-here>
*   Libvirt with CEPH: <http://ceph.com/docs/master/rbd/libvirt/>
*   Cinder REST API: <http://developer.openstack.org/api-ref-blockstorage-v2.html>
*   OpenStack Java SDK: <https://github.com/woorea/openstack-java-sdk>
*   Introducing Ceph to OpenStack: <http://www.sebastien-han.fr/blog/2012/06/10/introducing-ceph-to-openstack/>

### Phases

*   CRUD for OpenStack Volume (Cinder) provider.
*   CRUD for adding/deleting Cinder disks (including monitoring).
*   CRUD for snapshots with Cinder disks.
*   Running VMs with Cinder disks attached.
*   CEPHX integration for using volumes securely.
*   Support templates.
*   Move/Copy (cinder-to-cinder?/cinder-to-vdsm?/vdsm-to-cinder?).
*   Import/Export.

### Patches

[Topic Branch: Cinder](http://gerrit.ovirt.org/#/q/status:open+project:ovirt-engine+branch:master+topic:cinder,n,z)

### Implementation

#### Flow Illustration

![](cinder_flow_illustration.png "cinder_flow_illustration.png")

### Engine

### VDSM

*   Add [librbd1 package](https://apps.fedoraproject.org/packages/librbd1) as dependency to vdsm.spec file.
*   Refactor 'Dirve -> getXML()' to support multiple hosts (represents Ceph monitors) in disk's source element:

<disk type='network' device='disk'>
             

`               `<host name='{monitor-host}' port='6789'/>
                     ...
             

</source>
`       `<target dev='vda' bus='virtio'/>
</disk>

#### UI

##### OpenStack Volume Providers

![](cinder_providers.png "cinder_providers.png")

##### OpenStack Volume Provider Dialog

![](cinder_new_provider.png "cinder_new_provider.png")

##### Cinder Storage Domains

![](cinder_domains.png "cinder_domains.png")

##### Cinder Disks List under Storage

![](cinder_storage_disks.png "cinder_storage_disks.png")

##### Cinder Disk Dialog

![](cinder_new_disk.png "cinder_new_disk.png")

##### Cinder Disks attached to a VM

![](cinder_vms_disks.png "cinder_vms_disks.png")

##### Cinder Disks List

![](cinder_disks_storage.png "cinder_disks_storage.png")

<Category:Features> <Category:Feature> [Category:oVirt 3.6 Proposed Feature](Category:oVirt 3.6 Proposed Feature)
