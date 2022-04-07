# Error retries<a name="error-retries"></a>

The **AWS SDKs** implement automatic retry logic for requests that return error responses\. You can configure the retry settings for the AWS SDKs\. For more information, refer to the documentation for the SDK that you are using\.

The **AWS CLI** can be configured to automatically retry some failed requests\. For more information about configuring retries for the AWS CLI, see [AWS CLI retries](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-retries.html) in the *AWS Command Line Interface User Guide*\.

The **AWS Query API** does not support retry logic for failed requests\. If you are using HTTP or HTTPS requests, you must implement retry logic in your client application\.

For more information, see [ Error retries and exponential backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html) in the *AWS General Reference*\.

Regardless of whether you're using the AWS SDKs, AWS CLI, or AWS Query API, you should ensure that your client application always rerties failed requests that receive server \(`5xx`\) error responses and the following client \(`4xx`\) error responses:


| Error code | Description | HTTP status code | Thrown by | 
| --- | --- | --- | --- | 
| ThrottlingException | The number of API requests has exceeded the maximum allowed API request throttling limit for the account\. | 400 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/error-retries.html)  | 
| RequestThrottledException | The number of API requests has exceeded the maximum allowed API request throttling limit for the snapshot\. | 400 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/error-retries.html)  | 