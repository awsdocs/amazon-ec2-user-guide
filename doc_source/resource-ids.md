# Resource IDs<a name="resource-ids"></a>

When resources are created, we assign each resource a unique resource ID\. A resource ID takes the form of a resource identifier \(such as `snap` for a snapshot\) followed by a hyphen and a unique combination of letters and numbers\.

Each resource identifier, such as an AMI ID, instance ID, EBS volume ID, or EBS snapshot ID, is tied to its Region and can be used only in the Region where you created the resource\.

You can use resource IDs to find your resources in the Amazon EC2 console\. If you are using a command line tool or the Amazon EC2 API to work with Amazon EC2, resource IDs are required for certain commands\. For example, if you are using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) AWS CLI command to stop an instance, you must specify the instance ID in the command\.

**Resource ID length**  
Prior to January 2016, the IDs assigned to newly created resources of certain resource types used 8 characters after the hyphen \(for example, i\-1a2b3c4d\)\. From January 2016 to June 2018, we changed the IDs of these resource types to use 17 characters after the hyphen \(for example, `i-1234567890abcdef0`\)\. Depending on when your account was created, you might have some existing resources with short IDs, however, any new resources will receive the longer IDs\.