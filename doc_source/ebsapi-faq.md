# Frequently asked questions<a name="ebsapi-faq"></a>

**Can a snapshot be accessed using the EBS direct APIs if it has a pending status?**  
No\. The snapshot can be accessed only if it has a completed status\.

**Are the block indexes returned by the EBS direct APIs in numerical order?**  
Yes\. The block indexes returned are unique, and in numerical order\.

**Can I submit a request with a MaxResults parameter value of under 100?**  
No\. The minimum MaxResult parameter value you can use is 100\. If you submit a request with a MaxResult parameter value of under 100, and there are more than 100 blocks in the snapshot, then the API will return at least 100 results\.

**Can I run API requests concurrently?**  
You can run API requests concurrently\. Be sure to take note of other workloads that might be running in the account to avoid bottlenecks\. You should also build retry mechanisms into your EBS direct APIs workflows to handle throttling, timeouts, and service unavailability\. For more information, see [Optimize performance](ebsapi-performance.md)\.  
Review the EBS direct APIs service quotas to determine the API requests that you can run per second\. For more information, see [Amazon Elastic Block Store Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#w542aab9d130b7c15) in the *AWS General Reference*\.

**When running the ListChangedBlocks action, is it possible to get an empty response even though there are blocks in the snapshot?**  
Yes\. If the changed blocks are scarce in the snapshot, the response may be empty but the API will return a next page token value\. Use the next page token value to continue to the next page of results\. You can confirm that you have reached the last page of results when the API returns a next page token value of null\.

**If the NextToken parameter is specified together with a StartingBlockIndex parameter, which of the two is used?**  
The NextToken is used, and the StartingBlockIndex is ignored\.

**How long are the block tokens and next tokens valid?**  
Block tokens are valid for seven days, and next tokens are valid for 60 minutes\.

**Are encrypted snapshots supported?**  
Yes\. Encrypted snapshots can be accessed using the EBS direct APIs\.  
To access an encrypted snapshot, the user must have access to the KMS key used to encrypt the snapshot, and the AWS KMS decrypt action\. See the [IAM permissions for EBS direct APIs](ebsapi-permissions.md) section earlier in this guide for the AWS KMS policy to assign to a user\.

**Are public snapshots supported?**  
Public snapshots are not supported\.

**Are Amazon EBS local snapshots on Outposts supported?**  
Amazon EBS local snapshots on Outposts are not supported\.

**Does list snapshot block return all block indexes and block tokens in a snapshot, or only those that have data written to them?**  
It returns only block indexes and tokens that have data written to them\.

**Can I get a history of the API calls made by the EBS direct APIs on my account for security analysis and operational troubleshooting purposes?**  
Yes\. To receive a history of EBS direct APIs API calls made on your account, turn on AWS CloudTrail in the AWS Management Console\. For more information, see [Log API Calls for EBS direct APIs with AWS CloudTrail](logging-ebs-apis-using-cloudtrail.md)\.