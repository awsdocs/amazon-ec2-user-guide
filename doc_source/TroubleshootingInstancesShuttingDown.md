# Troubleshoot instance termination \(shutting down\)<a name="TroubleshootingInstancesShuttingDown"></a>

You are not billed for any instance usage while an instance is not in the `running` state\. In other words, when you terminate an instance, you stop incurring charges for that instance as soon as its state changes to `shutting-down`\.

## Instance terminates immediately<a name="instance-terminates-immediately"></a>

Several issues can cause your instance to terminate immediately on start\-up\. See [Instance terminates immediately](troubleshooting-launch.md#troubleshooting-launch-internal) for more information\.

## Delayed instance termination<a name="instance-stuck-terminating"></a>

If your instance remains in the `shutting-down` state longer than a few minutes, it might be delayed due to shutdown scripts being run by the instance\.

Another possible cause is a problem with the underlying host computer\. If your instance remains in the `shutting-down` state for several hours, Amazon EC2 treats it as a stuck instance and forcibly terminates it\.

If it appears that your instance is stuck terminating and it has been longer than several hours, post a request for help to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the instance ID and describe the steps that you've already taken\. Alternatively, if you have a support plan, create a technical support case in the [Support Center](https://console.aws.amazon.com/support/home#/)\.

## Terminated instance still displayed<a name="terminated-instance-still-displaying"></a>

After you terminate an instance, it remains visible for a short while before being deleted\. The state shows as `terminated`\. If the entry is not deleted after several hours, contact Support\.

## Instances automatically launched or terminated<a name="automatic-instance-create-or-delete"></a>

Generally, the following behaviors mean that you've used Amazon EC2 Auto Scaling, EC2 Fleet, or Spot Fleet to scale your computing resources automatically based on criteria that you've defined:
+ You terminate an instance and a new instance launches automatically\.
+ You launch an instance and one of your instances terminates automatically\.
+ You stop an instance and it terminates and a new instance launches automatically\.

To stop automatic scaling, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/), [Launch instances using an EC2 Fleet](ec2-fleet.md), or [Create a Spot Fleet request](spot-fleet-requests.md#create-spot-fleet)\.