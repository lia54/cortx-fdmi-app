## Welcome to CORTX FDMI Word Count Application

CORTX is an object storage systems that allows to attach a FDMI application or plugin to the cluster server that can extend or add more functionalities and features to the storage system.

In this case, CORTX exports a more flexible API to include complex tasks to the system such as replication, deduplication, etc.
 
One example of such FDMI application is the `fdmi_app_word_count` that works as a python wrapper of the `fdmi_sample_plugin` executable written in C.

The `fdmi_app_word_count` runs the plugin executable and takes the cluster configuration parameters from default directory (/etc/motr/conf.xc) or as an input.
Then it starts listening for new S3 requests and create/print to stdout an event with the corresponding metadata fields. 
As a goal, the app takes the Object-name and Bucket-name information, reads the Object data and print the number of ocurrences of each word.

### Network Architecture



