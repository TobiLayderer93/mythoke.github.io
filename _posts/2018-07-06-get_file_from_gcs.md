---
title: "Get file from bucket - Node.js sample"
date: 2018-07-03
tags: [GCP, node.js, cloud-storage]
excerpt: "Google Cloud Platform, node.js, Google Cloud Storage"
---

## Sample node.js app to download file from Google cloud storage bucket

Here is a simple sample code for getting files from cloud storage: 

* first retrieving filenames from bucket
* second download files from bucket 

```javascript

const Storage = require('@google-cloud/storage');
         
// Creates a Client 
const storage = new Storage({
        projectId: "myProjectId",
        keyFilename: 'PATH/TO/YOUR/service-account-key.json'
    });
    
const bucketName = 'myBucket';
const destFilename = '/path/needs/to/be/defined.txt'; 
const options = {
      destination: destFilename,
    };

// Get all filenames from Bucket
getFilesFromBucket: function(){
    storage
        .bucket("rt-tf-monitoring/" + bucketPath)
        .getFiles()
        .then( results => {
            const files = results[0];
            console.log('Files:');
            files.forEach(file => {
                console.log(file.name);
                downloadFileFromBucket(file)
            });
        })
        .catch(err => {
            console.error('ERROR:', err);
        })  
    }

// Downloads file from bucket
downloadFileFromBucket: function(srcfilename){
    storage
      .bucket(bucketName)
      .file(srcFilename)
      .download(options)
      .catch(err => {
        console.error('ERROR:', err);
      });
    }

```

