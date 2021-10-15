## Setting AWS Client


### Prerequisites

- A Computer or VM with CentOS 7.8 (or later).
-`$ sudo yum install epel-release`
-`$ sudo yum install ansible`


### Create AWS Account on the CORTX server

`$ s3iamcli CreateAccount -n liana -e liana@seagate.com --ldapuser sgiamadmin --ldappasswd seagate2 --no-ssl`

It will create the following information:
- AccountID
- CanonicalId
- RootUserName
- AccessKeyId
- SecretKey

### Install and Configure AWS cli

