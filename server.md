## Setting CORTX-MOTR Cluster

To run our experiments we need a CORTX-MOTR cluster up and running with an S3 Server.

### Prerequisites

- A Computer or VM with CentOS 7.8 (or later).
- A CORTX rpm installed with all four components: Motr, Hare, S3server and Cortx-utils 


### Commands to run

```
# git clone --recursive https://github.com/Seagate/cortx-motr.git
# cd cortx-motr
# sudo scripts/install-build-deps
# scripts/m0 make or rebuild
# hctl status
# hctl shutdown
# hctl bootstrap --mkfs ~/demo_cluster.yaml
# cd ~/cortx-motr/fdmi/plugins
# ./fdmi_app_word_count
```
Command reference:

[CORTX MOTR Quick Start Guide](https://github.com/lia54/cortx-motr/blob/main/doc/Quick-Start-Guide.rst)
