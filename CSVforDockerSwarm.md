*UNDER CONSTRUCTION*

# Cluster Shared Volume for Docker Swarm Cluster
## Overview
- Create an iSCSI Target Cluster with EXPRESSCLUSTER.
  - iSCSI Target virtual disk are saved on a **Mirror Disk** of EXPRESSCLUSTER.
- Create a Cluster Shared Volume (CSV) on iSCSI Target disk.
- Create a container using ```docker service``` command and ```--mount``` option to mount a directory on CSV to save persistent data (e.g. database file).

## Evaluation Environment
```
+--------------------------+
| Active Directory         |
| - Windows Server 2012 R2 |
+--------------------------+
 |
 |  +--------------------------------+             +------------------------------------+
 +--| Docker Swarm manager #1        |------+      | iSCSI Target Cluster               |
 |  | - Windows Server, version 1803 |      |      | +--------------------------------+ |
 |  +--------------------------------+      |      | | Cluster Node #1                | |
 |                                          |      | | - Windows Server, version 1803 | |
 |  +--------------------------------+      |      | | - EXPRESSCLUSTER X 4.0         | |
 +--| Docker Swarm manager #2        |------+------| +--------------------------------+ |
 |  | - Windows Server, version 1803 |      |      |    |                               |
 |  +--------------------------------+      |      |    | Mirroring                     |
 |                                          |      |    |                               |
 |  +--------------------------------+      |      | +--------------------------------+ |
 +--| Docker Swarm manager #3        |------+      | | Cluster Node #2                | |
    | - Windows Server, version 1803 |             | | - Windows Server, version 1803 | |
    +--------------------------------+             | | - EXPRESSCLUSTER X 4.0         | |
                                                   | +--------------------------------+ |
                                                   +------------------------------------+
```

## Create iSCSI Target Cluster
1. Create two disk. The one is for quorum disk and the other is for Cluster Shared Volume.

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
1. Ch
   ```powershell
   PS C:\> Get-ClusterSharedVolume
   
   Name           State  Node
   ----           -----  ----
   Cluster Disk 2 Online ws2016-243
   ```
## Install Container

## Create Docker Swarm Cluster

## Create Container Service
