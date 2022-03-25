# I/O scheduler<a name="io-scheduler"></a>

The I/O scheduler is a part of the Linux operating system that sorts and merges I/O requests and determines the order in which they are processed\.

I/O schedulers are particularly beneficial for devices such as magnetic hard drives, where seek time can be expensive and where it is optimal to merge co\-located requests\. I/O schedulers have less of an effect with solid state devices and virtualized environments\. This is because for solid state devices, sequential and random access don't differ, and for virtualized environments, the host provides its own layer of scheduling\.

This topic discusses the Amazon Linux I/O scheduler\. For more information about the I/O scheduler used by other Linux distributions, refer to their respective documentation\.

**Topics**
+ [Supported schedulers](#supported-schedulers)
+ [Default scheduler](#default-schedulers)
+ [Change the scheduler](#change-scheduler)

## Supported schedulers<a name="supported-schedulers"></a>

Amazon Linux supports the following I/O schedulers:
+ `deadline` — The *Deadline* I/O scheduler sorts I/O requests and handles them in the most efficient order\. It guarantees a start time for each I/O request\. It also gives I/O requests that have been pending for too long a higher priority\.
+ `cfq` — The *Completely Fair Queueing* \(CFQ\) I/O scheduler attempts to fairly allocate I/O resources between processes\. It sorts and inserts I/O requests into per\-process queues\.
+ `noop` — The *No Operation* \(noop\) I/O scheduler inserts all I/O requests into a FIFO queue and then merges them into a single request\. This scheduler does not do any request sorting\.

## Default scheduler<a name="default-schedulers"></a>

No Operation \(noop\) is the default I/O scheduler for Amazon Linux\. This scheduler is used for the following reasons:
+ Many instance types use virtualized devices where the underlying host performs scheduling for the instance\.
+ Solid state devices are used in many instance types where the benefits of an I/O scheduler have less effect\.
+ It is the least invasive I/O scheduler, and it can be customized if needed\.

## Change the scheduler<a name="change-scheduler"></a>

Changing the I/O scheduler can increase or decrease performance based on whether the scheduler results in more or fewer I/O requests being completed in a given time\. This is largely dependent on your workload, the generation of the instance type that's being used, and the type of device being accessed\. If you change the I/O scheduler being used, we recommend that you use a tool, such as **iotop**, to measure I/O performance and to determine whether the change is beneficial for your use case\.

You can view the I/O scheduler for a device using the following command, which uses `nvme0n1` as an example\. Replace `nvme0n1` in the following command with the device listed in `/sys/block` on your instance\.

```
$  cat /sys/block/nvme0n1/queue/scheduler
```

To set the I/O scheduler for the device, use the following command\. 

```
$  echo cfq|deadline|noop > /sys/block/nvme0n1/queue/scheduler
```

For example, to set the I/O scheduler for an *xvda* device from `noop` to `cfq` , use the following command\. 

```
$  echo cfq > /sys/block/xvda/queue/scheduler
```