# Understanding Instance Placement and Host Affinity<a name="dedicated-hosts-instance-placement"></a>

Placement control happens on both the instance level and host level\.


+ [Instance Auto\-Placement](#dedicated-hosts-auto-placement)
+ [Host Affinity](#dedicated-hosts-affinity)
+ [Modifying Instance Auto\-Placement and Host Affinity](#dedicated-hosts-instance-placement-controls)
+ [Modifying Instance Host Affinity](#modifying-instance-host-affinity)

## Instance Auto\-Placement<a name="dedicated-hosts-auto-placement"></a>

Auto\-placement allows you to manage whether instances that you launch are launched onto a specific host, or onto any host that has matching configurations\. The default setting for this is **Off**\. This means that the Dedicated Host you are allocating only accepts `host` tenancy instances launches that specify the unique host ID\. Instances launched without a specified host ID are not able to launch onto a host that has instance auto\-placement set to **Off**\.

## Host Affinity<a name="dedicated-hosts-affinity"></a>

Host Affinity establishes a launch relationship between an instance and a Dedicated Host\. When affinity is set to `host`, an instance launched onto a specific host always restarts on the same host if stopped\. This applies to both targeted and untargeted launches\.

If affinity is set to `default`, and you stop and restart the instance, it can be restarted on any available host\. However, it tries to launch back onto the last Dedicated Host on which it ran \(on a best\-effort basis\)\.

You can modify the relationship between an instance and a Dedicated Host by changing the affinity from `host` to `default` and vice versa\. For more information, see [Modifying Instance Tenancies](how-dedicated-hosts-work.md#moving-instances-dedicated-hosts)\.

## Modifying Instance Auto\-Placement and Host Affinity<a name="dedicated-hosts-instance-placement-controls"></a>

You can manage instance placement controls using the Amazon EC2 console, the API, or CLI\.

To modify the instance placement settings of your instances, first stop the instances and then edit the instance placement settings\.

**Note**  
If the instance is stopped and restarted, it is not guaranteed to restart on the same Dedicated Host\.

**To edit an instance's placement settings \(any available hosts\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Instances** page, select the instance to edit\.

1. Choose **Actions**, **Instance State**, and **Stop**\.

1. Choose **Actions**, **Instance Settings**, and **Modify Instance Placement**\.

1. Change the instance tenancy to **Launch this instance on a Dedicated host**\.

1. Choose **This instance can run on any one of my Hosts**\. The instance launches onto any Dedicated Host that has auto\-placement enabled\.

1. Choose **Save** to continue\.

1. Open the context \(right\-click\) menu on the instance and choose **Instance State**, **Start**\.

**To edit an instance's placement settings \(specific Dedicated Host\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Instances** page, select the instance to edit\.

1. Choose **Actions**, **Instance State**, and **Stop**\.

1. Choose **Actions**, **Instance Settings**, and **Modify Instance Placement**\.

1. Change the instance tenancy to **Launch this instance on a Dedicated host**\.

1. Choose **This instance can only run on the selected Host**\. Then select a value for **Target Host** and choose whether you want the instance to be placed on any available host, or a specific host\. 

1. Choose **Save** to continue\.

1. Open the context \(right\-click\) menu on the instance and choose **Instance State**, **Start**\.

## Modifying Instance Host Affinity<a name="modifying-instance-host-affinity"></a>

If you no longer want an instance to have affinity with a host, you can stop the instance and change its affinity to `default`\. This removes the persistence between the instance and the host\. However, when you restart the instance, it may launch back onto the same Dedicated Host \(depending on Dedicated Host availability in your account, and on a best\-effort basis\)\. However, if it is stopped again, it does not restart on the same host\.