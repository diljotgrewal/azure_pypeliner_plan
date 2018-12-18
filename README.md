# pypeliner aws plan

### AWS Simple Storage Solution

##### S3 file operation Abstraction (AwsSimpleStorage):


create a client
```
    import boto3
    s3 = boto3.client('s3')
```
list bucket
```
    response = s3.list_buckets()
```
Create a new bucket
```
    s3.create_bucket(Bucket='my-bucket')
```
upload to S3
```
    s3.upload_file(filename, bucket_name, filename)
```
download from S3
```
    s3.Bucket(BUCKET_NAME).download_file(KEY, 'my_local_image.jpg')
```
delete from S3
```
    obj = s3.Object("aniketbucketpython", "abcd.txt")
    obj.delete()
```

##### pypeliner storage class (AwsS3Storage) :
methods:
- connect (not required if credential file is already there)0
- push
- pull
- retrieve createtime
- update createtime 
- create_store


### Batch

All jobs run in a container. The submission is very simple, it just takes the image, arguments and flags for docker. 

##### Abstraction class for batch operations

will use boto3. api is pretty simple:

client:
```
import boto3
client = boto3.client('batch')
```
submit job:
```
response = client.submit_job(
    jobName='string',
    jobQueue='string',
    arrayProperties=dict,
    dependsOn=,
    jobDefinition='string',
    parameters=,
    containerOverrides={
        'vcpus': 123,
        'memory': 123,
        'command': [
            'string',
        ],
        'instanceType': 'string',
        'environment': 
    },
    nodeOverrides={
        'nodePropertyOverrides': [
            {
                'targetNodes': 'string',
                'containerOverrides': {
                    'vcpus': 123,
                    'memory': 123,
                    'command': [],
                    'instanceType': 'string',
                    'environment': []
                }
            },
        ]
    },
    retryStrategy={
        'attempts': 123
    },
    timeout={
        'attemptDurationSeconds': 123
    }
)
```

cancel job:
```
response = client.cancel_job(
    jobId='string',
    reason='string'
)
```

list jobs:
```
response = client.list_jobs(
    jobQueue='string',
    arrayJobId='string',
    multiNodeJobId='string',
    jobStatus='SUBMITTED'|'PENDING'|'RUNNABLE'|'STARTING'|'RUNNING'|'SUCCEEDED'|'FAILED',
    maxResults=123,
    nextToken='string'
)
```
functions to create batch resource, roles etc: (Lets use the management console (web portal) for now. its easier and a one time thing)
- create_compute_environment
- create_job_queue
- delete_compute_environment
- delete_job_queue

Set autoscaling:
- using management console for now. one time operation

Set spot instances:
- using management console for now. one time operation

##### Amazon Batch Jobqueue
implements job queue with:
- send
- receive
- wait


### possible issues:
1. resource files (such as job pickles are not downloaded from S3 automatically). We will have to submit a shell script to batch that downloads the pickles from S3 and then runs pypeliner_delegate to launch the job. From this point pypeliner can handle everything else.
2. Disk space inside container: Will have to use a custom AMI instead of the default image
3. reference data (?)


### Setup Process:
1. Create a spot fleet role from management console (5 mins)
2. Create a batch resource from management console (10 mins), also creates all access roles
3. create an AMI
4. create a container registry and push containers



### Resources:
https://aws.amazon.com/blogs/compute/building-high-throughput-genomics-batch-workflows-on-aws-introduction-part-1-of-4/

### Notes: 
the docker container instances come with valid amazon credentials, no need to manually send creds through env vars



# Plan:

1. set up a container registry in aws and push all containers
2. set up and AWS AMI (Amazon Machine Image) with docker and referencedata
3. Add S3 backend to pypeliner and test it locally on aws EC2 instance (instantiated with AMI from 2) with docker containers and S3
4. Add Batch queue
5. run test data through batch + S3

# Research Topics:
- S3 scalability targets