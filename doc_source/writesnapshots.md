# Write snapshots with EBS direct APIs<a name="writesnapshots"></a>

The following steps describe how to use the EBS direct APIs to write incremental snapshots:

1. Use the StartSnapshot action and specify a parent snapshot ID to start a snapshot as an incremental snapshot of an existing one, or omit the parent snapshot ID to start a new snapshot\. This action returns the new snapshot ID, which is in a pending state\.

1. Use the PutSnapshotBlock action and specify the ID of the pending snapshot to add data to it in the form of individual blocks\. You must specify a Base64\-encoded SHA256 checksum for the block of data transmitted\. The service computes the checksum of the data received and validates it with the checksum that you specified\. The action fails if the checksums don't match\.

1. When you're done adding data to the pending snapshot, use the CompleteSnapshot action to start an asynchronous workflow that seals the snapshot and moves it to a completed state\.

Repeat these steps to create a new, incremental snapshot using the previously created snapshot as the parent\.

For example, in the following diagram, snapshot A is the first new snapshot started\. Snapshot A is used as the parent snapshot to start snapshot B\. Snapshot B is used as the parent snapshot to start and create snapshot C\. Snapshots A, B, and C are incremental snapshots\. Snapshot A is used to create EBS volume 1\. Snapshot D is created from EBS volume 1\. Snapshot D is an incremental snapshot of A; it is not an incremental snapshot of B or C\.

