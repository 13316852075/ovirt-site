---
title: Api
category: api
authors: michael pasternak, quaid
wiki_title: Category:Api
wiki_revision_count: 36
wiki_last_updated: 2013-09-23
---

# Api

oVirt api package provides Application programming interface for the oVirt engine.

[Notes from workshop in November 2011](API - oVirt workshop November 2011 Notes).

__TOC__

## Maintainer

Michael Pasternak: mpastern@redhat.com

## REST Concept

*   Client–server
*   Stateless
*   Cacheable
*   Uniform interface

## REST Principals

*   Identification of resources
*   Manipulation of resources through representations
*   Self-descriptive
*   Hypermedia as the engine of application state

## oVirt-API URI structure

http(s)://server:port/api/vms/xxx-xxx/disks/yyy-yyy

1. protocol

2. server details

3. entry point (base resource)

4. collection

5. resource

6. sub-collection

7. sub-resource

## oVirt-API How-to (the methods)

*   To list all collection resources, use GET.

      GET http(s)://server:port/api/vms

*   To retrieve specific resource, use GET.

      GET http(s)://server:port/api/vms/xxx

      curl -v -u "user@domain:password" -H "Content-type: application/xml" -X GET http(s)://server:port/api/vms/xxx

*   To create a resource, use POST.

      POST http(s)://server:port/api/vms
<vm>`...`</vm>

      curl -v -u "user@domain:password" 
      -H "Content-type: application/xml" 
      -d 
` '`<vm>
`  `<name>`my_new_vm`</name>
`  `<cluster><name>`cluster_name`</name></cluster>
`  `<template><name>`template_name`</name></template>
       `</vm>`' 'http(s)://server:port/api/vms'

*   To update the resource, use PUT.

      PUT http(s)://server:port/api/vms/xxx 
<vm><name>`aaa`</name></vm>

      echo "`<vm><name>`new_name`</name></vm>`" >  /tmp/upload.xml
      curl -v -u "user@domain:password" 
       -H "Content-type: application/xml" 
       -T /tmp/upload.xml 
       'http(s)://server:port/api/vms/xxx'

*   To remove the resource, use DELETE.

      DELETE http(s)://server:port/api/vms/xxx

      curl -v -u "user@domain:password" -X DELETE http(s)://server:port/api/vms/xxx

## Repository

*   <git://gerrit.ovirt.org/ovirt-engine> (restapi is one of the engine modules)

## TODO list

### RSDL

#### advertising /request/ parameters (assigned)

### features

#### setup networks

       * atomic operation on a collection level

#### non-admin users support

<Category:Api>
