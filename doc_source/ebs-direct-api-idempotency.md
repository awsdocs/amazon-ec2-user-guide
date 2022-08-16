# Idempotency for StartSnapshot API<a name="ebs-direct-api-idempotency"></a>

Idempotency ensures that an API request completes only once\. With an idempotent request, if the original request completes successful, the subsequent retries return the result from the original successful request and they have no additional effect\.

The [ StartSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_StartSnapshot.html) API supports idempotency using a *client token*\. A client token is a unique string that you specify when you make an API request\. If you retry an API request with the same client token and the same request parameters after it has completed successfully, the result of the original request is returned\. If you retry a request with the same client token, but change one or more of the request parameters, the `ConflictException` error is returned\.

If you do not specify your own client token, the AWS SDKs automatically generates a client token for the request to ensure that it is idempotent\.

A client token can be any string that includes up to 64 ASCII characters\. You should not reuse the same client tokens for different requests\.

**To make an idempotent StartSnapshot request with your own client token using the API**  
Specify the `ClientToken` request parameter\.

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

**To make an idempotent StartSnapshot request with your own client token using the AWS CLI**  
Specify the `client-token` request parameter\.

```
$ aws ebs start-snapshot --region us-east-2 --volume-size 8 --parent-snapshot snap-123EXAMPLE1234567 --timeout 60 --client-token 550e8400-e29b-41d4-a716-446655440000
```