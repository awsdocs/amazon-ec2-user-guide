# Prerequisites<a name="ec2-serial-console-prerequisites"></a>

**Topics**
+ [AWS Regions](#sc-prereqs-regions)
+ [Wavelength Zones and AWS Outposts](#sc-prereqs-wavelength-zones-outposts)
+ [Local Zones](#sc-prereqs-local-zones)
+ [Instance types](#sc-prereqs-instance-types)
+ [Grant access](#sc-prereqs-configure-ec2-serial-console)
+ [Support for browser\-based client](#sc-prereqs-for-browser-based-connection)
+ [Instance state](#sc-prereqs-instance-state)
+ [Amazon EC2 Systems Manager](#sc-prereqs-ssm)
+ [sshd server](#sc-prereqs-sshd-server)

## AWS Regions<a name="sc-prereqs-regions"></a>

Supported in the following AWS Regions:
+ US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\)
+ Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), Europe \(Paris\), Europe \(Stockholm\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\), AWS GovCloud \(US\-West\)

## Wavelength Zones and AWS Outposts<a name="sc-prereqs-wavelength-zones-outposts"></a>

Not supported\.

## Local Zones<a name="sc-prereqs-local-zones"></a>

Supported in the following Local Zones:
+ Dallas, US

  Local Zone name: `us-east-1-dfw-1a`

  Parent Region: US East \(N\. Virginia\)
+ Los Angeles, US

  Local Zone names: `us-west-2-lax-1a`, `us-west-2-lax-1b`

  Parent Region: US West \(Oregon\)

## Instance types<a name="sc-prereqs-instance-types"></a>

Supported instance types:
+ All virtualized instances built on the [Nitro System](instance-types.md#nitro-instance-types)\.
+ All bare metal instances except:
  + General purpose: `a1.metal`, `mac1.metal`, `mac2.metal`
  + Accelerated computing: `g5g.metal`
  + Memory optimized: `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, `u-24tb1.metal`

## Grant access<a name="sc-prereqs-configure-ec2-serial-console"></a>

You must complete the configuration tasks to grant access to the EC2 Serial Console\. For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.

## Support for browser\-based client<a name="sc-prereqs-for-browser-based-connection"></a>

To connect to the serial console [using the browser\-based client](connect-to-serial-console.md#sc-connect-browser-based-client), your browser must support WebSocket\. If your browser does not support WebSocket, connect to the serial console [using your own key and an SSH client\.](connect-to-serial-console.md#sc-connect-SSH)

## Instance state<a name="sc-prereqs-instance-state"></a>

Must be `running`\.

You can't connect to the serial console if the instance is in the `pending`, `stopping`, `stopped`, `shutting-down`, or `terminated` state\.

For more information about the instance states, see [Instance lifecycle](ec2-instance-lifecycle.md)\.

## Amazon EC2 Systems Manager<a name="sc-prereqs-ssm"></a>

If the instance uses Amazon EC2 Systems Manager, then SSM Agent version 3\.0\.854\.0 or later must be installed on the instance\. For information about SSM Agent, see [Working with SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) in the *AWS Systems Manager User Guide*\.

## sshd server<a name="sc-prereqs-sshd-server"></a>

You do not need an sshd server installed or running on your instance\.