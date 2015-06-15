---
title: MultiHostNetworkConfiguration
category: feature
authors: danken, didi, mkolesni, moti, mpavlik
wiki_category: Feature
wiki_title: Features/MultiHostNetworkConfiguration
wiki_revision_count: 35
wiki_last_updated: 2014-02-23
---

# Multi Host Network Configuration

## Edit Provisioned Networks

### Summary

The feature allows the administrator to modify a network (i.e. vlan-id, mtu) which is already provisioned by the hosts and to apply the network changes to all of the hosts within the data-center to which the network is assigned.

### Owner

*   Name: Moti Asayag
*   Email: <masayag@redhat.com>

### Current status

*   On development
*   Planned for ovirt-engine-3.4
*   Last updated: ,

### Benefit to oVirt

Having this feature simplifies significantly the maintenance of hosts within the system:
It reduces the amount of actions required to reflect a logical network definition change by the administrator (a single 'Setup Network' action per host).
In addition, the feature reduces the risk of having hosts network configuration not synchronized with the logical network definition.

### Detailed Description

#### Phase 1

A new property 'apply' will be added to 'Update Network' command which triggers the hosts network configuration sync with the update network definition.
The 'UpdateNetworkCommand' will be changed to a non-transactive. Its execution will be consisted of 2 steps:
# Updating the network logical definition on the DB and handles vnic profile accordingly (remove all if network changed to non-vm network) will run in a new transaction scope.

1.  Applying the network changes by executing a 'setup networks' command for each host which the network is assigned to.
2.  Save the network changes to the host upon a successful 'setup networks'

The Setup Networks command will use the 'sync network' for the modified network.
A dedicated multiple action runner will be added to run the 'Setup Networks' commands in parallel.
Currently, Updating the network is blocked for network which is used by VMs. As part of the feature we should permit the change in these cases:

*   Networks that aren't used by VMs
*   The VMs are down and the change doesn't include modifying a VM network to a non-VM network.

The feature will be enabled only for 3.1 clusters and above since it relies on the 'Setup Networks' which was introduced in 3.1.

#### Phase 2

1.  The same behaviour will be added to 'Remove Network' action as well in a similar manner:
    -   By providing the property 'apply' the network will be removed from all of the hosts which the network is configured on.

2.  Change which includes the renaming of the network will be permitted to be applied to hosts:
    -   This enhancement has a further implication: If the network is used by vms (in status 'down'), and the network failed to be configured on the hosts, any host that wasn't configured properly with the new network name will not be eligible by the scheduler to run vms as it misses the network. Such result requires a further intervention by the administrator. Since the action isn't transactive, there will be no rollback once the network name has changed.

#### User Experience

A checkbox will be added to the 'Edit Network' dialog with caption 'Apply network change to all hosts'.
The only supported properties to be apply on the hosts are: vlan, mtu, network type (vm/non-vm) and STP.
Modifying the network name is permitted, as long as it was not marked to be applied to hosts. Therefore attempt to modify network name and applying it to hosts will be blocked. (This limitation will be removed once p2 is implemented)

#### REST

Editing the network is done on rest via PUT method on:

       api/networks/{network:id}/
       api/datacenters/{datacenter:id}/networks/{network:id}/

By providing the optional element *apply* (default is *false*):

` `<network>
           ...
`     `<apply>`true`</apply>
` `</network>

#### Events

If the can-do-action of the 'Setup Networks' command fails, an event log should be reported including the host names.
Each host individually will have its own logging for the 'Setup network' command.

### Dependencies / Related Features

The feature is purely engine-side related. It doesn't depended on any other package.
A reuse of a mass-operations on host should be consider for network labels.

### Testing

To test the feature, a setup should include at least one host within the data-center having a network 'red' attached to on of its interfaces.

1.  Edit the logical network definition of network 'red' (i.e. change vlan-id)
2.  Mark the 'apply to all hosts' checkbox
3.  Approve the command.
4.  Verify network 'red' was modified correctly on the relevant data-center's hosts.

<Category:Feature> <Category:Template>
