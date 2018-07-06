---
title: "Query Stackdriver API example with OAuth"
date: 2018-07-06
tags: [GCP, Stackdriver, API, OAuth, Ruby]
excerpt: "Google Cloud Platform, Stackdriver, API, OAuth, Ruby"
---

# Query Stackdriver logging APIv2


To query Stackdriver logging APIv2 OAuth authentication is necessary. API keys won't work because they only support the following Google APIs: 

* Google Cloud Natural Language API
* Google Cloud Speech API
* Google Cloud Translation API
* Google Cloud Vision API
* Google Cloud Endpoints
* Google Cloud Billing Catalog API
* Cloud Data Loss Prevention API


## OAuth authentication 

To authenticate via OAuth several steps need to be performed: 

1. Open APIs & Services in GCP -> Credentials
2. Create Credentials -> OAuth Client ID 
3. Configure Consent screen: 
	-> Name it: "Logging API" (or whatever you like)
	-> Save 
4. Choose "Web Application"
5. Give it a name and enter the following redirect URI "https://developers.google.com/oauthplayground" -> save 

Now you receive your Client ID and your Client secret, both is what you need for the next step: 

1. Visit *https://developers.google.com/oauthplayground/*
2. Settings (gear symbol) -> Tick "Use your own OAuth credentials" -> Enter your Client ID and your Client secret 
3. Chosse APIs you like to query -> Select every Stackdriver API for full support 
4. Click -> Authorize APIs 
5. Now you've received your Authorization Token: Click -> Exchange authorization code for token 
6. Your Refresh token and your Acces token will be created 
7. Tick "Auto-refresh the token before it expires."
8. Now you can test your Request in step 3. 

The Access token which was created can now be used within the skript -> Request header, to query the API with your specified request body. 
A good way to genereate your request body is to generate it with the [Google API Explorer](https://developers.google.com/apis-explorer/#search/stackdriver/m/logging/v2/logging.entries.list) 
-> Entries.list method was already selected in this example but you can query every API provided by Google with this explorer -> Authorize simply by OAuth to test your request. 



## Query filter

All resource used in GCP infrastructre can be queried by their resource.type - here is a complete [list](https://cloud.google.com/logging/docs/api/v2/resource-list) of all resources which could be queried. 

Here are some sample specific resource types: 

### Kubernetes Cluster & Container: 

```
container      -> A Google Container Engine (GKE) container instance.
k8s_cluster    -> A Kubernetes cluster. It contains Kubernetes audit logs from the cluster.
k8s_container  -> A Kubernetes container instance.
k8s_node       -> A Kubernetes node instance. 
k8s_pod        -> A Kubernetes pod instance. 
gke_cluster    -> A Google Container Engine (GKE) Cluster. It contains events and audit logs about cluster operations.
```

### Datastore: 

```
datastore_database -> A Cloud Datastore database. 
datastore_index    -> A Cloud Datastore index. 
```

### Audit Logs (Pub/Sub): 

```
audited_resource  -> A Google Cloud resource that produces an audit log. [service: specification]
```

### Time frame specification: 

```
timestamp >= "2016-11-29T23:00:00Z"
timestamp <= "2016-11-29T23:30:00Z"
```


Sample filter query: 

```
resource.type=\"k8s_cluster\" AND resource.labels.location=\"europe-west1-b\" AND resource.labels.cluster_name=\"container-cluster\" AND resource.jsonPayload.service_id=\"alice-trf-esb-mdng-manufacturer\"
```


## Sample Ruby API call 

One thing to mention is, that the access token has to be placed within the request header. 

```ruby
require 'net/http'
require 'json'
 
def get_stackdriver_logs
	uri = URI('https://logging.googleapis.com/v2/entries:list')
    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = true
    req = Net::HTTP::Post.new(uri.path, 'Content-Type' => 'application/json', 'Authorization' => 'Bearer [Access Token]')
    req.body = {
 				"pageSize": 5,
 				"filter": "resource.type=\"k8s_cluster\" AND resource.labels.location=\"europe-west1-b\" AND resource.labels.cluster_name=\"container-cluster\"",
 				"resourceNames": [
  					"projects/project-id"
 					]
				}.to_json

    res = http.request(req)
    puts(res)
    puts "response #{res.body}"
rescue => e
    puts "failed #{e}"
end


get_stackdriver_logs

```


