# Capacity Reservations in Wavelength Zones<a name="capacity-reservations-wavelengthzones"></a>

*AWS Wavelength* enables developers to build applications that deliver ultra\-low latencies to mobile devices and end users\. Wavelength deploys standard AWS compute and storage services to the edge of telecommunication carriers' 5G networks\. You can extend an Amazon Virtual Private Cloud \(VPC\) to one or more Wavelength Zones\. You can then use AWS resources like Amazon EC2 instances to run applications that require ultra\-low latency and a connection to AWS services in the Region\. For more information, see [ AWS Wavelength Zones](http://aws.amazon.com/wavelength/)\.

When you create On\-Demand Capacity Reservations, you can choose the Wavelength Zone and you can launch instances into a Capacity Reservation in a Wavelength Zone by specifying the subnet associated with the Wavelength Zone\. A Wavelength Zone is represented by an AWS Region code followed by an identifier that indicates the location, for example `us-east-1-wl1-bos-wlz-1`\.

Wavelength Zones are not available in every Region\. For information about the Regions that support Wavelength Zones, see [Available Wavelength Zones](https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-quotas.html) in the *AWS Wavelength Developer Guide*\.

**Considerations**  
You can't use Capacity Reservation groups in a Wavelength Zone\.

**To use a Capacity Reservation in a Wavelength Zone**

1. Enable the Wavelength Zone for use in your AWS account\. For more information, see [ Enable Wavelength Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#opt-in-wavelength-zone) in the *Amazon EC2 User Guide for Linux Instances*\. 

1. Create a Capacity Reservation in the Wavelength Zone\. For **Availability Zone**, choose the Wavelength\. The Wavelength is represented by an AWS Region code followed by an identifier that indicates the location, for example `us-east-1-wl1-bos-wlz-1`\. For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-create)\.

1. Create a subnet in the Wavelength Zone\. For **Availability Zone**, choose the Wavelength Zone\. For more information, see [Creating a subnet in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet) in the *Amazon VPC User Guide*\. 

1. Launch an instance\. For **Subnet**, choose the subnet in the Wavelength Zone \(for example `subnet-123abc | us-east-1-wl1-bos-wlz-1`\), and for **Capacity Reservation**, choose the specification \(either `open` or target it by ID\) that's required for the Capacity Reservation that you created in the Wavelength\. For more information, see [Launch instances into an existing Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\. 