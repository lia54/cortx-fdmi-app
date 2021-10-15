## Setting AWS Client


### Prerequisites

- A Computer or VM with CentOS 7.8 (or later).
- `$ sudo yum install epel-release`
- `$ sudo yum install ansible`


### Create AWS Account on the CORTX server

`$ s3iamcli CreateAccount -n liana -e liana@seagate.com --ldapuser sgiamadmin --ldappasswd seagate2 --no-ssl`

It will create the following information:
- AccountID
- CanonicalId
- RootUserName
- AccessKeyId
- SecretKey

### Install and Configure AWS cli
```
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ yum install unzip+ or yum install unzip --enablerepo=epel
$ unzip awscliv2.zip
$ sudo ./aws/install
$ pip3 install awscli-plugin-endpoint
$ export PATH=/usr/local/bin:$PATH
$ aws configure set plugins.endpoint awscli_plugin_endpoint
$ aws configure set s3.endpoint_url http://s3.seagate.com
$ aws configure set s3api.endpoint_url http://s3.seagate.com
$ aws configure
```
At this point add your AccessKeyId and SecretKey generated above and check the configuration afterwards in the following files to see if they match.
```
$ cat ~/.aws/credentials
$ cat ~/.aws/config
```

Set the network connection

`$ vim /etc/hosts /* add your target node (cortx-server endpoint) e.g. IP s3.seagate.com */`

Check connection

`$ ping s3.seagate.com`

Try some AWS commands and make sure you have sample foo.txt in your directory
```
$ aws s3 ls
$ aws s3 mb s3://seagatebucket
$ aws s3 cp foo.txt s3://seagatebucket/foo
$ aws s3 rb s3://seagatebucket
```

### Troubleshooting
When getting Gateway problem
1. hctl shutdown the cluster server
2. hctl bootstrap --nkfs demo_cluster.yaml
3. aws s3 ls should be empty

When having problems to connect to endpoint
1. Uninstall aws
```
$ sudo rm -rf /usr/local/aws-cli
$ sudo rm /usr/local/bin/aws
$ sudo rm /usr/local/bin/aws_completer
```
2. Uninstall awscli-plugin
`$ pip3 uninstall awscli-plugin`

3. reinstall and setup again endpoint and credentials

Command reference:

[Amazon S3 Cli](https://docs.aws.amazon.com/cli/latest/reference/s3/)

Other reference:
 
[CORTX S3-server Quick Start Guide](https://github.com/Seagate/cortx-s3server/blob/main/docs/CORTX-S3%20Server%20Quick%20Start%20Guide.md#14-test-your-build-using-s3-cli)
