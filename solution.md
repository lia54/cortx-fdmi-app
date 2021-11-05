## Solution demo video for creating the CORTX FDMI Word Count application


### Link to demo video 

- A VM with CentOS 7.8 (or later) for installing and deploying the CORTX server.
- A CORTX rpm installed with all four components: Motr, Hare, S3server and Cortx-utils 
- A VM with CentOS 7.8 (or later) with as AWS client 

The video demostrates how to run the application in a real CORTX setup.

[Demo video](images/Liana Valdes FDMI app demo video-20211014_105759-Meeting Recording.mp4)

[Solution patch for CORTX Motr/S3 server setup](https://gist.github.com/trshaffer/9b2fd7f352e3dc3f688af789e3e451d7#file-0001-local-changes-patch)

### Function that does word count

```
def count_words(text_obj):
    my_counts= {}
    
    for obj in text_obj:
        obj = obj.translate(str.maketrans('', '', string.punctuation))
        obj = obj.replace('“', "").replace("”", "")
        words = str(obj).split()

        for word in words:
            word_lw = word.lower()
            if word_lw in my_counts:
                my_counts[word_lw] +=1
            else:
                my_counts[word_lw] =1
    # Sort count dict
    sorted_counts= {k: v for k, v in sorted(my_counts.items(), key=lambda item: item[1], reverse=True)}
    
    # Return the 40 most popular words           
    return list(sorted_counts.items())[:40]
 ```


