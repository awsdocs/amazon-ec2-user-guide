# Modify instance metadata options for existing instances<a name="configuring-IMDS-existing-instances"></a>

You can modify the instance metadata options for existing instances\.

You can also create an IAM policy that prevents users from modifying the instance metadata options on existing instances\. To control which users can modify the instance metadata options, specify a policy that prevents all users other than users with a specified role to use the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\. For the example IAM policy, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

**Topics**
+ [Require the use of IMDSv2](#modify-require-IMDSv2)
+ [Restore the use of IMDSv1](#modify-restore-IMDSv1)
+ [Change the PUT response hop limit](#modify-PUT-response-hop-limit)
+ [Enable the IPv6 endpoint for your instance](#enable-ipv6-endpoint-for-existing-instances)
+ [Turn on access to instance metadata](#enable-instance-metadata-on-existing-instances)
+ [Turn off access to instance metadata](#disable-instance-metadata-on-existing-instances)

## Require the use of IMDSv2<a name="modify-require-IMDSv2"></a>

Use one of the following methods to modify the instance metadata options on an existing instance to require that IMDSv2 is used when requesting instance metadata\. When IMDSv2 is required, IMDSv1 cannot be used\.

------
#### [ Console ]

**To require the use of IMDSv2 on an existing instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\.

1. Choose **Actions**, **Instance settings**, **Modify instance metadata options**\.

1. In the **Modify instance metadata options** dialog box, do the following:

   1. For **Instance metadata service**, select **Enable**\.

   1. For **IMDSv2**, choose **Required**\.

   1. Choose **Save**\.

------
#### [ AWS CLI ]

**To require the use of IMDSv2 on an existing instance**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-tokens` parameter to `required`\. When you specify a value for `http-tokens`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-tokens required \
    --http-endpoint enabled
```

------

## Restore the use of IMDSv1<a name="modify-restore-IMDSv1"></a>

When IMDSv2 is required, IMDSv1 will not work when requesting instance metadata\. When IMDSv2 is optional, then both IMDSv2 and IMDSv1 will work\. Therefore, to restore IMDSv1, make IMDSv2 optional by using one of the following methods\.

------
#### [ Console ]

**To restore the use of IMDSv1 on an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\.

1. Choose **Actions**, **Instance settings**, **Modify instance metadata options**\.

1. In the **Modify instance metadata options** dialog box, do the following:

   1. For **Instance metadata service**, make sure that **Enable** is selected\.

   1. For **IMDSv2**, choose **Optional**\.

   1. Choose **Save**\.

------
#### [ AWS CLI ]

**To restore the use of IMDSv1 on an instance**  
You can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command with `http-tokens` set to `optional` to restore the use of IMDSv1 when requesting instance metadata\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-tokens optional \
    --http-endpoint enabled
```

------

## Change the PUT response hop limit<a name="modify-PUT-response-hop-limit"></a>

For existing instances, you can change the settings of the `PUT` response hop limit\.

Currently only the AWS CLI and AWS SDKs support changing the PUT response hop limit\.

**To change the PUT response hop limit**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-put-response-hop-limit` parameter to the required number of hops\. In the following example, the hop limit is set to `3`\. Note that when specifying a value for `http-put-response-hop-limit`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-put-response-hop-limit 3 \
    --http-endpoint enabled
```

## Enable the IPv6 endpoint for your instance<a name="enable-ipv6-endpoint-for-existing-instances"></a>

By default, the IPv6 endpoint is disabled\. This is true even if you have launched an instance into an IPv6\-only subnet\. The IPv6 endpoint for the IMDS is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

Currently only the AWS CLI and AWS SDKs support enabling the IPv6 endpoint for your instance\.

**To enable the IPv6 endpoint for your instance**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-protocol-ipv6` parameter to `enabled`\. Note that when specifying a value for `http-protocol-ipv6`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
	--instance-id i-1234567898abcdef0 \
	--http-protocol-ipv6 enabled \
	--http-endpoint enabled
```

## Turn on access to instance metadata<a name="enable-instance-metadata-on-existing-instances"></a>

You can turn on access to instance metadata by enabling the HTTP endpoint of the IMDS on your instance, regardless of which version of the IMDS you are using\. You can reverse this change at any time by disabling the HTTP endpoint\.

Use one of the following methods to turn on access to instance metadata on an instance\.

------
#### [ Console ]

**To turn on access to instance metadata**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\.

1. Choose **Actions**, **Instance settings**, **Modify instance metadata options**\.

1. In the **Modify instance metadata options** dialog box, do the following:

   1. For **Instance metadata service**, select **Enable**\.

   1. Choose **Save**\.

------
#### [ AWS CLI ]

**To turn on access to instance metadata**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-endpoint enabled
```

------

## Turn off access to instance metadata<a name="disable-instance-metadata-on-existing-instances"></a>

You can turn off access to instance metadata by disabling the HTTP endpoint of the IMDS on your instance, regardless of which version of the IMDS you are using\. You can reverse this change at any time by enabling the HTTP endpoint\.

Use one of the following methods to turn off access to instance metadata on an instance\.

------
#### [ Console ]

**To turn off access to instance metadata**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\.

1. Choose **Actions**, **Instance settings**, **Modify instance metadata options**\.

1. In the **Modify instance metadata options** dialog box, do the following:

   1. For **Instance metadata service**, clear **Enable**\.

   1. Choose **Save**\.

------
#### [ AWS CLI ]

**To turn off access to instance metadata**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `disabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-endpoint disabled
```

------