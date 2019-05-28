# Setting Up to Launch Amazon EC2 with Amazon EI<a name="setting-up-ei"></a>

To launch an instance and associate it with an Amazon EI accelerator, you must first configure your security groups and AWS PrivateLink endpoint services\. Then, you must configure an instance role with the Amazon EI policy\. 

**Topics**
+ [Configuring AWS PrivateLink Endpoint Services](#eia-privatelink)
+ [Configuring Your Security Groups for Amazon EI](#ei-security)
+ [Configuring an Instance Role with an Amazon EI Policy](#ei-role-policy)
+ [Launching an Instance with Amazon EI](#eia-launch)

## Configuring AWS PrivateLink Endpoint Services<a name="eia-privatelink"></a>

Amazon EI uses [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html) to privately connect the instance in your VPC with their associated Amazon EI accelerator\. You must create a VPC endpoint for Amazon EI before you launch instances with accelerators\. This needs to be done just one time per VPC\. For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html)\.

**To configure an AWS PrivateLink endpoint service \(console\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Find service by name**\.

1. For **Service Name**, select **com\.amazonaws\.*<your\-region>*\.elastic\-inference\.runtime**\. 

   For example, for the us\-west\-2 region, select **com\.amazonaws\.us\-west\-2\.elastic\-inference\.runtime**\.

1. For **Subnets**, select one or more Availability Zones where the endpoint should be created\. Where you plan to launch instances with accelerators, you must select subnets for the Availability Zone\. 

1. Enable the private DNS name and enter the security group for your endpoint\. Choose **Create endpoint**\. Note the VPC endpoint ID for later\. 

1. The security group for the endpoint must allow inbound traffic to port 443\. 

**To configure an AWS PrivateLink endpoint service \(AWS CLI\)**  
Use the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command, specifying the VPC ID, type of VPC endpoint \(interface\), service name, and subnets to use, and the security groups to associate with the endpoint network interfaces\. For information about how to set up a security group for your VPC endpoint, see [Configuring Your Security Groups for Amazon EI](#ei-security)\.

```
aws ec2 create-vpc-endpoint --vpc-id vpc-1234567890abcdef0 --vpc-endpoint-type Interface --service-name com.amazonaws.us-west-2.elastic-inference.runtime --subnet-id subnet-1234567890abcdef0 --security-group-id sg-1234567890abcdef0
```

## Configuring Your Security Groups for Amazon EI<a name="ei-security"></a>

You need two security groups: one for inbound and outbound traffic for the new Amazon EI VPC endpoint and another for outbound traffic for the associated EC2 instances that you launch\.

### Configure Your Security Groups for Amazon EI<a name="ei-security-group"></a>

**To configure a security group for an Amazon EI accelerator \(console\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Security**, **Security Groups**\.

1. Choose **Create security group**\.

1. Under **Create Security Group**, specify a name and description for the security group and choose the ID of the VPC\. Choose **Create** and then choose **Close**\.

1. Select the check box next to your security group and choose **Actions**, **Edit inbound rules**\. Add a rule to allow HTTPS traffic on port 443 as follows:

   1. Choose **Add Rule**\.

   1. For **Type**, select **HTTPS**\.

   1. For **Source**, specify a CIDR block \(for example, 0\.0\.0\.0/0\) or the security group for your instance\.

   1. Choose **Save rules** and then choose **Close**\.

1. Choose **Actions**, **Edit outbound rules**\. Add a rule to allow HTTPS traffic on port 443 as follows:

   1. Choose **Add Rule**\.

   1. For **Type**, select **HTTPS**\.

   1. For **Source**, specify a CIDR block \(for example, 0\.0\.0\.0/0\) or the security group for your VPC endpoint\.

   1. Choose **Save rules** and then choose **Close**\.

**To configure a security group for an Amazon EI accelerator \(AWS CLI\)**

1. Create a security group using the [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) command:

   ```
   aws ec2 create-security-group --group-name my-security-group \
   --description My security group \
   --vpc-id vpc-1234567890abcdef0
   ```

1. Create an inbound rule using the [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) command:

   ```
   aws ec2 authorize-security-group-ingress --group-id sg-1234567890abcdef0 --protocol tcp --port 443 --cidr 0.0.0.0/0
   ```

1. Create an outbound rule using the [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) command:

   ```
   aws ec2 authorize-security-group-egress --group-id sg-1234567890abcdef0 --protocol tcp --port 443 --cidr 0.0.0.0/0
   ```

## Configuring an Instance Role with an Amazon EI Policy<a name="ei-role-policy"></a>

To launch an instance with an Amazon EI accelerator, you must provide an [IAM role](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) that allows actions on Amazon EI accelerators\. 

**To configure an instance role with an Amazon EI policy \(console\)**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Policies**, **Create Policy**\. 

1. Choose **JSON** and paste the following policy:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                  "elastic-inference:Connect",
                  "iam:List*",
                  "iam:Get*",
                  "ec2:Describe*",
                  "ec2:Get*"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   You may get a warning message about the elastic\-inference service not being recognizable\. This is a known issue and does not block creation of the policy\.

1. Choose **Review policy** and enter a name for the policy, such as `ec2-role-trust-policy.json`, and a description\.

1. Choose **Create policy**\.

1. In the left navigation pane, choose **Roles**, **Create role**\.

1. Choose **AWS service**, **EC2**, **Next: Permissions**\.

1. Select the name of the policy that you just created \(`ec2-role-trust-policy.json`\)\. Choose **Next: Tags**\.

1. Provide a role name and choose **Create Role**\.

When you create your instance, select the role under **Configure Instance Details** in the launch wizard\.

**To configure an instance role with an Amazon EI policy \(AWS CLI\)**  
To configure an instance role with an Amazon EI policy, follow the steps in [Creating an IAM Role](iam-roles-for-amazon-ec2.md#create-iam-role)\. Add the following policy to your instance:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
              "elastic-inference:Connect",
              "iam:List*",
              "iam:Get*",
              "ec2:Describe*",
              "ec2:Get*"
            ],
            "Resource": "*"
        }
    ]
}
```

You may get a warning message about the elastic\-inference service not being recognizable\. This is a known issue and does not block creation of the policy\.

## Launching an Instance with Amazon EI<a name="eia-launch"></a>

You can now configure EC2 instances with accelerators to launch within your subnet\. You can choose any supported Amazon EC2 instance type and Amazon EI accelerator size\. Amazon EI accelerators are available to all current generation instance types\. There are three Amazon EI accelerator sizes to choose from: 
+ `eia1.medium` with 1 GB of accelerator memory
+ `eia1.large` with 2 GB of accelerator memory
+ `eia1.xlarge` with 4 GB of accelerator memory

**To launch an instance with Amazon EI \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\. 

1. Under **Choose an Amazon Machine Image**, select an Amazon Linux or Ubuntu AMI\. We recommend one of the Deep Learning AMIs\. 

1. Under **Choose an Instance Type**, select the hardware configuration of your instance\. 

1. Choose **Next: Configure Instance Details**\.

1. Under **Configure Instance Details**, check the configuration settings\. Ensure that you are using the VPC with the security groups for the instance and the Amazon EI accelerator that you set up earlier\. For more information, see [Configuring Your Security Groups for Amazon EI](#ei-security)\.

1. For **IAM role**, select the role that you created in the [Configuring an Instance Role with an Amazon EI Policy](#ei-role-policy) procedure\.

1. Select **Add an Amazon EI accelerator**\.

1. Select the size of the Amazon EI accelerator\. Your options are: `eia1.medium`, `eia1.large`, and `eia1.xlarge`\. 

1. \(Optional\) You can choose to add storage and tags by choosing **Next** at the bottom of the page\. Or, you can let the instance wizard complete the remaining configuration steps for you\. 

1. Review the configuration of your instance and choose **Launch**\. 

1. You are prompted to choose an existing key pair for your instance or to create a new key pair\. For more information, see [Amazon EC2 Key Pairs\.](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
**Warning**  
Don’t select the **Proceed without a key pair** option\. If you launch your instance without a key pair, then you can’t connect to it\. 

1. After making your key pair selection, choose **Launch Instances**\. 

1. A confirmation page lets you know that your instance is launching\. To close the confirmation page and return to the console, choose **View Instances**\. 

1. Under **Instances**, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running`\.

1. It can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column\. 

**To launch an instance with Amazon EI \(AWS CLI\)**

To launch an instance with Amazon EI at the command line, you need your key pair name, subnet ID, security group ID, AMI ID, and the name of the instance profile that you created in the section [Configuring an Instance Role with an Amazon EI Policy](#ei-role-policy)\. For the security group ID, use the one you created for your instance that contains the AWS PrivateLink endpoint\. For more information, see [Configuring Your Security Groups for Amazon EI](#ei-security)\)\. For more information about the AMI ID, see [Finding a Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/find-an-ami.html)\.

1. Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch your instance and accelerator:

   ```
   aws ec2 run-instances --image-id ami-1234567890abcdef0 --instance-type m5.large --subnet-id subnet-1234567890abcdef0 --elastic-inference-accelerator Type=eia1.large --key-name my-key-pair --security-group-ids sg-1234567890abcdef0 --iam-instance-profile Name="accelerator profile name"
   ```

1. When the `run-instances` operation succeeds, your output is similar to the following\. The ElasticInferenceAcceleratorArn identifies the Amazon EI accelerator\.

   ```
   "ElasticInferenceAcceleratorAssociations": [
    {
      "ElasticInferenceAcceleratorArn": "arn:aws:elastic-inference:us-west-2:204044812891:elastic-inference-accelerator/eia-3e1de7c2f64a4de8b970c205e838af6b",
      "ElasticInferenceAcceleratorAssociationId": "eia-assoc-031f6f53ddcd5f260",
      "ElasticInferenceAcceleratorAssociationState": "associating",
      "ElasticInferenceAcceleratorAssociationTime": "2018-10-05T17:22:20.000Z"
    }
   ],
   ```

You are now ready to run your models using either TensorFlow or MXNet on the provided AMI\.