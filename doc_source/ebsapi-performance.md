# Optimize performance<a name="ebsapi-performance"></a>

You can run API requests concurrently\. Assuming PutSnapshotBlock latency is 100ms, then a thread can process 10 requests in one second\. Furthermore, assuming your client application creates multiple threads and connections \(for example, 100 connections\), it can make 1000 \(10 \* 100\) requests per second in total\. This will correspond to a throughput of around 500 MB per second\.

The following list contains few things to look for in your application:
+ Is each thread using a separate connection? If the connections are limited on the application then multiple threads will wait for the connection to be available and you will notice lower throughput\.
+ Is there any wait time in the application between two put requests? This will reduce the effective throughput of a thread\.
+ The bandwidth limit on the instance – If bandwidth on the instance is shared by other applications, it could limit the available throughput for PutSnapshotBlock requests\.

Be sure to take note of other workloads that might be running in the account to avoid bottlenecks\. You should also build retry mechanisms into your EBS direct APIs workflows to handle throttling, timeouts, and service unavailability\.

Review the EBS direct APIs service quotas to determine the maximum API requests that you can run per second\. For more information, see [Amazon Elastic Block Store Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#w542aab9d130b7c15) in the *AWS General Reference*\.