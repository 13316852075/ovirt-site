---
title: GlusterGeoReplication
category: feature
authors: kmayilsa, sahina, sandrobonazzola, shtripat
wiki_category: Feature|Gluster Geo Replication
wiki_title: Features/Design/GlusterGeoReplication
wiki_revision_count: 15
wiki_last_updated: 2015-01-21
---

# Gluster Geo Replication

## Summary

This document describes the design for geo replication feature under gluster. For overview of this feature, refer [Features/Gluster_Geo_Replication](Features/Gluster_Geo_Replication)

## Owner

*   Feature owner: Sahina Bose <sabose@redhat.com>
    -   GUI Component owner: Anmol Babu <anbabu@redhat.com>
    -   Engine Component owner: Sahina Bose <sabose@redhat.com
    -   VDSM Component owner: Darshan Narayana Murthy <dnarayan@redhat.com>
    -   QA Owner: Kasturi Narra <knarra@redhat.com>

## Current Status

*   Status: In Progress
*   Last updated date: Thu Jul 25 2013

## Design

Geo-replication feature is designed to enable creation and maintenance of geo-replication sessions across clusters in GlusterFS. A geo-replication session can be setup between a GlusterFS managed source cluster and remote (destination) GlusterFS managed cluster.

### Entity Description

#### Gluster Geo Replication Destinations

This entity stores the details of remote (destination) for a geo-replication setup. While creation of a geo-replication session if it is first time that the geo-replication session is being setup being the source and destination cluster then an entry in maintained as part of this entity. For further geo-replication sessions the existing entry if referred for details.

| Column name                   | Type   | description                                                              |
|-------------------------------|--------|--------------------------------------------------------------------------|
| Id                            | UUID   | Primary Key                                                              |
| Vds_Group_Id                | UUID   | Id of the Source Cluster                                                 |
| Server_Id                    | UUID   | Host of the Source Cluster                                               |
| Destination_Host_IP         | String | Host part of remote/destination cluster                                  |
| Destination_SSH_Fingerprint | String | SSH key fingerprint of destination host                                  |
| Connection_Status            | String | Password less connection status between source node and destination node |

#### Gluster Geo Replication Sessions

This entity stores the details of the individual geo-replication sessions

| Column name         | Type   | description                                      |
|---------------------|--------|--------------------------------------------------|
| Id                  | UUID   | Primary Key                                      |
| Destination_id     | UUID   | References Id of gluster_geo_rep_destinations |
| Volume_id          | UUID   | References Id of gluster_volumes                |
| Destination_Volume | String | Name of the volume in destination cluster        |

#### Gluster Geo Replication Session Status

This entity stores the status of individual geo-replication sessions maintained in oVirt engine

| Column name | Type   | description                                             |
|-------------|--------|---------------------------------------------------------|
| Id          | UUID   | Primary Key                                             |
| Session_id | UUID   | References Id of gluster_geo_rep_session             |
| Server_id  | UUID   | Host in the source cluster                              |
| Status      | String | Valid values STABLE, FAULTY, INITIALIZING, NOT_STARTED |

#### Gluster Geo Replication Session Config

Refer the URL <http://www.ovirt.org/Features/Entity_Configuration_Management> for more details on configuration maintenance.

### REST APIs

The details of the REST for gluster geo-replication feature are as below -

#### Listing APIs

*   api/clusters/{id}/geo-replication-destinations - lists all the geo-replication destinations from current cluster

Output:

    <geo-replication-destinations>
      <geo-replication-destination>
        <id>geo replication destination id</id>
        <cluster>Cluster Id</cluster>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
      </geo-replication-destination>
      <geo-replication-destination>
        <id>geo replication destination id</id>
        <cluster>Cluster Id</cluster>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
      </geo-replication-destination>
    </geo-replication-destinations>

*   api/clusters/{id}/geo-replication-destinations/{geo-rep-destination-id} - lists the details of the individual geo-replication destination

Output:

      <geo-replication-destination>
        <id>geo replication destination id</id>
        <cluster>Cluster Id</cluster>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
      </geo-replication-destination>

*   api/volumes/{id}/geo-replication-sessions - lists all the geo-replication sessions for the current volume

Output:

    <geo-replication-sessions>
      <geo-replication-session>
        <id>geo replication session id</id>
        <volume>source volume id</volume>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
        <destination_volume>destination volume id</destination_volume>
        <status>Stable/Faulty/Initializing/Not Started</status>
      </geo-replication-session>
      <geo-replication-session>
        <id>geo replication session id</id>
        <volume>source volume id</volume>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
        <destination_volume>destination volume id</destination_volume>
        <status>Stable/Faulty/Initializing/Not Started</status>
      </geo-replication-session>
    </geo-replication-sessions>

*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id} - lists the detail of an individual geo-replication session

Output:

      <geo-replication-session>
        <id>geo replication session id</id>
        <volume>source volume id</volume>
        <source_host>Source Host Id</source-host>
        <destination_host>Destination Host Id</destination_host>
        <destination_volume>destination volume id</destination_volume>
        <status>Stable/Faulty/Initializing/Not Started</status>
      </geo-replication-session>

*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/configurations - lists all the configurations for a geo-replication session

Output:

    <geo_replication_configurations>
      <configuration>
        <id>Configuration Id</id>
        <configuration_name>Name of the configuration</configuration_name>
        <configuration_value>Value of the configuration</configuration_value>
      </configuration>
    </geo_replication_configurations>

*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/configurations/{config-id} - shows the details of an individual configuration for a geo-replication session

Output:

      <configuration>
        <id>Configuration Id</id>
        <configuration_name>Name of the configuration</configuration_name>
        <configuration_value>Value of the configuration</configuration_value>
      </configuration>

#### Actions Supported

*   api/clusters/{id}/create-geo-rep-destination - creates a new geo-replication destination for the cluster
    -   Parameters
        -   source_host - uuid
        -   destination_host - uuid
        -   destination_root_passwd - string

Input:

    <action>
      <source_host>Source Host Id</source_host>
      <destination_host>Destination Host Id</destination_host>
      <destination_root_passwd>Destination Host root password</destination_root_passwd>
    </action>

*   api/clusters/{id}/geo-replication-destinations/{geo-rep-destination-id}/remove - Removes the given geo-replication destination
*   api/clusters/{id}/geo-replication-destinations/{geo-rep-destination-id}/reestablish - reestablishes the communication between geo-replication source-destination
*   api/clusters/{id}/geo-replication-destinations/{geo-rep-destination-id}/test - checks the validity of communication between geo-replication source-destination

<!-- -->

*   api/volumes/{id}/create-geo-rep-session - Creates a new geo-replication session for the volume
    -   Parameters
        -   destination_host
        -   destination_volume

Input:

    <action>
      <destination_host>Destination Host Id</destination_host>
      <destination_volume>Destination Volume Id</destination_volume>
    </action>

*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/start - starts the given geo-replication session
*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/stop - stops the given geo-replication session
*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/remove - removes the given geo-replication session
*   api/volumes/{id}/geo-replication-sessions/{geo-rep-session-id}/set-config - sets a configuration value for a geo-replication session
    -   Parameters
        -   configuration_name - string
        -   configuration_value - string

Input:

    <action>
      <configuration_name>Name of the configuration</configuration_name>
      <configuration_value>Value of the configuration</configuration_value>
    </action>

[Category: Feature](Category: Feature)
