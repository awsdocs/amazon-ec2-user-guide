# Monitoring Dedicated Hosts<a name="dedicated-hosts-monitoring"></a>

Amazon EC2 constantly monitors the state of your Dedicated Hosts; updates are communicated on the Amazon EC2 console\. You can also obtain information about your Dedicated Hosts using the API or CLI\.

The following table illustrates the possible **State** values in the console\.


| **State** | **Description** | 
| --- | --- | 
| available | AWS hasn't detected an issue with the Dedicated Host; no maintenance or repairs are scheduled\. Instances can be launched onto this Dedicated Host\. | 
| released | The Dedicated Host has been released\. The host ID is no longer in use\. Released hosts cannot be reused\. | 
| under\-assessment | AWS is exploring a possible issue with the Dedicated Host\. If action needs to be taken, you are notified via the AWS Management Console or email\. Instances cannot be launched onto a Dedicated Host in this state\. | 
| permanent\-failure | An unrecoverable failure has been detected\. You receive an eviction notice through your instances and by email\. Your instances may continue to run\. If you stop or terminate all instances on a Dedicated Host with this state, AWS retires the host\. Instances cannot be launched onto Dedicated Hosts in this state\. | 
| released\-permanent\-failure | AWS permanently releases Dedicated Hosts that have failed and no longer have running instances on them\. The Dedicated Host ID is no longer available for use\. | 