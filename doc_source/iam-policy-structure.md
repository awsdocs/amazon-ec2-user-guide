# Policy Structure<a name="iam-policy-structure"></a>

The following topics explain the structure of an IAM policy\.

**Topics**
+ [Policy Syntax](#policy-syntax)
+ [Actions for Amazon EC2](#UsingWithEC2_Actions)
+ [Amazon Resource Names for Amazon EC2](#EC2_ARN_Format)
+ [Condition Keys for Amazon EC2](#amazon-ec2-keys)
+ [Checking That Users Have the Required Permissions](#check-required-permissions)

## Policy Syntax<a name="policy-syntax"></a>

An IAM policy is a JSON document that consists of one or more statements\. Each statement is structured as follows\.

```
{
  "Statement":[{
    "Effect":"effect",
    "Action":"action",
    "Resource":"arn",
    "Condition":{
      "condition":{
        "key":"value"
        }
      }
    }
  ]
}
```

There are various elements that make up a statement:
+ **Effect:** The *effect* can be `Allow` or `Deny`\. By default, IAM users don't have permission to use resources and API actions, so all requests are denied\. An explicit allow overrides the default\. An explicit deny overrides any allows\.
+ **Action**: The *action* is the specific API action for which you are granting or denying permission\. To learn about specifying *action*, see [Actions for Amazon EC2](#UsingWithEC2_Actions)\. 
+ **Resource**: The resource that's affected by the action\. Some Amazon EC2 API actions allow you to include specific resources in your policy that can be created or modified by the action\. To specify a resource in the statement, you need to use its Amazon Resource Name \(ARN\)\. For more information about specifying the ARN value, see [Amazon Resource Names for Amazon EC2](#EC2_ARN_Format)\. For more information about which API actions support which ARNs, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. If the API action does not support ARNs, use the \* wildcard to specify that all resources can be affected by the action\. 
+ **Condition**: Conditions are optional\. They can be used to control when your policy is in effect\. For more information about specifying conditions for Amazon EC2, see [Condition Keys for Amazon EC2](#amazon-ec2-keys)\.

For more information about example IAM policy statements for Amazon EC2, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\. 

## Actions for Amazon EC2<a name="UsingWithEC2_Actions"></a>

In an IAM policy statement, you can specify any API action from any service that supports IAM\. For Amazon EC2, use the following prefix with the name of the API action: `ec2:`\. For example: `ec2:RunInstances` and `ec2:CreateImage`\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": ["ec2:action1", "ec2:action2"]
```

You can also specify multiple actions using wildcards\. For example, you can specify all actions whose name begins with the word "Describe" as follows:

```
"Action": "ec2:Describe*"
```

To specify all Amazon EC2 API actions, use the \* wildcard as follows:

```
"Action": "ec2:*"
```

For a list of Amazon EC2 actions, see [Actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-apis.html) in the *Amazon EC2 API Reference*\.

## Amazon Resource Names for Amazon EC2<a name="EC2_ARN_Format"></a>

Each IAM policy statement applies to the resources that you specify using their ARNs\. 

**Important**  
Currently, not all API actions support individual ARNs\. We'll add support for additional API actions and ARNs for additional Amazon EC2 resources later\. For information about which ARNs you can use with which Amazon EC2 API actions, as well as supported condition keys for each ARN, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 

An ARN has the following general syntax:

```
arn:aws:[service]:[region]:[account]:resourceType/resourcePath
```

*service*  
The service \(for example, `ec2`\)\.

*region*  
The Region for the resource \(for example, `us-east-1`\)\.

*account*  
The AWS account ID, with no hyphens \(for example, `123456789012`\)\.

*resourceType*  
The type of resource \(for example, `instance`\)\.

*resourcePath*  
A path that identifies the resource\. You can use the \* wildcard in your paths\.

For example, you can indicate a specific instance \(`i-1234567890abcdef0`\) in your statement using its ARN as follows\. 

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:instance/i-1234567890abcdef0"
```

You can also specify all instances that belong to a specific account by using the \* wildcard as follows\.

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*"
```

To specify all resources, or if a specific API action does not support ARNs, use the \* wildcard in the `Resource` element as follows\.

```
"Resource": "*"
```

The following table describes the ARNs for each type of resource used by the Amazon EC2 API actions\. 


| Resource Type | ARN | 
| --- | --- | 
|  All Amazon EC2 resources  |  arn:aws:ec2:\*  | 
|  All Amazon EC2 resources owned by the specified account in the specified Region  |  arn:aws:ec2:*region*:*account*:\*  | 
|  Customer gateway  |  arn:aws:ec2:*region*:*account*:customer\-gateway/*cgw\-id* Where *cgw\-id* is cgw\-*xxxxxxxx*  | 
|  DHCP options set  |  arn:aws:ec2:*region*:*account*:dhcp\-options/*dhcp\-options\-id* Where *dhcp\-options\-id* is dopt\-*xxxxxxxx*  | 
|  Elastic GPU  |  arn:aws:ec2:*region*:*account*:elastic\-gpu/\*  | 
|  Image  |  arn:aws:ec2:*region*::image/*image\-id* Where *image\-id* is the ID of the AMI, AKI, or ARI, and *account* isn't used  | 
|  Instance  |  arn:aws:ec2:*region*:*account*:instance/*instance\-id* Where *instance\-id* is i\-*xxxxxxxx* or i\-*xxxxxxxxxxxxxxxxx*  | 
|  Instance profile  |  arn:aws:iam::*account*:instance\-profile/*instance\-profile\-name* Where *instance\-profile\-name* is the name of the instance profile, and *region* isn't used  | 
|  Internet gateway  |  arn:aws:ec2:*region*:*account*:internet\-gateway/*igw\-id* Where *igw\-id* is igw\-*xxxxxxxx*  | 
|  Key pair  |  arn:aws:ec2:*region*:*account*:key\-pair/*key\-pair\-name* Where *key\-pair\-name* is the key pair name \(for example, `gsg-keypair`\)  | 
|  Launch template  |  arn:aws:ec2:*region*:*account*:launch\-template/*launch\-template\-id* Where *launch\-template\-id* is lt\-*xxxxxxxxxxxxxxxxx*  | 
|  NAT gateway  |  arn:aws:ec2:*region*:*account*:natgateway/*natgateway\-id* Where *natgateway\-id* is nat\-*xxxxxxxxxxxxxxxxx*  | 
|  Network ACL  |  arn:aws:ec2:*region*:*account*:network\-acl/*nacl\-id* Where *nacl\-id* is acl\-*xxxxxxxx*  | 
|  Network interface  |  arn:aws:ec2:*region*:*account*:network\-interface/*eni\-id* Where *eni\-id* is eni\-*xxxxxxxx*  | 
|  Placement group  |  arn:aws:ec2:*region*:*account*:placement\-group/*placement\-group\-name* Where *placement\-group\-name* is the placement group name \(for example, `my-cluster`\)  | 
|  Reserved Instance  |  arn:aws:ec2:*region*:*account*:reserved\-instances/*reservation\-id* Where *reservation\-id* is *xxxxxxxx*\-*xxxx*\-*xxxx*\-*xxxx*\-*xxxxxxxxxxxx*  | 
|  Route table  |  arn:aws:ec2:*region*:*account*:route\-table/*route\-table\-id* Where *route\-table\-id* is rtb\-*xxxxxxxx*  | 
|  Security group  |  arn:aws:ec2:*region*:*account*:security\-group/*security\-group\-id* Where *security\-group\-id* is sg\-*xxxxxxxx*  | 
|  Snapshot  |  arn:aws:ec2:*region*::snapshot/*snapshot\-id* Where *snapshot\-id* is snap\-*xxxxxxxx* or snap\-*xxxxxxxxxxxxxxxxx*, and *account* isn't used  | 
|  Spot Instance request  |  arn:aws:ec2:*region*:*account*:spot\-instances\-request/*spot\-instance\-request\-id* Where *spot\-instance\-request\-id* is sir\-*xxxxxxxx*  | 
|  Subnet  |  arn:aws:ec2:*region*:*account*:subnet/*subnet\-id* Where *subnet\-id* is subnet\-*xxxxxxxx*  | 
|  Volume  |  arn:aws:ec2:*region*:*account*:volume/*volume\-id* Where *volume\-id* is vol\-*xxxxxxxx* or vol\-*xxxxxxxxxxxxxxxxx*  | 
|  VPC  |  arn:aws:ec2:*region*:*account*:vpc/*vpc\-id* Where *vpc\-id* is vpc\-*xxxxxxxx*  | 
|  VPC peering connection  |  arn:aws:ec2:*region*:*account*:vpc\-peering\-connection/*vpc\-peering\-connection\-id* Where *vpc\-peering connection\-id* is pcx\-*xxxxxxxx*  | 
|  VPN connection  |  arn:aws:ec2:*region*:*account*:vpn\-connection/*vpn\-connection\-id* Where *vpn\-connection\-id* is vpn\-*xxxxxxxx*  | 
|  VPN gateway  |  arn:aws:ec2:*region*:*account*:vpn\-gateway/*vpn\-gateway\-id* Where *vpn\-gateway\-id* is vgw\-*xxxxxxxx*  | 

Many Amazon EC2 API actions involve multiple resources\. For example, `AttachVolume` attaches an Amazon EBS volume to an instance, so an IAM user must have permissions to use the volume and the instance\. To specify multiple resources in a single statement, separate their ARNs with commas, as follows\.

```
"Resource": ["arn1", "arn2"]
```

For more general information about ARNs, see [Amazon Resource Names \(ARN\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\. For more information about the resources that are created or modified by the Amazon EC2 actions, and the ARNs that you can use in your IAM policy statements, see [Granting IAM Users Required Permissions for Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ec2-api-permissions.html) in the *Amazon EC2 API Reference*\.

## Condition Keys for Amazon EC2<a name="amazon-ec2-keys"></a>

In a policy statement, you can optionally specify conditions that control when it is in effect\. Each condition contains one or more key\-value pairs\. Condition keys are not case\-sensitive\. We've defined AWS\-wide condition keys, plus additional service\-specific condition keys\.

If you specify multiple conditions, or multiple keys in a single condition, we evaluate them using a logical AND operation\. If you specify a single condition with multiple values for one key, we evaluate the condition using a logical OR operation\. For permissions to be granted, all conditions must be met\.

You can also use placeholders when you specify conditions\. For example, you can grant an IAM user permission to use resources with a tag that specifies his or her IAM user name\. For more information, see [Policy Variables](https://docs.aws.amazon.com/IAM/latest/UserGuide/PolicyVariables.html) in the *IAM User Guide*\.

**Important**  
Many condition keys are specific to a resource, and some API actions use multiple resources\. If you write a policy with a condition key, use the `Resource` element of the statement to specify the resource to which the condition key applies\. If not, the policy may prevent users from performing the action at all, because the condition check fails for the resources to which the condition key does not apply\. If you do not want to specify a resource, or if you've written the `Action` element of your policy to include multiple API actions, then you must use the `...IfExists` condition type to ensure that the condition key is ignored for resources that do not use it\. For more information, see [\.\.\.IfExists Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_IfExists) in the *IAM User Guide*\.

Amazon EC2 implements the following service\-specific condition keys\. For information about which condition keys you can use with which Amazon EC2 resources, on an action\-by\-action basis, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 


| Condition Key | Key\-Value Pair | Evaluation Types | 
| --- | --- | --- | 
|  `ec2:AccepterVpc`  |  "ec2:AccepterVpc":"*vpc\-arn*" Where *vpc\-arn* is the VPC ARN for the accepter VPC in a VPC peering connection  |  ARN, Null  | 
|  `ec2:AuthorizedService`  |  "ec2:AuthorizedService":"*service\-principal*" Where *service\-principal* is the service principal \(for example, ecs\.amazonaws\.com\)  |  String, Null  | 
|  `ec2:AuthorizedUser`  |  "ec2:AuthorizedUser":"*principal\-arn*" Where *principal\-arn* is the ARN for the principal \(for example, arn:aws:iam::123456789012:root\)  |  ARN, Null  | 
|  `ec2:AvailabilityZone`  |  "ec2:AvailabilityZone":"*az\-api\-name*" Where *az\-api\-name* is the name of the Availability Zone \(for example, `us-east-2a`\) To list your Availability Zones, use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)  |  String, Null  | 
| ec2:CreateAction | "ec2:CreateAction":"api\-name"Where *api\-name* is the name of the resource\-creating action \(for example, `RunInstances`\) | String, Null | 
|  `ec2:EbsOptimized`  |  "ec2:EbsOptimized":"*optimized\-flag*" Where *optimized\-flag* is `true` \| `false` \(for an instance\)  |  Boolean, Null  | 
|  `ec2:ElasticGpuType`  |  "ec2:ElasticGpuType":"*elastic\-gpu\-type*" Where *elastic\-gpu\-type* is the name of the elastic GPU type  |  String, Null  | 
| ec2:Encrypted | "ec2:Encrypted":"encrypted\-flag"Where *encrypted\-flag* is `true` \| `false` \(for an EBS volume\) | Boolean, Null | 
|  `ec2:ImageType`  |  "ec2:ImageType":"*image\-type\-api\-name*" Where *image\-type\-api\-name* is `machine` \| `aki` \| `ari`  |  String, Null  | 
|  `ec2:InstanceMarketType`  |  "ec2:InstanceMarketType":"*market\-type*" Where *market\-type* is `spot` \| `on-demand`  |  String, Null  | 
|  `ec2:InstanceProfile`  |  "ec2:InstanceProfile":"*instance\-profile\-arn*" Where *instance\-profile\-arn* is the instance profile ARN  |  ARN, Null  | 
| `ec2:InstanceType`  |  "ec2:InstanceType":"*instance\-type\-api\-name*" Where *instance\-type\-api\-name* is the name of the instance type  |  String, Null  | 
|  `ec2:IsLaunchTemplateResource`  |  "ec2:IsLaunchTemplateResource":"*launch\-template\-resource\-flag*" Where *launch\-template\-resource\-flag* is `true` \| `false`  |  Boolean, Null  | 
|  `ec2:LaunchTemplate`  |  "ec2:LaunchTemplate":"*launch\-template\-arn*" Where *launch\-template\-arn* is the launch template ARN  |  ARN, Null  | 
|  `ec2:Owner`  |  "ec2:Owner":"*account\-id*" Where *account\-id* is `amazon` \| `aws-marketplace` \| *aws\-account\-id*  |  String, Null  | 
| `ec2:ParentSnapshot` |  "ec2:ParentSnapshot":"*snapshot\-arn*" Where *snapshot\-arn* is the snapshot ARN  |  ARN, Null  | 
|  `ec2:ParentVolume`  |  "ec2:ParentVolume":"*volume\-arn*" Where *volume\-arn* is the volume ARN  |  ARN, Null  | 
|  `ec2:Permission`  |  "ec2:Permission":"*permission*" Where *permission* is `INSTANCE-ATTACH` \| `EIP-ASSOCIATE`  |  String, Null  | 
|  `ec2:PlacementGroup`  |  "ec2:PlacementGroup":"*placement\-group\-arn*" Where *placement\-group\-arn* is the placement group ARN  |  ARN, Null  | 
| ec2:PlacementGroupStrategy |  "ec2:PlacementGroupStrategy":"*placement\-group\-strategy*" Where *placement\-group\-strategy* is `cluster` \| `spread`  | String, Null | 
|  `ec2:ProductCode`  |  "ec2:ProductCode":"*product\-code*" Where *product\-code* is the product code  |  String, Null  | 
| `ec2:Public` |  "ec2:Public":"*public\-flag*" Where *public\-flag* is `true` \| `false` \(for an AMI\)  |  Boolean, Null  | 
|  `ec2:Region`  |  "ec2:Region":"*region\-name*" Where *region\-name* is the name of the Region \(for example, `us-east-2`\)\. To list your Regions, use [describe\-regions](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-regions.html)\. This condition key can be used with all Amazon EC2 actions\.  |  String, Null  | 
| `ec2:RequesterVpc` |  "ec2:RequesterVpc":"*vpc\-arn*" Where *vpc\-arn* is the VPC ARN for the requester VPC in a VPC peering connection  |  ARN, Null  | 
|  `ec2:ReservedInstancesOfferingType`  |  "ec2:ReservedInstancesOfferingType":"*offering\-type* Where *offering\-type* is `No Upfront` \| `Partial Upfront` \| `All Upfront`  |  String, Null  | 
|  `ec2:ResourceTag/`*tag\-key*  |  "ec2:ResourceTag/*tag\-key*":"*tag\-value*" Where *tag\-key* and *tag\-value* are the tag\-key pair  |  String, Null  | 
|  `ec2:RootDeviceType`  |  "ec2:RootDeviceType":"*root\-device\-type\-name*" Where *root\-device\-type\-name* is `ebs` \| `instance-store`  |  String, Null  | 
|  `ec2:SnapshotTime`  |  "ec2:SnapshotTime":"*time*" Where *time* is the snapshot creation time \(for example, 2013\-06\-01T00:00:00Z\)  |  Date, Null  | 
|  `ec2:Subnet`  |  "ec2:Subnet":"*subnet\-arn*" Where *subnet\-arn* is the subnet ARN  |  ARN, Null  | 
|  `ec2:Tenancy`  |  "ec2:Tenancy":"*tenancy\-attribute*" Where *tenancy\-attribute* is `default` \| `dedicated` \| `host`  |  String, Null  | 
|  `ec2:VolumeIops`  |  "ec2:VolumeIops":"*volume\-iops*" Where *volume\-iops* is the input/output operations per second \(IOPS\)\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.  |  Numeric, Null  | 
|  `ec2:VolumeSize`  |  "ec2:VolumeSize":"*volume\-size*" Where *volume\-size* is the size of the volume, in GiB  |  Numeric, Null  | 
|  `ec2:VolumeType`  |  "ec2:VolumeType":"*volume\-type\-name*" Where *volume\-type\-name* is `gp2` for General Purpose SSD volumes, `io1` for Provisioned IOPS SSD volumes, `st1` for Throughput Optimized HDD volumes, `sc1` for Cold HDD volumes, or `standard` for Magnetic volumes\.  |  String, Null  | 
|  `ec2:Vpc`  |  "ec2:Vpc":"*vpc\-arn*" Where *vpc\-arn* is the VPC ARN  |  ARN, Null  | 

Amazon EC2 also implements the AWS\-wide condition keys\. For more information, see [Information Available in All Requests](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html#policy-vars-infoallreqs) in the *IAM User Guide*\. 

All Amazon EC2 actions support the `aws:RequestedRegion` and `ec2:Region` condition keys\. For more information, see [Example: Restricting Access to a Specific Region](ExamplePolicies_EC2.md#iam-example-region)\.

The `ec2:SourceInstanceARN` key can be used for conditions that specify the ARN of the instance from which a request is made\. This condition key is available AWS\-wide and is not service\-specific\. For policy examples, see [Allows an EC2 Instance to Attach or Detach Volumes](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_volumes-instance.html) and [Example: Allowing a Specific Instance to View Resources in Other AWS Services](ExamplePolicies_EC2.md#iam-example-source-instance)\. The `ec2:SourceInstanceARN` key cannot be used as a variable to populate the ARN for the `Resource` element in a statement\.

The following AWS condition keys were introduced for Amazon EC2 and are supported by a limited number of additional services\.


| Condition Key | Key/Value Pair | Evaluation Types | 
| --- | --- | --- | 
| `aws:RequestTag`/*tag\-key* | "aws:Request/*tag\-key*":"*tag\-value*" Where *tag\-key* and *tag\-value* are the tag key\-value pair | String, Null | 
| `aws:TagKeys` | "aws:TagKeys":"*tag\-key*" Where *tag\-key* is a list of tag keys \(for example, \["A","B"\]\) | String, Null | 

For example policy statements for Amazon EC2, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\.

## Checking That Users Have the Required Permissions<a name="check-required-permissions"></a>

After you've created an IAM policy, we recommend that you check whether it grants users the permissions to use the particular API actions and resources they need before you put the policy into production\.

First, create an IAM user for testing purposes, and then attach the IAM policy that you created to the test user\. Then, make a request as the test user\.

If the Amazon EC2 action that you are testing creates or modifies a resource, you should make the request using the `DryRun` parameter \(or run the AWS CLI command with the `--dry-run` option\)\. In this case, the call completes the authorization check, but does not complete the operation\. For example, you can check whether the user can terminate a particular instance without actually terminating it\. If the test user has the required permissions, the request returns `DryRunOperation`; otherwise, it returns `UnauthorizedOperation`\.

If the policy doesn't grant the user the permissions that you expected, or is overly permissive, you can adjust the policy as needed and retest until you get the desired results\. 

**Important**  
It can take several minutes for policy changes to propagate before they take effect\. Therefore, we recommend that you allow five minutes to pass before you test your policy updates\.

If an authorization check fails, the request returns an encoded message with diagnostic information\. You can decode the message using the `DecodeAuthorizationMessage` action\. For more information, see [DecodeAuthorizationMessage](https://docs.aws.amazon.com/STS/latest/APIReference/API_DecodeAuthorizationMessage.html) in the *AWS Security Token Service API Reference*, and [decode\-authorization\-message](https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html) in the *AWS CLI Command Reference*\.