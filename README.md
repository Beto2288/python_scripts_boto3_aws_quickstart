# Quick Start with Boto3 and AWS

---

**List instances**

```
#!/usr/bin/env python
import boto3
ec2 = boto3.resource('ec2')
for instance in ec2.instances.all():
    print(instance.id, instance.state, instance.instance_type)
```

**Create an instance**

```
#!/usr/bin/env python
import boto3
ec2 = boto3.resource('ec2')
instance = ec2.create_instances(
    ImageId='Your AMI ID',
    MinCount=1,
    MaxCount=1,
    InstanceType='t2.micro')
print(instance[0].id)
```

**Terminate an instance**

```
#!/usr/bin/env python
import boto3
import sys
ec2 = boto3.resource('ec2')
for instance_id in sys.argv[1:]:
    instance = ec2.Instance(instance_id)
    response = instance.terminate()
    print(response)
```

**Checking status of the instance**

```
#!/usr/bin/env python
import boto3
ec2 = boto3.resource('ec2')
for instance in ec2.instances.all():
    print(instance.id, instance.state, instance.instance_type)
```

**Create S3 bucket**

```
#!/usr/bin/env python
import boto3
import sys
s3 = boto3.resource("s3")
for bucket_name in sys.argv[1:]:
    try:
        response = s3.create_bucket(Bucket=bucket_name)
        print(response)
    except Exception as error:
        print(error)
```

**Create an instance, create a file with information of the instance and put it on the S3 bucket created**

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- 
import boto3
import os
import sys

ec2 = boto3.resource('ec2')
instances = ec2.create_instances(
    ImageId='Your AMI ID',
    MinCount=1,
    MaxCount=1,
    InstanceType='t2.micro')
for instance in instances:
    print("Waiting until running...")
    instance.wait_until_running()
    instance.reload()
    ID_instance = instance.id
    DNS_instance = instance.public_dns_name
    IP_instance = instance.public_ip_address
    file = open("/home/ec2-user/name_of_your_file.txt", "w")
    file.write(ID_instance +" "+ DNS_instance +" "+ IP_instance + os.linesep)
    file.write("TEXTO....")
    file.close()
print((instance.id, instance.state, instance.public_dns_name, instance.public_ip_address,))

s3 = boto3.resource('s3')
bucket_name = sys.argv[1]
object_name = 'name_of_your_file.txt'
try:
    response = s3.Object(bucket_name, object_name).put(Body=open(object_name, 'rb'))
    print(response)
except Exception as error:
    print(error)
```
