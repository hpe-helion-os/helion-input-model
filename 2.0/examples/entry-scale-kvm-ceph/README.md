
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


##Helion Entry Scale Cloud with Ceph Example##

The input files in this example deploy a cloud that has the following characteristics:


### Control Planes ###

- A single control plane consisting of three servers that co-host all of the required services. In addition, each hosts Ceph monitor service as well.

###Resource Pools###

- One compute Node

- Three Ceph OSD Nodes

*Additional resource nodes can be added to the configuration.*

*Minimal Swift Resources are provided by the control plane*

###Deployer Node###


This configuration runs the lifecycle-manager (formerly referred to as the deployer) on a control plane node.
You need to include this node address in your servers.yml definition. This function does not need a dedicated network.

*The minimum server count for this example is therefore 7 servers (Control Plane (x3) + Compute (x1) + OSD (x3))*

An example set of servers are defined in ***data/servers.yml***.   You will need to modify this file to reflect your specific environment.


###Networking###

The example requires the following networks:

IPMI/iLO network, connected to the deployer and the IPMI/iLO ports of all servers

A pair of bonded NICs which are used by the following networks:

- External API - This is the network that users will use to make requests to the cloud
- External VM - This is the network that will be used to provide access to VMs (via floating IP addresses)
- Guest - This is the network that will carry traffic between VMs on private networks within the cloud
- Cloud Management - This is the network that will be used for all internal traffic between the cloud services, This network is also
used to install and configure the nodes. This network needs to be on an untagged VLAN

Note that the EXTERNAL\_API network must be reachable from the EXTERNAL\_VM network if you want VMs to be able to make API calls to the cloud.

An example set of networks are defined in ***data/networks.yml***.    You will need to modify this file to reflect your environment.

The example uses the devices hed3 & hed4 as a bonded network for all services.   If you need to modify these
for your environment they are defined in ***data/net_interfaces.yml*** The network devices eth3 & eth4 are renamed to devices hed4 & hed5 using the PCI bus mappings secified in  ***data/nic_mappings.yml***. You may need to modify the PCI bus addresses to match your system.

###Local Storage###

All servers should present a single OS disk, protected by a RAID controller. This disk needs to be at least 512GB in capacity.   In addition the example configures one additional disk depending on the role of the server:

- Controllers:  /dev/sdb is configured to be used by Swift
- Compute Severs:  /dev/sdb is configured as an additional Volume Group to be used for VM storage
- OSD Servers:  /dev/sdc, /dev/sdd, /dev/sde, /dev/sdf and /dev/sdg are configured for OSD data and journal storage

Additional discs can be configured for any of these roles by editing the corresponding ***data/disks_*.yml*** file

