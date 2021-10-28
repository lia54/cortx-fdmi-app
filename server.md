## Setting CORTX-MOTR Cluster

To run our experiments we need a CORTX-MOTR cluster up and running with an S3 Server.

### Prerequisites

- A Computer or VM with CentOS 7.8 (or later).
- A CORTX rpm installed with all four components: Motr, Hare, S3server and Cortx-utils 

or 
- CORTX motr and s3 server components running

### Commands to run to install CORTX-motr server after having hare and S3 already deployed

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

[CORTX Hare Quick Start Guide](https://github.com/Seagate/cortx-hare#readme)

### Commands to run to install CORTX-Motr and S3 server setup

Please take a look at the following link tutorial 

[CORTX S3 server quickstart guide](https://github.com/Seagate/cortx-s3server/blob/main/docs/CORTX-S3%20Server%20Quick%20Start%20Guide.md),

We need to make some modifications to the previous guide.

You'll need a VM set up with CentOS 7.8 or 7.9,
ideally a fresh install.

You'll use the `main` branch of the `cortx-s3server` repo to get a current version of the S3 server
(commit 04432a).

We'll only follow the guide as far as the build step,
no need to do tests or build RPMs.

Follow the instructions in the guide,
with the following exceptions:
- Skip steps 10 and 11 installing Python utils and Kafka.
  Later scripts will do this
  (though if you run into issues,
  it might be a good idea to check these).
- Don't create a message bus configuration for Kafka,
  again this is handled by the `./init.sh` script in the next step.
- Invoke `./init.sh` without the `-a` flag.
- Stop before invoking `./jenkins-build.sh`.

This demo depends on recent additions to MOTR,
so we'll need to change the version used.
In addition, we need to use some hacks to get the FDMI plugin set up.

From the `cortx-s3server` directory,

```
cd third_party/motr
git checkout 6f3d78
git submodule update --init --recursive
git am path/to/0001-local-changes.patch
cd ../..
git add third_party/motr
git commit
```

The build phase will reset all submodules,
so we need to have a throwaway commit.
Next, run the actual build and wait....

```
./jenkins-build.sh --skip_tests
```

If everything goes well,
you'll have a functioning S3 server and MOTR cluster running.
You can test this with the `./iss3up.sh` script.

Next, you'll need to get the FDMI plugin running.
Don't do the entire "Test your Build using S3-CLI" section
(package conflicts will break your build),
but do step 5.iii of that section to generate AWS CLI credentials via `s3iamcli`.

You'll now need to edit the FDMI plugin script at
`third_party/motr/fdmi/plugins/fdmi_app_word_count`:
- paste the generated AWS access and secret key into the boto3 setup
- find and paste in the endpoint info for the MOTR cluster

The info about the MOTR cluster is printed during server startup,
you may need to scroll up a ways.
There should be a section starting with

```
MOTR is UP.
Motr client config:
```

You'll get cluster info from this section and update the FDMI plugin so it knows where to connect.
In the `get_cluster_info()` function,
match the following flags to the following fields:
- `-l`: The second `FDMI plugin ep`
  (we don't use the first `FDMI plugin ep` and `FDMI filter fid` pair,
  just ignore them)
- `-h`: `HA addr`
- `-p`: `Profile fid`
- `-f`: `Process fid`
- `-g`: Second `FDMI filter fid`

After saving, you can start the FDMI plugin!
You may need to provide the path to the `fdmi_sample_plugin` executable
(in the same directory as the script).

Now if you configure AWS CLI and do some S3 puts,
you should see FDMI events and word counts.

Link to the changes needed to run with this setup in the following files:
`m0t1fs/linux_kernel/st/m0t1fs_common_inc.sh`
`motr/st/utils/motr_services.sh`

[Patch link with solution](https://gist.github.com/trshaffer/9b2fd7f352e3dc3f688af789e3e451d7#file-0001-local-changes-patch)
