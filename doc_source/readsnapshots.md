# Read snapshots with EBS direct APIs<a name="readsnapshots"></a>

The following steps describe how to use the EBS direct APIs to read snapshots:

1. Use the ListSnapshotBlocks action to view all block indexes and block tokens of blocks in a snapshot\. Or use the ListChangedBlocks action to view only the block indexes and block tokens of blocks that are different between two snapshots of the same volume and snapshot lineage\. These actions help you identify the block tokens and block indexes of blocks for which you might want to get data\.

1. Use the GetSnapshotBlock action, and specify the block index and block token of the block for which you want to get data\.

The following examples show how to read snapshots using the EBS direct APIs\.

**Topics**
+ [List blocks in a snapshot](#list-blocks)
+ [List blocks that are different between two snapshots](#list-different-blocks)
+ [Get block data from a snapshot](#get-block-data)

## List blocks in a snapshot<a name="list-blocks"></a>

------
#### [ AWS CLI ]

The following [list\-snapshot\-blocks](https://docs.aws.amazon.com/cli/latest/reference/ebs/list-snapshot-blocks.html) example command returns the block indexes and block tokens of blocks that are in snapshot `snap-0987654321`\. The `--starting-block-index` parameter limits the results to block indexes greater than `1000`, and the `--max-results` parameter limits the results to the first `100` blocks\.

```
aws ebs list-snapshot-blocks --snapshot-id snap-0987654321 --starting-block-index 1000 --max-results 100
```

The following example response for the previous command lists the block indexes and block tokens in the snapshot\. Use the `get-snapshot-block` command and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\.

```
{
      "Blocks": [
          {
              "BlockIndex": 1001,
              "BlockToken": "AAABAV3/PNhXOynVdMYHUpPsetaSvjLB1dtIGfbJv5OJ0sX855EzGTWos4a4"
          },
          {
              "BlockIndex": 1002,
              "BlockToken": "AAABATGQIgwr0WwIuqIMjCA/Sy7e/YoQFZsHejzGNvjKauzNgzeI13YHBfQB"
          },
          {
              "BlockIndex": 1007,
              "BlockToken": "AAABAZ9CTuQtUvp/dXqRWw4d07eOgTZ3jvn6hiW30W9duM8MiMw6yQayzF2c"
          },
          {
              "BlockIndex": 1012,
              "BlockToken": "AAABAQdzxhw0rVV6PNmsfo/YRIxo9JPR85XxPf1BLjg0Hec6pygYr6laE1p0"
          },
          {
              "BlockIndex": 1030,
              "BlockToken": "AAABAaYvPax6mv+iGWLdTUjQtFWouQ7Dqz6nSD9L+CbXnvpkswA6iDID523d"
          },
          {
              "BlockIndex": 1031,
              "BlockToken": "AAABATgWZC0XcFwUKvTJbUXMiSPg59KVxJGL+BWBClkw6spzCxJVqDVaTskJ"
          },
          ...
      ],
      "ExpiryTime": 1576287332.806,
      "VolumeSize": 32212254720,
      "BlockSize": 524288
  }
```

------
#### [ AWS API ]

The following [ListChangedBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListSnapshotBlocks.html) example request returns the block indexes and block tokens of blocks that are in snapshot `snap-0acEXAMPLEcf41648`\. The `startingBlockIndex` parameter limits the results to block indexes greater than `1000`, and the `maxResults` parameter limits the results to the first `100` blocks\.

```
GET /snapshots/snap-0acEXAMPLEcf41648/blocks?maxResults=100&startingBlockIndex=1000 HTTP/1.1
  Host: ebs.us-east-2.amazonaws.com
  Accept-Encoding: identity
  User-Agent: <User agent parameter>
  X-Amz-Date: 20200617T231953Z
  Authorization: <Authentication parameter>
```

The following example response for the previous request lists the block indexes and block tokens in the snapshot\. Use the GetSnapshotBlock action and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\. 

```
HTTP/1.1 200 OK
  x-amzn-RequestId: d6e5017c-70a8-4539-8830-57f5557f3f27
  Content-Type: application/json
  Content-Length: 2472
  Date: Wed, 17 Jun 2020 23:19:56 GMT
  Connection: keep-alive
  
  {
      "BlockSize": 524288,
      "Blocks": [
          {
              "BlockIndex": 0,
              "BlockToken": "AAUBAcuWqOCnDNuKle11s7IIX6jp6FYcC/q8oT93913HhvLvA+3JRrSybp/0"
          },
          {
              "BlockIndex": 1536,
              "BlockToken": "AAUBAWudwfmofcrQhGVlLwuRKm2b8ZXPiyrgoykTRC6IU1NbxKWDY1pPjvnV"
          },
          {
              "BlockIndex": 3072,
              "BlockToken": "AAUBAV7p6pC5fKAC7TokoNCtAnZhqq27u6YEXZ3MwRevBkDjmMx6iuA6tsBt"
          },
          {
              "BlockIndex": 3073,
              "BlockToken": "AAUBAbqt9zpqBUEvtO2HINAfFaWToOwlPjbIsQOlx6JUN/0+iMQl0NtNbnX4"
          },
          ...
      ],
      "ExpiryTime": 1.59298379649E9,
      "VolumeSize": 3
  }
```

------

## List blocks that are different between two snapshots<a name="list-different-blocks"></a>

Keep the following in mind when making **paginated requests** to list the changed blocks between two snapshots:
+ The response can include one or more empty `ChangedBlocks` arrays\. For example:
  + Snapshot 1 — full snapshot with 1000 blocks with block indexes `0` \- `999`\.
  + Snapshot 2 — incremental snapshot with only one changed block with block index `999`\.

  Listing the changed blocks for these snapshots with `StartingBlockIndex = 0` and `MaxResults = 100` returns an empty array of `ChangedBlocks`\. You must request the remaining results using `nextToken` until the changed block is returned in the tenth result set, which includes blocks with block indexes `900` \- `999`\.
+ The response can skip unwritten blocks in the snapshots\. For example:
  + Snapshot 1 — full snapshot with 1000 blocks with block indexes `2000` \- `2999`\.
  + Snapshot 2 — incremental snapshot with only one changed block with block index `2000`\.

  Listing the changed blocks for these snapshots with `StartingBlockIndex = 0` and `MaxResults = 100`, the response skips block indexes `0` \- `1999` and includes block index `2000`\. The response will not include empty `ChangedBlocks` arrays\.

------
#### [ AWS CLI ]

The following [list\-changed\-blocks](https://docs.aws.amazon.com/cli/latest/reference/ebs/list-changed-blocks.html) example command returns the block indexes and block tokens of blocks that are different between snapshots `snap-1234567890` and `snap-0987654321`\. The `--starting-block-index` parameter limits the results to block indexes greater than `0`, and the `--max-results` parameter limits the results to the first `500` blocks\.\.

```
aws ebs list-changed-blocks --first-snapshot-id snap-1234567890 --second-snapshot-id snap-0987654321 --starting-block-index 0 --max-results 500
```

The following example response for the previous command shows that block indexes 0, 6000, 6001, 6002, and 6003 are different between the two snapshots\. Additionally, block indexes 6001, 6002, and 6003 exist only in the first snapshot ID specified, and not in the second snapshot ID because there is no second block token listed in the response\.

Use the `get-snapshot-block` command and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\.

```
{
      "ChangedBlocks": [
          {
              "BlockIndex": 0,
              "FirstBlockToken": "AAABAVahm9SO60Dyi0ORySzn2ZjGjW/KN3uygGlS0QOYWesbzBbDnX2dGpmC",
              "SecondBlockToken": "AAABAf8o0o6UFi1rDbSZGIRaCEdDyBu9TlvtCQxxoKV8qrUPQP7vcM6iWGSr"
          },
          {
              "BlockIndex": 6000,
              "FirstBlockToken": "AAABAbYSiZvJ0/R9tz8suI8dSzecLjN4kkazK8inFXVintPkdaVFLfCMQsKe",
              "SecondBlockToken": "AAABAZnqTdzFmKRpsaMAsDxviVqEI/3jJzI2crq2eFDCgHmyNf777elD9oVR"
          },
          {
              "BlockIndex": 6001,
              "FirstBlockToken": "AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR"
          },
          {
              "BlockIndex": 6002,
              "FirstBlockToken": "AAABASqX4/NWjvNceoyMUljcRd0DnwbSwNnes1UkoP62CrQXvn47BY5435aw"
          },
          {
              "BlockIndex": 6003,
              "FirstBlockToken": "AAABASmJ0O5JxAOce25rF4P1sdRtyIDsX12tFEDunnePYUKOf4PBROuICb2A"
          },
          ...
      ],
      "ExpiryTime": 1576308931.973,
      "VolumeSize": 32212254720,
      "BlockSize": 524288,
      "NextToken": "AAADARqElNng/sV98CYk/bJDCXeLJmLJHnNSkHvLzVaO0zsPH/QM3Bi3zF//O6Mdi/BbJarBnp8h"
  }
```

------
#### [ AWS API ]

The following [ListChangedBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListChangedBlocks.html) example request returns the block indexes and block tokens of blocks that are different between snapshots `snap-0acEXAMPLEcf41648` and `snap-0c9EXAMPLE1b30e2f`\. The `startingBlockIndex` parameter limits the results to block indexes greater than `0`, and the `maxResults` parameter limits the results to the first `500` blocks\.

```
GET /snapshots/snap-0c9EXAMPLE1b30e2f/changedblocks?firstSnapshotId=snap-0acEXAMPLEcf41648&maxResults=500&startingBlockIndex=0 HTTP/1.1
  Host: ebs.us-east-2.amazonaws.com
  Accept-Encoding: identity
  User-Agent: <User agent parameter>
  X-Amz-Date: 20200617T232546Z
  Authorization: <Authentication parameter>
```

The following example response for the previous request shows that block indexes `0`, `3072`, `6002`, and `6003` are different between the two snapshots\. Additionally, block indexes `6002`, and `6003` exist only in the first snapshot ID specified, and not in the second snapshot ID because there is no second block token listed in the response\.

Use the `GetSnapshotBlock` action and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\. 

```
HTTP/1.1 200 OK
  x-amzn-RequestId: fb0f6743-6d81-4be8-afbe-db11a5bb8a1f
  Content-Type: application/json
  Content-Length: 1456
  Date: Wed, 17 Jun 2020 23:25:47 GMT
  Connection: keep-alive
  
  {
      "BlockSize": 524288,
      "ChangedBlocks": [
          {
              "BlockIndex": 0,
              "FirstBlockToken": "AAUBAVaWqOCnDNuKle11s7IIX6jp6FYcC/tJuVT1GgP23AuLntwiMdJ+OJkL",
              "SecondBlockToken": "AAUBASxzy0Y0b33JVRLoYm3NOresCxn5RO+HVFzXW3Y/RwfFaPX2Edx8QHCh"
          },
          {
              "BlockIndex": 3072,
              "FirstBlockToken": "AAUBAcHp6pC5fKAC7TokoNCtAnZhqq27u6fxRfZOLEmeXLmHBf2R/Yb24MaS",
              "SecondBlockToken": "AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid"
          },
          {
              "BlockIndex": 6002,
              "FirstBlockToken": "AAABASqX4/NWjvNceoyMUljcRd0DnwbSwNnes1UkoP62CrQXvn47BY5435aw"
          },
          {
              "BlockIndex": 6003,
              "FirstBlockToken": "AAABASmJ0O5JxAOce25rF4P1sdRtyIDsX12tFEDunnePYUKOf4PBROuICb2A"
          },
          ...
      ],
      "ExpiryTime": 1.592976647009E9,
      "VolumeSize": 3
  }
```

------

## Get block data from a snapshot<a name="get-block-data"></a>

------
#### [ AWS CLI ]

The following [get\-snapshot\-block](https://docs.aws.amazon.com/cli/latest/reference/ebs/get-snapshot-block.html) example command returns the data in the block index `6001` with block token `AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR`, in snapshot `snap-1234567890`\. The binary data is output to the `data` file in the `C:\Temp` directory on a Windows computer\. If you run the command on a Linux or Unix computer, replace the output path with `/tmp/data` to output the data to the `data` file in the `/tmp` directory\.

```
aws ebs get-snapshot-block --snapshot-id snap-1234567890 --block-index 6001 --block-token AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR C:/Temp/data
```

The following example response for the previous command shows the size of the data returned, the checksum to validate the data, and the algorithm of the checksum\. The binary data is automatically saved to the directory and file you specified in the request command\.

```
{
      "DataLength": "524288",
      "Checksum": "cf0Y6/Fn0oFa4VyjQPOa/iD0zhTflPTKzxGv2OKowXc=",
      "ChecksumAlgorithm": "SHA256"
  }
```

------
#### [ AWS API ]

The following [GetSnapshotBlock](https://docs.aws.amazon.com/ebs/latest/APIReference/API_GetSnapshotBlock.html) example request returns the data in the block index `3072` with block token `AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid`, in snapshot `snap-0c9EXAMPLE1b30e2f`\.

```
GET /snapshots/snap-0c9EXAMPLE1b30e2f/blocks/3072?blockToken=AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid HTTP/1.1
  Host: ebs.us-east-2.amazonaws.com
  Accept-Encoding: identity
  User-Agent: <User agent parameter>
  X-Amz-Date: 20200617T232838Z
  Authorization: <Authentication parameter>
```

The following example response for the previous request shows the size of the data returned, the checksum to validate the data, and the algorithm used to generate the checksum\. The binary data is transmitted in the body of the response and is represented as *BlockData* in the following example\.

```
HTTP/1.1 200 OK
  x-amzn-RequestId: 2d0db2fb-bd88-474d-a137-81c4e57d7b9f
  x-amz-Data-Length: 524288
  x-amz-Checksum: Vc0yY2j3qg8bUL9I6GQuI2orTudrQRBDMIhcy7bdEsw=
  x-amz-Checksum-Algorithm: SHA256
  Content-Type: application/octet-stream
  Content-Length: 524288
  Date: Wed, 17 Jun 2020 23:28:38 GMT
  Connection: keep-alive
  
  BlockData
```

------