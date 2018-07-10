*UNDER CONSTRUCTION*

# Cluster Shared Volume for Docker Swarm Cluster
## Overview
- Create an iSCSI Target Cluster with EXPRESSCLUSTER.
  - iSCSI Target virtual disk are saved on a **Mirror Disk** of EXPRESSCLUSTER.
- Create a Cluster Shared Volume (CSV) on iSCSI Target disk.
- Create a container using ```docker service``` command and ```--mount``` option to mount a directory on CSV to save persistent data (e.g. database file).

## Evaluation Environment
- Docker Swarm uses Raft algorithm for node consistency. Then, you should prepare 3 or more nodes for Docker Swarm cluster.
```
+--------------------------+
| Active Directory         |
| - Windows Server 2012 R2 |
+--------------------------+
 |
 |  +--------------------------------+             
 |  | ws2016-01                      |             +------------------------------------+ 
 +--| - Docker Swarm manager #1      |------+      | iSCSI Target Cluster               |
 |  | - Windows Server, version 1803 |      |      | +--------------------------------+ |
 |  +--------------------------------+      |      | | ws2016-04                      | |
 |                                          |      | | - Cluster Node #1              | |
 |  +--------------------------------+      |      | | - Windows Server, version 1803 | |
 |  | ws2016-02                      |      |      | | - EXPRESSCLUSTER X 4.0         | |
 +--| - Docker Swarm manager #2      |------+------| +--------------------------------+ |
 |  | - Windows Server, version 1803 |      |      |    |                               |
 |  +--------------------------------+      |      |    | Mirroring                     |
 |                                          |      |    |                               |
 |  +--------------------------------+      |      | +--------------------------------+ |
 |  | ws2016-03                      |      |      | | ws2016-05                      | |
 +--| - Docker Swarm manager #3      |------+      | | - Cluster Node #2              | |
    | - Windows Server, version 1803 |             | | - Windows Server, version 1803 | |
    +--------------------------------+             | | - EXPRESSCLUSTER X 4.0         | |
                                                   | +--------------------------------+ |
                                                   +------------------------------------+
```

## Create iSCSI Target Cluster
1. Create two virtual disks. The one is for quorum disk and the other is for Cluster Shared Volume.

## Connect iSCSI Disk

## Create Windows Server Failover Cluster
1. Create a new cluster.
   ```powershell
   PS> New-Cluster -Name docker -node ws2016-01,ws2016-02,ws2016-03 -StaticAddress <Cluster IP Address>
   ```
1. Check the cluster resources.
   ```powershell
   PS> Get-ClusterResources
   
   Name                 State  OwnerGroup    ResourceType
   ----                 -----  ----------    ------------
   Cluster Disk 1       Online Cluster Group Physical Disk
   Cluster Disk 1       Online Cluster Group Physical Disk
   Cluster IP Address   Online Cluster Group IP Address
   Cluster Name         Online Cluster Group Network Name
   Storage Qos Resource Online Cluster Group Storage QoS Policy Manager
   ```
1. Check which one is Quorum Disk.
   ```powershell
   PS> Get-ClusterQuorum

   Cluster              QuorumResource
   -------              --------------
   docker               Cluster Disk 1
   ```
1. Add the normal cluster disk to Cluster Shared Volume.
   ```powershell
   PS> Add-ClusterSharedVolume "Cluster Disk 2"
   ```
1. Chech if the Cluster Shared Volume is available.
   ```powershell
   PS> Get-ClusterSharedVolume
   
   Name           State  Node
   ----           -----  ----
   Cluster Disk 2 Online ws2016-01
   ```
1. Create a directory on the Cluster Shared Volume.
   ```bat
   C:\> mkdir C:\ClusterStorage\Volume1\share
   ```
## Install Docker
Do the following steps on ws2016-01, ws2016-02 and ws2016-03.
1. Install Docker-Microsoft PackageManagement Provider.
   ```powershell
   PS> Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
   ```
1. Install the latest Docker.
   ```powershell
   PS> Install-Package -Name docker -ProviderName DockerMsftProvider
   ```
1. Restart the servers.
1. Get a container image to create a container service.
   ```bat
   C:\> docker pull microsoft/windowsservercore
   ```   

## Create Docker Swarm Cluster
1. Run the following command on ws2016-01 to initialize a swarm cluster.
   ```bat
   C:\> docker swarm init --advertise-addr=<HOSTIPADDRESS> --listen-addr <HOSTIPADDRESS>:2377
   ```
1. Get the token to join the swarm cluster.
   ```bat
   C:\> docker swarm join-token manager
   ```
1. Run the following command on ws2016-02 and ws2016-03 to join the swarm cluster.
   ```bat
   C:\> docker swarm join --token <MANAGERJOINTOKEN> <MANAGERIPADDRESS>
   ```
1. Check the all nodes are running.
   ```bat 
   C:\> docker node ls
   ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
   9dxqrn6sl5n1nvyk8selnhk9x *   ws2016-01           Ready               Active              Leader              18.03.1-ee-1
   tob06is94mvfzwvsyl1uozwh0     ws2016-02           Ready               Active              Reachable           18.03.1-ee-1
   xveucfzi1y7dposwop7jrvc48     ws2016-03           Ready               Active              Reachable           18.03.1-ee-1
   ```
1. Create an overlay network.
   ```bat
   docker network create --driver=overlay oln
   ```
1. Check the overlay network is running.
   ```bat
   C:\> docker network ls
   NETWORK ID          NAME                DRIVER              SCOPE
   gr31xyo9hxs9        oln                 overlay             swarm
   r072z5kc7ehb        ingress             overlay             swarm
   2427ae354703        nat                 nat                 local
   16942e03c8f6        none                null                local
   ```
## Create Container Service
1. Create a service using a container image.
   ```bat
   docker service create --name=test --mount type=bind src="C:\ClusterStorage\Volume1\share" "dst=C:\mydata" --endpoint-mode dnsrr --network=oln -d -t microsoft/windowsservercore cmd.exe
   ```
1. Check if the service is available.
   ```bat
   C:\> docker service ls
   ID            NAME  MODE        REPLICAS  IMAGE                            PORTS
   l0597119sben  test  replicated  1/1       microsoft/windowsservercore:1803        
   ```
1. Chekc if the service is running.
   ```bat
   C:\> docker service ps test
   ID           NAME    IMAGE                             NODE       DESIRED STATE  CURRENT STATE           (snip)
   tej372fvlhk2 test.1  microsoft/windowsservercore:1803  ws2016-01  Running        Running 19 minutes ago  (snip)
   ```
1. Run the following command to run the same container on available nodes.
   ```bat
   C:\> docker service scale test=2
   ```   

## Link
- Container Host Deployment - Windows Server
  - https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/deploy-containers-on-server
- Getting Started with Swarm Mode
  - https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/swarm-mode
