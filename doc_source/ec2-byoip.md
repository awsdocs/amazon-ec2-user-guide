# Bring your own IP addresses \(BYOIP\) in Amazon EC2<a name="ec2-byoip"></a>

You can bring part or all of your public IPv4 address range or IPv6 address range from your on\-premises network to your AWS account\. You continue to own the address range, but AWS advertises it on the internet by default\. After you bring the address range to AWS, it appears in your account as an address pool\.

BYOIP is not available in all Regions and for all resources\. For a list of supported Regions and resources, see the [FAQ for Bring Your Own IP](https://aws.amazon.com/vpc/faqs/#Bring_Your_Own_IP)\.

**Note**  
The following steps describe how to bring your own IP address range for use in Amazon EC2 only\. For steps to bring your own IP address range for use in AWS Global Accelerator, see [Bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/global-accelerator/latest/dg/using-byoip.html) in the *AWS Global Accelerator Developer Guide*\.

**Topics**
+ [Requirements](#byoip-requirements)
+ [Prepare to bring your address range to your AWS account](#prepare-for-byoip)
+ [Provision the address range for use with AWS](#byoip-provision)
+ [Advertise the address range through AWS](#byoip-advertise)
+ [Work with your address range](#byoip-working-with)
+ [Deprovision the address range](#byoip-deprovision)

## Requirements<a name="byoip-requirements"></a>
+ The address range must be registered with your Regional internet registry \(RIR\), such as the American Registry for Internet Numbers \(ARIN\), Réseaux IP Européens Network Coordination Centre \(RIPE\), or Asia\-Pacific Network Information Centre \(APNIC\)\. It must be registered to a business or institutional entity and cannot be registered to an individual person\.
+ The most specific IPv4 address range that you can bring is /24\.
+ The most specific IPv6 address range that you can bring is /48 for CIDRs that are publicly advertised, and /56 for CIDRs that are [not publicly advertised](#byoip-provision-non-public)\.
+ You can bring each address range to one Region at a time\.
+ You can bring a total of five IPv4 and IPv6 address ranges per Region to your AWS account\.
+ You cannot share your IP address range with other accounts using AWS Resource Access Manager \(AWS RAM\)\.
+ The addresses in the IP address range must have a clean history\. We might investigate the reputation of the IP address range and reserve the right to reject an IP address range if it contains an IP address that has a poor reputation or is associated with malicious behavior\.
+ You must own the IP address that you use\. This means that only the following are supported:
  + ARIN \- "Direct Allocation" and "Direct Assignment" network types
  + RIPE \- "ALLOCATED PA", "LEGACY", "ASSIGNED PI", and "ALLOCATED\-BY\-RIR" allocation statuses
  + APNIC – "ALLOCATED PORTABLE" and "ASSIGNED PORTABLE" allocation statuses

## Prepare to bring your address range to your AWS account<a name="prepare-for-byoip"></a>

To ensure that only you can bring your address range to your AWS account, you must authorize Amazon to advertise the address range\. You must also provide proof that you own the address range through a signed authorization message\.

A Route Origin Authorization \(ROA\) is a cryptographic statement about your route announcements that you can create through your RIR\. It contains the address range, the Autonomous System numbers \(ASN\) that are allowed to advertise the address range, and an expiration date\. A ROA authorizes Amazon to advertise an address range under a specific AS number\. However, it does not authorize your AWS account to bring the address range to AWS\. To authorize your AWS account to bring an address range to AWS, you must publish a self\-signed X509 certificate in the Registry Data Access Protocol \(RDAP\) remarks for the address range\. The certificate contains a public key, which AWS uses to verify the authorization\-context signature that you provide\. Keep your private key secure and use it to sign the authorization\-context message\.

The commands in these tasks are supported on Linux\. On Windows, you can use the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about) to run Linux commands\.

**Topics**
+ [Create a ROA object](#byoip-create-roa-object)
+ [Create a self\-signed X509 certificate](#byoip-certificate)
+ [Create a signed authorization message](#byoip-signed-authorization-message)

### Create a ROA object<a name="byoip-create-roa-object"></a>

Create a ROA object to authorize Amazon ASNs 16509 and 14618 to advertise your address range, plus the ASNs that are currently authorized to advertise the address range\. You must set the maximum length to the size of the smallest prefix that you want to bring \(for example, /24\)\. It might take up to 24 hours for the ROA to become available to Amazon\. For more information, see the following:
+ ARIN — [ROA Requests](https://www.arin.net/resources/rpki/roarequest.html)
+ RIPE — [Managing ROAs](https://www.ripe.net/manage-ips-and-asns/resource-management/certification/resource-certification-roa-management)
+ APNIC — [Route Management](https://www.apnic.net/wp-content/uploads/2017/01/route-roa-management-guide.pdf)

### Create a self\-signed X509 certificate<a name="byoip-certificate"></a>

Use the following procedure to create a self\-signed X509 certificate and add it to the RDAP record for your RIR\. The openssl commands require OpenSSL version 1\.0\.2 or later\.

Copy the commands below and replace only the placeholder values \(in colored italic text\)\. 

**To create a self\-signed X509 certificate and add it to the RDAP record**

1. Generate an RSA 2048\-bit key pair as shown in the following\.

   ```
   openssl genrsa -out private.key 2048
   ```

1. Create a public X509 certificate from the key pair using the following command\. In this example, the certificate expires in 365 days, after which time it cannot be trusted\. Be sure to set the expiration appropriately\. When prompted for information, you can accept the default values\.

   ```
   openssl req -new -x509 -key private.key -days 365 | tr -d "\n" > publickey.cer
   ```

1. Update the RDAP record for your RIR with the X509 certificate\. Be sure to copy the `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----` from the certificate\. Be sure that you have removed newline characters, if you haven't already done so using the tr \-d "\\n" commands in the previous steps\. To view your certificate, run the following command\.

   ```
   cat publickey.cer
   ```

   For ARIN, add the certificate in the "Public Comments" section for your address range\. Do not add it to the comments section for your organization\.

   For RIPE, add the certificate as a new "descr" field for your address range\. Do not add it to the comments section for your organization\.

   For APNIC, email the public key to [helpdesk@apnic\.net](mailto:helpdesk@apnic.net) to manually add it to the "remarks" field for your address range\. Send the email using the APNIC authorized contact for the IP addresses\.

### Create a signed authorization message<a name="byoip-signed-authorization-message"></a>

The format of the signed authorization message is as follows, where the date is the expiry date of the message\.

```
1|aws|account|cidr|YYYYMMDD|SHA256|RSAPSS
```

**To create a signed authorization message**

1. Create a plaintext authorization message and store it in a variable named `text_message` as shown in the following example\. Copy the following example and replace only the example account number, address range, and expiry date with your own values\.

   ```
   text_message="1|aws|123456789012|198.51.100.0/24|20191201|SHA256|RSAPSS"
   ```

1. Sign the authorization message in `text_message` using the key pair that you created, and store it in a variable named `signed_message`\.

   ```
   signed_message=$( echo -n $text_message | openssl dgst -sha256 -sigopt rsa_padding_mode:pss -sigopt rsa_pss_saltlen:-1 -sign private.key -keyform PEM | openssl base64 | tr -- '+=/' '-_~' | tr -d "\n")
   ```
**Important**  
We recommend that you copy and paste this command\. Do not modify or replace any of the values\.

## Provision the address range for use with AWS<a name="byoip-provision"></a>

When you provision an address range for use with AWS, you are confirming that you own the address range and are authorizing Amazon to advertise it\. We also verify that you own the address range through a signed authorization message\. This message is signed with the self\-signed X509 key pair that you used when updating the RDAP record with the X509 certificate\.

**To provision the address range**  
Use the [provision\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/provision-byoip-cidr.html) command\. Replace the example address range with your own address range\. The `--cidr-authorization-context` option uses the variables that you created previously, not the ROA message\.

```
aws ec2 provision-byoip-cidr --cidr address-range --cidr-authorization-context Message="$text_message",Signature="$signed_message"
```

Provisioning an address range is an asynchronous operation, so the call returns immediately, but the address range is not ready to use until its status changes from `pending-provision` to `provisioned`\.

**Note**  
It can take up to three weeks to complete the provisioning process\.

**To monitor the status of the address ranges that you've provisioned**  
Use the [describe\-byoip\-cidrs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-byoip-cidrs.html) command\.

```
aws ec2 describe-byoip-cidrs --max-results 5
```

If there are issues during provisioning and the status goes to `failed-provision`, you must run the `provision-byoip-cidr` command again after the issues have been resolved\.

### Provision an IPv6 address range that's not publicly advertised<a name="byoip-provision-non-public"></a>

By default, an address range is provisioned to be publicly advertised to the internet\. You can provision an IPv6 address range that will not be publicly advertised\. When you associate an IPv6 CIDR block from a non\-public address range with a VPC, the IPv6 CIDR can only be accessed through an AWS Direct Connect connection\. 

An ROA is not required to provision a non\-public address range\.

To provision an IPv6 address range that will not be publicly advertised, use the following [provision\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/provision-byoip-cidr.html) command\.

```
aws ec2 provision-byoip-cidr --cidr address-range --cidr-authorization-context Message="$text_message",Signature="$signed_message" --no-publicly-advertisable
```

**Important**  
You can only set the `publicly-advertisable` or `no-publicly-advertisable` flag during provisioning\. You cannot change the advertisable status of an address range later\.

## Advertise the address range through AWS<a name="byoip-advertise"></a>

After the address range is provisioned, it is ready to be advertised\. You must advertise the exact address range that you provisioned\. You can't advertise only a portion of the provisioned address range\.

If you provisioned an IPv6 address range that will not be publicly advertised, you do not need to complete this step\.

We recommend that you stop advertising the address range from other locations before you advertise it through AWS\. If you keep advertising your IP address range from other locations, we can't reliably support it or troubleshoot issues\. Specifically, we can't guarantee that traffic to the address range will enter our network\.

To minimize down time, you can configure your AWS resources to use an address from your address pool before it is advertised, and then simultaneously stop advertising it from the current location and start advertising it through AWS\. For more information about allocating an Elastic IP address from your address pool, see [Allocate an Elastic IP address](elastic-ip-addresses-eip.md#using-instance-addressing-eips-allocating)\.

To advertise the address range, use the following [advertise\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/advertise-byoip-cidr.html) command\.

```
aws ec2 advertise-byoip-cidr --cidr address-range
```

**Important**  
You can run the advertise\-byoip\-cidr command at most once every 10 seconds, even if you specify different address ranges each time\.

To stop advertising the address range, use the following [withdraw\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/withdraw-byoip-cidr.html) command\.

```
aws ec2 withdraw-byoip-cidr --cidr address-range
```

**Important**  
You can run the withdraw\-byoip\-cidr command at most once every 10 seconds, even if you specify different address ranges each time\.

## Work with your address range<a name="byoip-working-with"></a>

You can view and work with the IPv4 and IPv6 address ranges that you've provisioned in your account\.

### IPv4 address ranges<a name="byoip-work-with-ipv4"></a>

You can create an Elastic IP address from your IPv4 address pool and use it with your AWS resources, such as EC2 instances, NAT gateways, and Network Load Balancers\.

To view information about the IPv4 address pools that you've provisioned in your account, use the following [describe\-public\-ipv4\-pools](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-public-ipv4-pools.html) command\.

```
aws ec2 describe-public-ipv4-pools
```

To create an Elastic IP address from your IPv4 address pool, use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command\. You can use the `--public-ipv4-pool` option to specify the ID of the address pool returned by `describe-byoip-cidrs`\. Or you can use the `--address` option to specify an address from the address range that you provisioned\.

### IPv6 address ranges<a name="byoip-work-with-ipv6"></a>

To view information about the IPv6 address pools that you've provisioned in your account, use the following [describe\-ipv6\-pools](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-ipv6-pools.html) command\.

```
aws ec2 describe-ipv6-pools
```

To create a VPC and specify an IPv6 CIDR from your IPv6 address pool, use the following [create\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html) command\. To let Amazon choose the IPv6 CIDR from your IPv6 address pool, omit the `--ipv6-cidr-block` option\.

```
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --ipv6-cidr-block ipv6-cidr --ipv6-pool pool-id
```

To associate an IPv6 CIDR block from your IPv6 address pool with a VPC, use the following [associate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-vpc-cidr-block.html) command\. To let Amazon choose the IPv6 CIDR from your IPv6 address pool, omit the `--ipv6-cidr-block` option\.

```
aws ec2 associate-vpc-cidr-block --vpc-id vpc-123456789abc123ab --ipv6-cidr-block ipv6-cidr --ipv6-pool pool-id
```

To view your VPCs and the associated IPv6 address pool information, use the [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) command\. To view information about associated IPv6 CIDR blocks from a specific IPv6 address pool, use the following [get\-associated\-ipv6\-pool\-cidrs](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-associated-ipv6-pool-cidrs.html) command\.

```
aws ec2 get-associated-ipv6-pool-cidrs --pool-id pool-id
```

If you disassociate the IPv6 CIDR block from your VPC, it's released back into your IPv6 address pool\.

For more information about working with IPv6 CIDR blocks in the VPC console, see [Working with VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html) in the *Amazon VPC User Guide*\.

## Deprovision the address range<a name="byoip-deprovision"></a>

To stop using your address range with AWS, first release any Elastic IP addresses and disassociate any IPv6 CIDR blocks that are still allocated from the address pool\. Then stop advertising the address range, and finally, deprovision the address range\.

You cannot deprovision a portion of the address range\. If you want to use a more specific address range with AWS, deprovision the entire address range and provision a more specific address range\.

\(IPv4\) To release each Elastic IP address, use the following [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) command\.

```
aws ec2 release-address --allocation-id eipalloc-12345678abcabcabc
```

\(IPv6\) To disassociate an IPv6 CIDR block, use the following [disassociate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-vpc-cidr-block.html) command\.

```
aws ec2 disassociate-vpc-cidr-block --association-id vpc-cidr-assoc-12345abcd1234abc1
```

To stop advertising the address range, use the following [withdraw\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/withdraw-byoip-cidr.html) command\.

```
aws ec2 withdraw-byoip-cidr --cidr address-range
```

To deprovision the address range, use the following [deprovision\-byoip\-cidr](https://docs.aws.amazon.com/cli/latest/reference/ec2/deprovision-byoip-cidr.html) command\.

```
aws ec2 deprovision-byoip-cidr --cidr address-range
```

It can take up to a day to deprovision an address range\.
