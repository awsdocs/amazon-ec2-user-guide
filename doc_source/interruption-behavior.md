# Interruption behavior<a name="interruption-behavior"></a>

You can specify that Amazon EC2 must do one of the following when it interrupts a Spot Instance:
+ [Stop interrupted Spot Instances](stop-spot-instances.md)
+ [Hibernate interrupted Spot Instances](hibernate-spot-instances.md)
+ [Terminate interrupted Spot Instances](terminate-interrupted-spot-instances.md) \(this is the default behavior\)

## Specify the interruption behavior<a name="specifying-spot-interruption-behavior"></a>

You can specify the interruption behavior when you create a Spot request\. If you do not specify an interruption behavior, the default is that Amazon EC2 terminates Spot Instances when they are interrupted\.

The way in which you specify the interruption behavior is different depending on how you request Spot Instances\.
+ If you request Spot Instances using the [launch instance wizard](ec2-launch-instance-wizard.md), you can specify the interruption behavior as follows: From **Request type**, choose **Persistent** \(new console\) or select the **Persistent request** check box \(old console\) and then, from **Interruption behavior**, choose an interruption behavior\.
+ If you request Spot Instances using the [Spot console](work-with-spot-fleets.md#create-spot-fleet), you can specify the interruption behavior as follows: Select the **Maintain target capacity** check box and then, from **Interruption behavior**, choose an interruption behavior\.
+ If you configure Spot Instances in a [launch template](create-launch-template.md), you can specify the interruption behavior as follows: In the launch template, expand **Advanced details** and select the **Request Spot Instances** check box\. Choose **Customize** and then, from **Interruption behavior**, choose an interruption behavior\.
+ If you configure Spot Instances in the request configuration when using the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) CLI, you can specify the interruption behavior as follows: For `InstanceInterruptionBehavior`, specify an interruption behavior\.
+ If you configure Spot Instances in the request configuration when using the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) CLI, you can specify the interruption behavior as follows: For `InstanceInterruptionBehavior`, specify an interruption behavior\.
+ If you configure Spot Instances using the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) CLI, you can specify the interruption behavior as follows: For `--instance-interruption-behavior`, specify an interruption behavior\.