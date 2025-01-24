---
description: List of commonly used AWS CLI
---

# Cli AWS - Incident

### With EC2 - Network - Log

**List Ec2 ldz**

```
aws ec2 describe-instances --profile aws-tcbs | jq -r '.Reservations[].Instances[] | .InstanceId + " " + .InstanceType + " " + (.Tags[] | select(.Key == "Name").Value)'
```

**List Security GP**

```
aws ec2 describe-security-groups --profile aws-tcbs | jq -r '.SecurityGroups[]|.GroupId+" "+.GroupName'
```

**List subnet vpc-id**

```
aws ec2 describe-subnets--filter Name=vpc-id,Values=<Your_VPC_ID> --profile aws-tcbs | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)'
```

**List log Avaiable Region**

```
aws logs describe-log-groups --profile aws-tcbs --region <region>
```

**CP log**

```
aws s3 cp s3://<log_bucket_here>/AWSLogs . --recursive --profile aws-tcbs
```

**Snapshot and create Volume**

```
aws ec2 create-snapshot --volume-id <volume_id> --description "Snapshotcreated"
aws ec2 create-volume --availability-zone ap-southeast-1 --snapshot-id <snapshot_id>
```

**Use coldsnap download snapshot**

Ref : https://github.com/awslabs/coldsnap

```
coldsnap --region ap-southeast-1 download <snapshot_id> image.dd
```

**Mount snapshot**

```
aws ec2 attach-volume --volume-id <volume_id> --instance-id <DFIR_instance> --device </dev/sdX>
```

### With IAM Log

Go to the cloudwatch console > select insight > logs > then choose your log groups and set your time constraints. Use the following queries to quickly identify suspicious activity:

#### IAM Logs

* List all IAM access denied attemptsList all IAM user and role creation events filter errorCode like

```
/Unauthorized|Denied|Forbidd
en/ | fields awsRegion,
userIdentity.arn, eventSource,
eventName, sourceIPAddress,
userAgent
```

* List All IAM user and role creation events

```
filter eventName="CreateUser" or eventName = "CreateRole" |
fields
requestParameters.userName,
requestParameters.roleName,
responseElements.user.arn,
responseElements.role.arn,
sourceIPAddress, eventTime,
errorCode
```

* List the actions an access key has performed

```
filter userIdentity.accessKeyId
="<Access_Key>" | fields
awsRegion, eventSource,
eventName, sourceIPAddress,
userAgent
```

* List all "ListBucket" event

```
filter eventName ="ListBuckets"
| fields awsRegion, eventSource,
eventName, sourceIPAddress,
userAgent
```

* List IAM actions performed by a specified IP

```
filter sourceIPAddress =
"192.0.2.1" | fields awsRegion,
userIdentity.arn, eventSource,
eventName, sourceIPAddress,
userAgent
```

* List all roles in json format

```
aws iam list-roles
```

* List all users

```
aws iam list-users --output table --query
'Users[*].UserName'
```

* List all groups

```
aws iam list-groups --output table --query
'Groups[*].GroupName'
```

* Block role

```
aws iam put-role-policy --role-name <ROLE> --policy-name DenyAll --policy-document '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*"
    }
  ]
}'
```

* Remove block

```
aws iam delete-role-policy --role-name <ROLE>
--policy-name DenyAll
```

* Block user

```
aws iam put-user-policy --user-name <USER> --policy-name DenyAll --policy-document '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*"
    }
  ]
}'
```

* Remove block

```
aws iam delete-user-policy --user-name <USER>
--policy-name DenyAll
```

* Disable access key

```
aws iam update-access-key --access-key-id <Access_Key> --status Inactive --user-name user
```

#### VPC flow Logs

* List reject requests by IP

```
filter action="REJECT" | stats
count(*) as numRejections by
srcAddr | sort numRejections
desc
```

* List requests originating from a specific ip

```
filter srcAddr = "192.0.2.1" |
fields @timestamp, interfaceId,
dstAddr, dstPort, action
```

* List outgoing requests from a specific IP

```
filter srcAddr = "10.1.1.1" | stats
count(*) as numConnections by
dstAddr | sort numConnections
desc
```

* List reject requests

```
filter action="REJECT" and
srcAddr like /^10\./ | stats
count(*) as numRejections by
srcAddr | sort numRejections
desc
```
