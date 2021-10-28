# Storage Buckets
## WTF
Storage buckets, like s3.

## Recon
```
site:appspot.com #app engine
site:appspot.com #app engine
http://storage.googleapis.com/<bucket_name>
https://<bucket_name>.storage.googleapi.com
https://buckets.grayhatwarfare.com/
search github for "storage.googleapis.com" <companyname>
check mobile apps for urls for buckets: i.e gs://<something>.appspot.com>
msf -> auxiliary/scanner/http/enum_wayback
don't forget to bruteforce file names if we can't list them all, the response has "NoSuchKey" if it doesnt exist
```
## Tools
https://github.com/RhinoSecurityLabs/GCPBucketBrute
```
python3 gcpbucketbrute.py -k test -u # basic unauth scan for "test" keyword
```

## Commands
```
gsutil ls -r gs://<bucket-name> # list bucket contents
# some buckets are set up to only be accessed by authenticated users, it is always worth checking logged in, to do this we can use
gcloud auth login # and then use gsutil ls again 
```

# App Engine
## WTF
Known as GAE, platform as a service for developing and hosting web apps.

## Recon
```
site:bc.googleusercontent.com intext:<organisation> 
```



  
