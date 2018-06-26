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

## Create Windows Server Failover Cluster

## Install Container

## Create Docker Swarm Cluster

## Create Container Service
