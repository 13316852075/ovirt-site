---
title: DetailedLockMechanism
category: feature
authors: mkublin, sandrobonazzola, yair zaslavsky
wiki_category: Feature|LockMechanism
wiki_title: Features/DetailedLockMechanism
wiki_revision_count: 29
wiki_last_updated: 2015-01-16
---

# Detailed Lock Mechanism

The actual name of your feature page should look something like: "Your feature name". Use natural language to name the pages.

## Internal Locking Mechanism

### Summary

The following feature should solve collisions which are occurring between sententious flows

Add a link to the main feature page as well: [Your feature name](Your feature name)

### Owner

This should link to your home wiki page so we know who you are

*   Name: [ Michael Kublin](User:mkublin)

Include you email address that you can be reached should people want to contact you about helping with your feature, status is requested, or technical issues need to be resolved

*   Email: mkublin@redhat.com

### Current status

*   Target Release: ...
*   Status: Design / Code review
*   Last updated date: 28.11.11

### Detailed Description

The following feature will introduce an internal in memory generic locking mechanism. A locking mechanism can be used all over bll in order to not allow to occurred for some flows sententiously. The feature will include :
1. Implementation of locking mechanism
2. Introducing it all over a bll logic

#### Entity Description

A new entity will be introduced : EngineLock.
The entity will represent a logical representation of the all objects needed to be locked.
An entity will contains a lists of "read locked" entities and "write locked" entites

#### CRUD

#### User Experience

No GUI required

#### Installation/Upgrade

No impact

#### User work-flows

The implementation will be based on the following algorithm :
1. The lock command will be marked by annotation and lock of object will be done before canDoAction
2. If needed additional treatment appropriate command will override getReadLocks() and getWriteLocks() methods of CommandBase
3. At the end of command the locked will be released (including failure during of canDoAction)
 Explanation on flow:
1. We are running activate/detach/remove/etc domain
2. The entry with domainId will be handled as required lock entity
3. The entry with poolId will be handled as read lock, if it is already exists: we will try to update count = count+1 when not write lock is acquired on that entity
4. Start Activate Domain.
 Now we want to start Reconstruct:
5. The entry with poolId will be handled as write lock. At case that lock on entity can not be acquired - meaning that one of the domains is Locked.
The same issue is regarding HandleFailedStorageDomain because of it can lead to Reconstruct.
 SPM election , for example
6. Also will try to acquire write lock by vdsmId and poolId attached to SPM.
 The base idea is : by uses of annotation and override of the two methods from CommandBase provide different commands with idea which entities they should lock.

#### Events

In case that user did not successes to acquire a lock appropriate canDoaction message should appeared to user

### Dependencies / Related Features and Projects

The following feature will provide a generic mechanism for synchronization different flows in ovirt, by default it will be applied only on known problematic flows , like: storage, vms and permissions flows.

### Documentation / External references

Is there upstream documentation on this feature, or notes you have written yourself? Link to that material here so other interested developers can get involved. Links to RFEs.

### Comments and Discussion

Add a link to the "discussion" tab associated with your page. This provides the ability to have ongoing comments or conversation without bogging down the main feature page

### Open Issues

Issues that we haven't decided how to take care of yet. These are issues that we need to resolve and change this document accordingly.

<Category:Template> <Category:Feature>