![\[EBS direct APIs used to create incremental snapshots.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ebs-apis-write.png)

The following examples show how to write snapshots using the EBS direct APIs\.

**Topics**
+ [Start a snapshot](#start-snapshot)
+ [Put data into a snapshot](#put-data)
+ [Complete a snapshot](#complete-snapshot)

## Start a snapshot<a name="start-snapshot"></a>

------
#### [ AWS CLI ]

The following [start\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/start-snapshot.html) example command starts an `8` GiB snapshot, using snapshot `snap-123EXAMPLE1234567` as the parent snapshot\. The new snapshot will be an incremental snapshot of the parent snapshot\. The snapshot moves to an error state if there are no put or complete requests made for the snapshot within the specified `60` minute timeout period\. The `550e8400-e29b-41d4-a716-446655440000` client token ensures idempotency for the request\. If the client token is omitted, the AWS SDK automatically generates one for you\. For more information about idempotency, see [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)\.

```
aws ebs start-snapshot --volume-size 8 --parent-snapshot snap-123EXAMPLE1234567 --timeout 60 --client-token 550e8400-e29b-41d4-a716-446655440000
```

The following example response for the previous command shows the snapshot ID, AWS account ID, status, volume size in GiB, and size of the blocks in the snapshot\. The snapshot is started in a `pending` state\. Specify the snapshot ID in subsequent `put-snapshot-block` commands to write data to the snapshot, then use the `complete-snapshot` command to complete the snapshot and change its status to `completed`\.

```
{
    "SnapshotId": "snap-0aaEXAMPLEe306d62",
    "OwnerId": "111122223333",
    "Status": "pending",
    "VolumeSize": 8,
    "BlockSize": 524288
}
```

------
#### [ AWS API ]

The following [StartSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_StartSnapshot.html) example request starts an `8` GiB snapshot, using snapshot `snap-123EXAMPLE1234567` as the parent snapshot\. The new snapshot will be an incremental snapshot of the parent snapshot\. The snapshot moves to an error state if there are no put or complete requests made for the snapshot within the specified `60` minute timeout period\. The `550e8400-e29b-41d4-a716-446655440000` client token ensures idempotency for the request\. If the client token is omitted, the AWS SDK automatically generates one for you\. For more information about idempotency, see [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)\.

```
POST /snapshots HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T040724Z
Authorization: <Authentication parameter>

{
    "VolumeSize": 8,
    "ParentSnapshot": snap-123EXAMPLE1234567,
    "ClientToken": "550e8400-e29b-41d4-a716-446655440000",
    "Timeout": 60
}
```

The following example response for the previous request shows the snapshot ID, AWS account ID, status, volume size in GiB, and size of the blocks in the snapshot\. The snapshot is started in a pending state\. Specify the snapshot ID in a subsequent `PutSnapshotBlocks` request to write data to the snapshot\.

```
HTTP/1.1 201 Created
x-amzn-RequestId: 929e6eb9-7183-405a-9502-5b7da37c1b18
Content-Type: application/json
Content-Length: 181
Date: Thu, 18 Jun 2020 04:07:29 GMT
Connection: keep-alive

{
    "BlockSize": 524288,
    "Description": null,
    "OwnerId": "138695307491",
    "Progress": null,
    "SnapshotId": "snap-052EXAMPLEc85d8dd",
    "StartTime": null,
    "Status": "pending",
    "Tags": null,
    "VolumeSize": 8
}
```

------

## Put data into a snapshot<a name="put-data"></a>

------
#### [ AWS CLI ]

The following [put\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/put-snapshot.html) example command writes `524288` Bytes of data to block index `1000` on snapshot `snap-0aaEXAMPLEe306d62`\. The Base64 encoded `QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=` checksum was generated using the `SHA256` algorithm\. The data that is transmitted is in the `/tmp/data` file\.

```
aws ebs put-snapshot-block --snapshot-id snap-0aaEXAMPLEe306d62 --block-index 1000 --data-length 524288 --block-data /tmp/data --checksum QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM= --checksum-algorithm SHA256
```

The following example response for the previous command confirms the data length, checksum, and checksum algorithm for the data received by the service\.

```
{
    "DataLength": "524288",
    "Checksum": "QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=",
    "ChecksumAlgorithm": "SHA256"
}
```

------
#### [ AWS API ]

The following [PutSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_PutSnapshotBlock.html) example request writes `524288` Bytes of data to block index `1000` on snapshot `snap-052EXAMPLEc85d8dd`\. The Base64 encoded `QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=` checksum was generated using the `SHA256` algorithm\. The data is transmitted in the body of the request and is represented as *BlockData* in the following example\.

```
PUT /snapshots/snap-052EXAMPLEc85d8dd/blocks/1000 HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
x-amz-Data-Length: 524288
x-amz-Checksum: QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=
x-amz-Checksum-Algorithm: SHA256
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T042215Z
X-Amz-Content-SHA256: UNSIGNED-PAYLOAD
Authorization: <Authentication parameter>
          
          BlockData
```

The following is example response for the previous request confirms the data length, checksum, and checksum algorithm for the data received by the service\. 

```
HTTP/1.1 201 Created
x-amzn-RequestId: 643ac797-7e0c-4ad0-8417-97b77b43c57b
x-amz-Checksum: QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=
x-amz-Checksum-Algorithm: SHA256
Content-Type: application/json
Content-Length: 2
Date: Thu, 18 Jun 2020 04:22:12 GMT
Connection: keep-alive

{}
```

------

## Complete a snapshot<a name="complete-snapshot"></a>

------
#### [ AWS CLI ]

The following [complete\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/complete-snapshot.html) example command completes snapshot `snap-0aaEXAMPLEe306d62`\. The command specifies that `5` blocks were written to the snapshot\. The `6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=` checksum represents the checksum for the complete set of data written to a snapshot\. For more information about checksums, see [Use checksums](ebsapis-using-checksums.md) earlier in this guide\.

```
aws ebs complete-snapshot --snapshot-id snap-0aaEXAMPLEe306d62 --changed-blocks-count 5 --checksum 6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c= --checksum-algorithm SHA256 --checksum-aggregation-method LINEAR
```

The following is an example response for the previous command\.

```
{
    "Status": "pending"
}
```

------
#### [ AWS API ]

The following [CompleteSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_CompleteSnapshot.html) example request completes snapshot `snap-052EXAMPLEc85d8dd`\. The command specifies that `5` blocks were written to the snapshot\. The `6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=` checksum represents the checksum for the complete set of data written to a snapshot\.

```
POST /snapshots/completion/snap-052EXAMPLEc85d8dd HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
x-amz-ChangedBlocksCount: 5
x-amz-Checksum: 6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=
x-amz-Checksum-Algorithm: SHA256
x-amz-Checksum-Aggregation-Method: LINEAR
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T043158Z
Authorization: <Authentication parameter>
```

The following is an example response for the previous request\.

```
HTTP/1.1 202 Accepted
x-amzn-RequestId: 06cba5b5-b731-49de-af40-80333ac3a117
Content-Type: application/json
Content-Length: 20
Date: Thu, 18 Jun 2020 04:31:50 GMT
Connection: keep-alive

{"Status":"pending"}
```

------