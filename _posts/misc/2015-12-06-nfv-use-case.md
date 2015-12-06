---
layout:         post
title:          NFV Use Cases, 2015-12-06
category:       misc
description:    NFV, Use Case
---

Network Functions Virtualisation aims to transform the way that network operators architect nwtworks by evolving
standard IT virtualisation technology to consolidate many network equipment types onto industry standard high volume
servers, switches and storage, which could be located in a variety of NFVI-PoPs including datacenters, network nodes
and in end user premises.

This document describes a set of service models and high level use cases which represent, in the view of NFV ISG member
companies, important service models and initial fields of application for NFV, and which span the scope of technical
challenges being addressed by the NFV ISG.

* CSP: Cloud Service Provider
* CSC: Cloud Service Customer
* PE: Provider Edge
* CPE: Customer Premises Equipment

##1. Network Functions Virtualisation Infrastructure as a Service (NFVIaaS)

Cloud Computing Services are typically offered in three service models:

* Infrastructure as a Service (IaaS): CSPs provide infrastructures, including networking, storage and computing
resources. Customers can install their own OS, and deploy any kinds of softwares on the Virtual Machine.
* Platform as a Service (PaaS): CSPs provide infrastructures with development environment. Customers cannot control
underlying infrastructures. They can only use the functionality provided by the platform.
* Software as a Service (SaaS): CSCs just directly use the functionality provided by the CSPs. For example, use web
browsers to get the results computed by CSPs.
* Network as a Service (NaaS): No standard definition. The network connectivity between CSPs and CSCs, or the network
connectivity within data centers or between the computing nodes of a CSPs infrastructure.

Service is a word with multiple meanings that generally related to the act of doing something useful for another
entity, often for a fee or as part of some commercial transaction, or as a functionality enabled by a service provider
for the consumer of that service. It can also refer to a function that is performed by software for another entity.

NFV Infrastructure can be considered as providing the capability or functionality of providing an environment in which
Virtualised Network Functions can execute.

This use case provides an approach to map the Cloud Computing Service Models IaaS and NaaS as elements within the
Network Function Virtualisation Infrastructure when it is provided as a service.

![Administrative Domain #2 running VNFs on Administrative Domain #1]({{ site.url }}/image/2015-12-06/NFVIaaS.png)


##2. Virtual Network Function as a Service (VNFaaS)

Pre-NFV service provider networks include a PE router at the edge of the core, facing the CPE device.

![Pre-NFV Service Provider]({{ site.url }}/image/2015-12-06/pre-nfv.png)

Virtualisation of the enterprise may include:

* Virtualisation of the CPE functions (vE-CPE) in the service provide cloud.
* Virtualisation of the PE functions (vPE) where the virtual network services functions and core-facing PE functions
can be executed in the service provider cloud.

###Virtualisation of the CPE (vE-CPE)

###Virtualisation of the PE (vPE)


##3. Virtual Network Platform as a Service (VNPaaS)


##4. VNF Forwarding Graphs


##5. Virtualisation of Mobile Core Network and IMS


##6. Virtualisation of Mobile base station


##7. Virtualisation of the Home Environment


##8. Virtualisation of CDNs


##9. Fixed Access Network Functions Virtualisation


