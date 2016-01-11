
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


##Helion Single Region Mid-Size model##
###Introduction###

The mid-size model is intended as a template for a moderate sized cloud. The Control plane is made up of multiple server clusters to provide sufficient computational, network and IOPS capacity for a mid-size production style cloud.  

The Helion mid size model is architected as follows:

####Control plane:

  - Core cluster: Runs Core OpenStack Services, (e.g. keystone, nova api, gance api, netron api, horizon, heat api). Default configuration is two nodes of role type ROLE-CORE

  - Metering & Monitoring cluster: Runs the OpenStack Services for metering & monitoring (e.g. celiometer, monasca & logging). Default configuration is three nodes of role type ROLE-MTRMON

  - Database & Message Queue Cluster: Runs clustered MySQL and RabbitMQ services to support the Helion cloud infrastructure. Default configuration is three nodes of role type ROLE-DBMQ. Three nodes are required for high availability.

  - Swift PAC cluster: Runs the Swift Proxy, Account and Container services. Default configuration is three nodes or role type ROLE-SWPAC.
  - Neutron Agent cluster: Runs Neutron VPN (L3), DHCP, Metadata and OpenVswitch agents. Default configuration is three nodes or role type ROLE-NEUTRON.

####Resource nodes: Three resource pools are defined:

  - Compute: Runs nova compute sand associated services. Runs on nodes of role type ROLE-COMPUTE. The example lists 3 nodes, one node is required at a minimum.

  - Vsa: Runs the Store Virtual VSA appliance. Runs on nodes of role type ROLE-VSA. One node minimum is required for demo purposes, three for high availability. The example lists 1 node.

  - Object: Runs the Swift object service. Runs on nodes of role type ROLE-SWPAC. The minimum node count should match your sift replica count. The example lists 3 nodes.



The minimum node count required to run the model un-modified is 20 nodes. This can be reduced by consolidating servies on the control plance clusters.

###Networking###

The example requires the following networks:

IMPI/iLO network, connected to the deployer and the IPMI/iLO ports of all servers

A network connected to a dedicated NIC on each server used to install the OS and install and configure Helion. In a future Beta release this network can be subsumed into system networks (below).

A pair of bonded NICs which are used used by the following networks:

- External API - This is the network that users will use to make requests to the cloud 
- Internal API - This is the network that will be used within the cloud for API access between services
- External VM - This is the network that will be used to provide access to VMs (via floating IP addresses)
- Guest - This is the network that will carry traffic between VMs on private networks within the cloud
- Cloud Management - This is the network that will be used for all internal traffic between the cloud services. In the example this is shown as untagged. It can be tagged if needed.
- iSCSI - This network is used to host all iSCSI (Cinder back end) traffic between Nova compute and the VSA servers
- SWIFT - This network is used for internal Swift comunications between the Swift servers 

Note that the EXTERNAL\_API network must be reachable from the EXTERNAL\_VM network if you want VMS to be able to make  API calls to the cloud.

An example set of networks are defined in ***data/networks.yml***.    You will need to modify this file to reflect your environment.

The example uses eth2 for the install network and eth3 & eth4 for the bonded network.   If you need to modify these
for your environment they are defined in ***data/net_interfaces.yml***

###Adapting the mid-size model to fit your environment###

The minimum set of changes you need to make to adapt the model for your environment are:

- Update baremetalConfig.yml to list the details of your bare metal servers. You need to perform this step if you are using the HLM supplied Cobber playbooks to install hLinux on your servers. 

- Update servers.yml to list the details of your bare metal servers.

- Update the networks.yml file to replace network CIDRs and VLANs with site specific values

- Update the nic_mappings.yml file to ensure that network devices are mapped to the correct physical port(s)

- Review the disk models (disks_*.yml) and confirm that the associated
    servers have the number of disks required by the disk model. The device
    names in the disk models might need to be adjusted to match the probe oder
    of your servers. 
The default number of disks for the Swift nodes (3 disks) is set low on purpose to facilitate deployment on generic hardware. For production scale Swift the servers should have more disks, e.g. 6 on SWPAC nodes and 12 on SWOBJ nodes. If you allocate more Swift disks then you should review the ring power in the Swift ring confguration. This is documented in the Swift section
Disk models are provided as follows:

  - DISK SET CONTROLLER: Minimum 1 disk
  - DISK SET DBMQ: Minimum 3 disks
  - DISK SET COMPUTE: Minimum 2 disks
  - DISK SET SWPAC: Minimum 3 disks
  - DISK SET SWOBJ: Minimum 3 disks
  - DISK SET VSA: Minimum 3 disks



- Update the net interfaces.yml file to match the server NICs used in your configuration. This file has a separate interface model definition for each of the following:

  - INTERFACE SET CONTROLLER
  - INTERFACE SET VSA
  - INTERFACE SET DBMQ
  - INTERFACE SET SWPAC
  - INTERFACE SET SWOBJ
  - INTERFACE SET COMPUTE

