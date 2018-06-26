**UNDER CONSTRUCTION**
# Container Host Cluster

## Overview
- Install EXPRESSCLUSTER on container host OS.
- Create a container and save persistent data (e.g. database file) on a **Mirror Disk** of EXPRESSCLUSTER.
- The containers are controlled by EXPRESSCLUSTER with **Script Resource**.

## Evaluation Environment
```
+------------------------+
| Cluster Node #1        |
| - Windows Server       |
| - EXPRESSCLUSTER X     |
+------------------------+
  |
  | Mirrorring
  |
+------------------------+
| Cluster Node #2        |
| - Windows Server       |
| - EXPRESSCLUSTER X     |
+------------------------+
```
- Windows Server
  - Windows Server, version 1709
  - Windows Server 2016
- EXPRESSCLUSTER X
  - EXPRESSCLUSTER X 4.0
  - EXPRESSCLUSTER X 3.3

