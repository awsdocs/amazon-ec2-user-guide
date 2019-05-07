# Monitoring an EFA<a name="efa-working-monitor"></a>

You can use the following features to monitor the performance of your Elastic Fabric Adapters\.

## Amazon VPC Flow Logs<a name="efa-flowlog"></a>

You can create an Amazon VPC flow log to capture information about the traffic going to and from an EFA\. Flow log data can be published to Amazon CloudWatch Logs and Amazon S3\. After you create a flow log, you can retrieve and view its data in the chosen destination\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

You create a flow log for an EFA in the same way that you create a flow log for an ENI\. For more information, see [Creating a Flow Log](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-flow-logs.html#create-flow-log) in the *Amazon VPC User Guide*\.

In the flow log entries, EFA traffic is identified by the `srcAddress` and `destAddress`, which are both formatted as MAC addresses\. For example:

```
version accountId  eniId        srcAddress        destAddress       sourcePort destPort protocol packets bytes start      end        action log-status
2       3794735123 eni-10000001 01:23:45:67:89:ab 05:23:45:67:89:ab -          -        -        9       5689  1521232534 1524512343 ACCEPT OK
```

## Amazon CloudWatch<a name="efa-cloudwatch"></a>

Amazon CloudWatch provides metrics that enable you to monitor your EFAs in real time\. You can collect and track metrics, create customized dashboards, and set alarms that notify you or take actions when a specified metric reaches a threshold that you specify\. For more information, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\.