# Paid AMIs<a name="paid-amis"></a>

A *paid AMI* is an AMI that you can purchase from a developer\.

Amazon EC2 integrates with AWS Marketplace, enabling developers to charge other Amazon EC2 users for the use of their AMIs or to provide support for instances\. 

The AWS Marketplace is an online store where you can buy software that runs on AWS, including AMIs that you can use to launch your EC2 instance\. The AWS Marketplace AMIs are organized into categories, such as Developer Tools, to enable you to find products to suit your requirements\. For more information about AWS Marketplace, see the [AWS Marketplace](https://aws.amazon.com/marketplace) site\.

Launching an instance from a paid AMI is the same as launching an instance from any other AMI\. No additional parameters are required\. The instance is charged according to the rates set by the owner of the AMI, as well as the standard usage fees for the related web services, for example, the hourly rate for running an m1\.small instance type in Amazon EC2\. Additional taxes might also apply\. The owner of the paid AMI can confirm whether a specific instance was launched using that paid AMI\. 

**Important**  
Amazon DevPay is no longer accepting new sellers or products\. AWS Marketplace is now the single, unified e\-commerce platform for selling software and services through AWS\. For information about how to deploy and sell software from AWS Marketplace, see [Selling on AWS Marketplace](https://aws.amazon.com/marketplace/help/200899830)\. AWS Marketplace supports AMIs backed by Amazon EBS\.

**Topics**
+ [Selling your AMI](#selling-your-ami)
+ [Finding a paid AMI](#using-paid-amis-finding-paid-ami)
+ [Purchasing a paid AMI](#using-paid-amis-purchasing-paid-ami)
+ [Getting the product code for your instance](#get-product-code)
+ [Using paid support](#using-paid-amis-support)
+ [Bills for paid and supported AMIs](#using-paid-amis-bills)
+ [Managing your AWS Marketplace subscriptions](#marketplace-manage-subscriptions)

## Selling your AMI<a name="selling-your-ami"></a>

You can sell your AMI using AWS Marketplace\. AWS Marketplace offers an organized shopping experience\. Additionally, AWS Marketplace also supports AWS features such as Amazon EBS\-backed AMIs, Reserved Instances, and Spot Instances\.

For information about how to sell your AMI on AWS Marketplace, see [Selling on AWS Marketplace](https://aws.amazon.com/marketplace/help/200899830/)\. 

## Finding a paid AMI<a name="using-paid-amis-finding-paid-ami"></a>

There are several ways that you can find AMIs that are available for you to purchase\. For example, you can use [AWS Marketplace](https://aws.amazon.com/marketplace), the Amazon EC2 console, or the command line\. Alternatively, a developer might let you know about a paid AMI themselves\.

### Finding a paid AMI using the console<a name="paid-ami-console"></a>

**To find a paid AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. 

1. Choose **Public images** for the first filter\.

1. In the Search bar, choose **Owner**, then **AWS Marketplace**\.

1. If you know the product code, choose **Product Code**, then type the product code\.

### Finding a paid AMI using AWS Marketplace<a name="finding-an-ami-mkt"></a>

**To find a paid AMI using AWS Marketplace**

1. Open [AWS Marketplace](https://aws.amazon.com/marketplace)\.

1. Enter the name of the operating system in the search box, and click **Go**\.

1. To scope the results further, use one of the categories or filters\.

1. Each product is labeled with its product type: either `AMI` or `Software as a Service`\.

### Finding a paid AMI using the AWS CLI<a name="finding-paid-windows-ami-cli"></a>

You can find a paid AMI using the following [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command \(AWS CLI\)\.

```
aws ec2 describe-images 
    --owners aws-marketplace
```

This command returns numerous details that describe each AMI, including the product code for a paid AMI\. The output from `describe-images` includes an entry for the product code like the following:

```
"ProductCodes": [
    {
        "ProductCodeId": "product_code",
        "ProductCodeType": "marketplace"
    }
],
```

If you know the product code, you can filter the results by product code\. This example returns the most recent AMI with the specified product code\.

```
aws ec2 describe-images 
    --owners aws-marketplace \
    --filters "Name=product-code,Values=product_code" \
    --query "sort_by(Images, &CreationDate)[-1].[ImageId]"
```

## Purchasing a paid AMI<a name="using-paid-amis-purchasing-paid-ami"></a>

You must sign up for \(purchase\) a paid AMI before you can launch an instance using the AMI\.

Typically a seller of a paid AMI presents you with information about the AMI, including its price and a link where you can buy it\. When you click the link, you're first asked to log into AWS, and then you can purchase the AMI\.

### Purchasing a paid AMI using the console<a name="purchase-paid-ami-console"></a>

You can purchase a paid AMI by using the Amazon EC2 launch wizard\. For more information, see [Launching an AWS Marketplace instance](launch-marketplace-console.md)\.

### Subscribing to a product using AWS Marketplace<a name="subscribe-to-paid-ami"></a>

To use the AWS Marketplace, you must have an AWS account\. To launch instances from AWS Marketplace products, you must be signed up to use the Amazon EC2 service, and you must be subscribed to the product from which to launch the instance\. There are two ways to subscribe to products in the AWS Marketplace:
+ **AWS Marketplace website**: You can launch preconfigured software quickly with the 1\-Click deployment feature\.
+ **Amazon EC2 launch wizard**: You can search for an AMI and launch an instance directly from the wizard\. For more information, see [Launching an AWS Marketplace instance](launch-marketplace-console.md)\.

## Getting the product code for your instance<a name="get-product-code"></a>

You can retrieve the AWS Marketplace product code for your instance using its instance metadata\. For more information about retrieving metadata, see [Instance metadata and user data](ec2-instance-metadata.md)\.

To retrieve a product code, use the following command:

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/product-codes
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/product-codes
```

------

If the instance has a product code, Amazon EC2 returns it\.

## Using paid support<a name="using-paid-amis-support"></a>

Amazon EC2 also enables developers to offer support for software \(or derived AMIs\)\. Developers can create support products that you can sign up to use\. During sign\-up for the support product, the developer gives you a product code, which you must then associate with your own AMI\. This enables the developer to confirm that your instance is eligible for support\. It also ensures that when you run instances of the product, you are charged according to the terms for the product specified by the developer\. 

**Important**  
You can't use a support product with Reserved Instances\. You always pay the price that's specified by the seller of the support product\.

To associate a product code with your AMI, use one of the following commands, where *ami\_id* is the ID of the AMI and *product\_code* is the product code:
+ [modify\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) \(AWS CLI\)

  ```
  aws ec2 modify-image-attribute --image-id ami_id --product-codes "product_code"
  ```
+ [Edit\-EC2ImageAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2ImageAttribute.html) \(AWS Tools for Windows PowerShell\)

  ```
  PS C:\> Edit-EC2ImageAttribute -ImageId ami_id -ProductCode product_code
  ```

After you set the product code attribute, it cannot be changed or removed\.

## Bills for paid and supported AMIs<a name="using-paid-amis-bills"></a>

At the end of each month, you receive an email with the amount your credit card has been charged for using any paid or supported AMIs during the month\. This bill is separate from your regular Amazon EC2 bill\. For more information, see [Paying For AWS Marketplace Products](https://aws.amazon.com/marketplace/help/200799490)\. 

## Managing your AWS Marketplace subscriptions<a name="marketplace-manage-subscriptions"></a>

On the AWS Marketplace website, you can check your subscription details, view the vendor's usage instructions, manage your subscriptions, and more\.

**To check your subscription details**

1.  Log in to the [AWS Marketplace](https://aws.amazon.com/marketplace)\.

1. Choose **Your Marketplace Account**\.

1. Choose **Manage your software subscriptions**\. 

1. All your current subscriptions are listed\. Choose ** Usage Instructions** to view specific instructions for using the product, for example, a user name for connecting to your running instance\. 

**To cancel an AWS Marketplace subscription**

1. Ensure that you have terminated any instances running from the subscription\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Instances**\.

   1. Select the instance, and choose **Actions**, **Instance State**, **Terminate**\.

   1. Choose **Yes, Terminate** when prompted for confirmation\.

1. Log in to the [AWS Marketplace](https://aws.amazon.com/marketplace), and choose **Your Marketplace Account**, then **Manage your software subscriptions**\.

1. Choose **Cancel subscription**\. You are prompted to confirm your cancellation\. 
**Note**  
After you've canceled your subscription, you are no longer able to launch any instances from that AMI\. To use that AMI again, you need to resubscribe to it, either on the AWS Marketplace website, or through the launch wizard in the Amazon EC2 console\.