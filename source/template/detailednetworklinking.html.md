---
title: DetailedNetworkLinking
category: template
authors: alkaplan, apuimedo, danken, lpeer, moti
wiki_category: Template
wiki_title: Feature/DetailedNetworkLinking
wiki_revision_count: 75
wiki_last_updated: 2012-12-18
wiki_warnings: list-item?
---

# Detailed Network Linking

## Network Wiring

### Summary

The network wiring feature is an enhancement for the VM Network Interface management. It supports the following actions without unplugging the Vnic, maintaining the address of the Vnic:

    * Dynamically changing the network of a running VM (without unplugging the Vnic)
    * Disconnecting a network of a VM without unplugging the vnic
[Network Wiring Feature Page](http://ovirt.org/wiki/Feature/NetworkWiring)

### Owner

*   Name: [ Alona Kaplan](User:alkaplan)
*   Email: <alkaplan@redhat.com>

### Current status

*   On Design
*   Last update date: 11/11/2012

### Detailed Description

#### Engine Flows

#### Engine API

##### Error codes

translate VDSM error codes: TODO

#### VDSM API

A new API is added for this feature.

#### Events

### Documentation / External references

### Streched Goals

*   Enable hot changes in port mirroring (without plugging and unplugging)

### Comments and Discussion

### Open Issues

NA

<Category:Template> <Category:Feature>
