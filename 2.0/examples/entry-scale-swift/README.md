
(c) Copyright 2015 Hewlett Packard Enterprise Development Company LP

Licensed under the Apache License, Version 2.0 (the "License"); you may
not use this file except in compliance with the License. You may obtain
a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations
under the License.


##Helion Entry Scale Swift Example##

The input files in this example deploy a cloud that has the following characteristics:


### Control Planes ###

- A single control plane consisting of three servers that co-host all the required services
  needed to support a swift only configuration. For example keystone, monasca, horizon,
  opsconsole. These 3 servers also include the swift proxy, account, container and
  swift ring building services.

###Resource Pools###

- Three swift object servers


*Additional resource nodes can be added to the configuration.*


###Deployer Node###


This configuration runs the lifecycle-manager (formerly referred to as the deployer) on a
control plane node. You need to include this node address in your servers.yml definition.
This function does not need a dedicated network.

*The minimum server count for this example is therefore 7 servers (Lifecycle Manager (1)
+ Control Plane (x3) + Swift Object (x3)*

An example set of servers are defined in ***data/servers.yml***. You will need to modify
this file to reflect your specific environment.


###Networking###

The example requires the following networks:

IPMI/iLO network, connected to the deployer and the IPMI/iLO ports of all servers

A pair of bonded NICs which are used by the following networks:

- External API - This is the network that users will use to make requests to the cloud
- Cloud Management - This is the network that will be used for all internal traffic
  between the cloud services, This network is also used to install and configure the nodes.
  This network needs to be on an untagged VLAN
- SWIFT - This network is used for internal Swift comunications between the Swift servers

An example set of networks are defined in ***data/networks.yml***. You will need to
modify this file to reflect your environment.

The example uses the devices hed3 & hed4 as a bonded network for all services.
If you need to modify these for your environment they are defined in
***data/net_interfaces.yml*** The network devices eth3 & eth4 are renamed to devices
hed4 & hed5 using the PCI bus mappings secified in  ***data/nic_mappings.yml***.
You may need to modify the PCI bus addresses to match your system.

###Local Storage###

All servers should present a single OS disk, protected by a RAID controller. This
disk needs to be at least 512GB in capacity. In addition the example configures
additional disk depending on the role of the server:

- Controllers:  /dev/sdb and /dev/sdc are configured to be used by Swift account
  and container services
- Object Servers:  /dev/sdb, /dev/sdc, /dev/sdd and /dev/sde are configured to be
  used by the Swift object service

Additional discs can be configured for any of these roles by editing the corresponding
***data/disks_*.yml*** file

