## Welcome to CORTX FDMI Word Count Application

CORTX is an object storage systems that allows to attach a FDMI application or plugin to the cluster server that can extend or add more functionalities and features to the storage system.

In this case, CORTX exports a more flexible API to include complex tasks to the system such as replication, deduplication, etc.
 
One example of such FDMI application is the `fdmi_app` that works as a python wrapper of the `fdmi_sample_plugin` executable written in C.

The `fdmi_app_word` runs the plugin executable and takes the cluster configuration parameters from default directory `/etc/motr/conf.xc` or as an input.
Then it starts listening for new S3 requests and create/print to stdout an event with the corresponding metadata fields. 

### The Goal of this project
Create an FDMI application that takes the Object-name and Bucket-name information from S3 requests from the AWS client, reads the Object data and print the number of ocurrences of each word.

### Instructions to setup the development enviroment

1. Follow steps to setup a CORTX cluster in the [CORTX server](https://lia54.github.io/cortx-fdmi-app/server) tab
2. Follow steps to create and configure AWS client in the [AWS-Client](https://lia54.github.io/cortx-fdmi-app/awscli) tab


### Code Skeleton
Modify fdmi_app python script to add the new functionalities as follows:

```
1. Create a new function that reads the object data by connecting with the AWS Client.
  # Open a new AWS client session using boto3 and passing your aws_access_key_id and aws_secret_access_key.
  # Create a resource specifying service_name='s3' region_name='US' and enpoint_url='http://s3.seagate.com'
  # Use the following function to read the object data client.Object(bucket_name, object_name).get()['Body'].read()
  # Decode the results using 'utf-8' binary format
  
2. Create a new function that does the word count
  # Call the function that reads the object data
  # Convert Object data to string
  # Remove quotation marks and punctuation
  # Count the number of ocurrences of each word 
    */Every word should be converted to lowercase Ex.: 'The' and 'the' should be counted as the same word /*
  # Load to memory using a python dictionary data structure
  # Return the 40 most popular words with the corresponging count value as a list of tuples.
  
3. Modify the process_fdmi_record() function.
  # Process the KV record and data obtained from the stdout produced by the fdmi_sample_plugin and store it in memory using a list
  # Decode each record using 'hex' hexadecimal and 'utf-8' binary formats
  # Convert to string
  # Get the Object-name and Bucket-name and pass them to the function that read the object data
  # Call the function that does the word count
  # Print to stdout the value returned 
```

### Network Architecture

![CORTX MOTR FDMI Architecture](./images/CORTX-Motr-fdmi.png)

FDMI Motr architecture. 
Each FDMI application is initialized as a Motr Client that communicates with Motr Core by using Remote Procedure Calls (RPCs). 
The AWS Client also communicates with Motr via CORTX S3-server that runs on top of Motr core. 
 


