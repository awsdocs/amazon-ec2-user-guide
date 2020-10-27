# Resource IDs<a name="resource-ids"></a>

When resources are created, we assign each resource a unique resource ID\. A resource ID takes the form of a resource identifier \(such as `snap` for a snapshot\) followed by a hyphen and a unique combination of letters and numbers\.

You can use resource IDs to find your resources in the Amazon EC2 console\. If you are using a command line tool or the Amazon EC2 API to work with Amazon EC2, resource IDs are required for certain commands\. For example, if you are using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) AWS CLI command to stop an instance, you must specify the instance ID in the command\.

**Resource ID length**

Prior to January 2016, the IDs assigned to newly created resources of certain resource types used 8 characters after the hyphen \(for example, i\-1a2b3c4d\)\. From January 2016 to June 2018, we changed the IDs of these resource types to use 17 characters after the hyphen \(for example, `i-1234567890abcdef0`\)\. Depending on when your account was created, you might have resources of the following resource types with short IDs, though any new resources of these types receive the longer IDs:
+ bundle
+ conversion\-task
+ customer\-gateway
+ dhcp\-options
+ elastic\-ip\-allocation
+ elastic\-ip\-association
+ export\-task
+ flow\-log
+ image
+ import\-task
+ instance
+ internet\-gateway
+ network\-acl
+ network\-acl\-association
+ network\-interface
+ network\-interface\-attachment
+ prefix\-list
+ route\-table
+ route\-table\-association
+ security\-group
+ snapshot
+ subnet
+ subnet\-cidr\-block\-association
+ reservation
+ volume
+ vpc
+ vpc\-cidr\-block\-association
+ vpc\-endpoint
+ vpc\-peering\-connection
+ vpn\-connection
+ vpn\-gateway