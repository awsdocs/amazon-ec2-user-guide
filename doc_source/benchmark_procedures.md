# Benchmark EBS Volumes<a name="benchmark_procedures"></a>

You can test the performance of Amazon EBS volumes by simulating I/O workloads\. The process is as follows:

1. Launch an EBS\-optimized instance\.

1. Create new EBS volumes\.

1. Attach the volumes to your EBS\-optimized instance\.

1. Configure and mount the block device\.

1. Install a tool to benchmark I/O performance\.

1. Benchmark the I/O performance of your volumes\.

1. Delete your volumes and terminate your instance so that you don't continue to incur charges\.

**Important**  
Some of the procedures result in the destruction of existing data on the EBS volumes you benchmark\. The benchmarking procedures are intended for use on volumes specially created for testing purposes, not production volumes\.

## Set Up Your Instance<a name="set_up_instance"></a>

To get optimal performance from EBS volumes, we recommend that you use an EBS\-optimized instance\. EBS\-optimized instances deliver dedicated throughput between Amazon EC2 and Amazon EBS, with instance\. EBS\-optimized instances deliver dedicated bandwidth between Amazon EC2 and Amazon EBS, with specifications depending on the instance type\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

To create an EBS\-optimized instance, choose **Launch as an EBS\-Optimized instance** when launching the instance using the Amazon EC2 console, or specify \-\-ebs\-optimized when using the command line\. Be sure that you launch a current\-generation instance that supports this option\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

### Setting up Provisioned IOPS SSD \(`io1`\) volumes<a name="setupPIOPS"></a>

To create an `io1` volume, choose **Provisioned IOPS SSD** when creating the volume using the Amazon EC2 console, or, at the command line, specify \-\-type io1 \-\-iops *n* where *n* is an integer between 100 and 64,000\. For more detailed EBS\-volume specifications, see [Amazon EBS Volume Types](ebs-volume-types.md)\. For information about creating an EBS volume, see [Creating an Amazon EBS Volume](ebs-creating-volume.md)\. For information about attaching a volume to an instance, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

For the example tests, we recommend that you create a RAID array with 6 volumes, which offers a high level of performance\. Because you are charged by gigabytes provisioned \(and the number of provisioned IOPS for `io1` volumes\), not the number of volumes, there is no additional cost for creating multiple, smaller volumes and using them to create a stripe set\. If you're using Oracle Orion to benchmark your volumes, it can simulate striping the same way that Oracle ASM does, so we recommend that you let Orion do the striping\. If you are using a different benchmarking tool, you need to stripe the volumes yourself\.

To create a six\-volume stripe set on Amazon Linux, use a command such as the following:

```
[ec2-user ~]$ sudo mdadm --create /dev/md0 --level=0 --chunk=64 --raid-devices=6 /dev/sdf /dev/sdg /dev/sdh /dev/sdi /dev/sdj /dev/sdk
```

For this example, the file system is XFS\. Use the file system that meets your requirements\. Use the following command to install XFS file system support:

```
[ec2-user ~]$ sudo yum install -y xfsprogs
```

Then, use these commands to create, mount, and assign ownership to the XFS file system:

```
[ec2-user ~]$ sudo mkdir -p /mnt/p_iops_vol0 && sudo mkfs.xfs /dev/md0
[ec2-user ~]$ sudo mount -t xfs /dev/md0 /mnt/p_iops_vol0
[ec2-user ~]$ sudo chown ec2-user:ec2-user /mnt/p_iops_vol0/
```

### Setting up Throughput Optimized HDD \(`st1`\) or Cold HDD \(`sc1`\) volumes<a name="set_up_hdd"></a>

To create an `st1` volume, choose **Throughput Optimized HDD** when creating the volume using the Amazon EC2 console, or specify \-\-type `st1` when using the command line\. To create an `sc1` volume, choose Cold HDD when creating the volume using the Amazon EC2 console, or specify \-\-type `sc1` when using the command line\. For information about creating EBS volumes, see [Creating an Amazon EBS Volume](ebs-creating-volume.md)\. For information about attaching these volumes to your instance, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

AWS provides a JSON template for use with AWS CloudFormation that simplifies this setup procedure\. Access the [template](https://s3.amazonaws.com/cloudformation-examples/community/st1_cloudformation_template.json) and save it as a JSON file\. AWS CloudFormation allows you to configure your own SSH keys and offers an easier way to set up a performance test environment to evaluate `st1` volumes\. The template creates a current\-generation instance and a 2 TiB `st1` volume, and attaches the volume to the instance at `/dev/xvdf`\. 

**To create an HDD volume with the template**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Choose **Create Stack**\.

1. Choose **Upload a Template to Amazon S3** and select the JSON template you previously obtained\.

1. Give your stack a name like “ebs\-perf\-testing”, and select an instance type \(the default is r3\.8xlarge\) and SSH key\.

1. Choose **Next** twice, and then choose **Create Stack**\.

1. After the status for your new stack moves from **CREATE\_IN\_PROGRESS** to **COMPLETE**, choose **Outputs** to get the public DNS entry for your new instance, which will have a 2 TiB `st1` volume attached to it\.

1. Connect using SSH to your new stack as user **ec2\-user**, with the hostname obtained from the DNS entry in the previous step\. 

1. Proceed to [Install Benchmark Tools](#install_tools)\.

## Install Benchmark Tools<a name="install_tools"></a>

The following table lists some of the possible tools you can use to benchmark the performance of EBS volumes\.


| Tool | Description | 
| --- | --- | 
|  fio  |  For benchmarking I/O performance\. \(Note that fio has a dependency on `libaio-devel`\.\) To install fio on Amazon Linux, run the following command: <pre>[ec2-user ~]$ sudo yum install -y fio</pre> To install fio on Ubuntu, run the following command: <pre>sudo apt-get install -y fio</pre>  | 
|  [Oracle Orion Calibration Tool](https://docs.oracle.com/cd/E18283_01/server.112/e16638/iodesign.htm#BABFCFBC)  |  For calibrating the I/O performance of storage systems to be used with Oracle databases\.  | 

These benchmarking tools support a wide variety of test parameters\. You should use commands that approximate the workloads your volumes will support\. These commands provided below are intended as examples to help you get started\.

## Choosing the Volume Queue Length<a name="UnderstandingQueueLength"></a>

Choosing the best volume queue length based on your workload and volume type\.

### Queue Length on SSD\-backed Volumes<a name="SSD_queue"></a>

To determine the optimal queue length for your workload on SSD\-backed volumes, we recommend that you target a queue length of 1 for every 1000 IOPS available \(baseline for `gp2` volumes and the provisioned amount for `io1` volumes\)\. Then you can monitor your application performance and tune that value based on your application requirements\.

Increasing the queue length is beneficial until you achieve the provisioned IOPS, throughput or optimal system queue length value, which is currently set to 32\. For example, a volume with 3,000 provisioned IOPS should target a queue length of 3\. You should experiment with tuning these values up or down to see what performs best for your application\.

### Queue Length on HDD\-backed Volumes<a name="HDD_queue"></a>

To determine the optimal queue length for your workload on HDD\-backed volumes, we recommend that you target a queue length of at least 4 while performing 1MiB sequential I/Os\. Then you can monitor your application performance and tune that value based on your application requirements\. For example, a 2 TiB `st1` volume with burst throughput of 500 MiB/s and IOPS of 500 should target a queue length of 4, 8, or 16 while performing 1,024 KiB, 512 KiB, or 256 KiB sequential I/Os respectively\. You should experiment with tuning these values value up or down to see what performs best for your application\.

## Disable C\-States<a name="cstates"></a>

Before you run benchmarking, you should disable processor C\-states\. Temporarily idle cores in a supported CPU can enter a C\-state to save power\. When the core is called on to resume processing, a certain amount of time passes until the core is again fully operational\. This latency can interfere with processor benchmarking routines\. For more information about C\-states and which EC2 instance types support them, see [Processor State Control for Your EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/processor_state_control.html)\.

### Disabling C\-States on a Linux System<a name="linux-cstates"></a>

You can disable C\-states on Amazon Linux, RHEL, and CentOS as follows:

1. Get the number of C\-states\.

   ```
   $ cpupower idle-info | grep "Number of idle states:"
   ```

1. Disable the C\-states from c1 to cN\. Ideally, the cores should be in state c0\.

   ```
   $ for i in `seq 1 $((N-1))`; do cpupower idle-set -d $i; done
   ```

## Perform Benchmarking<a name="perform_benchmarking"></a>

The following procedures describe benchmarking commands for various EBS volume types\. 

Run the following commands on an EBS\-optimized instance with attached EBS volumes\. If the EBS volumes were restored from snapshots, be sure to initialize them before benchmarking\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

When you are finished testing your volumes, see the following topics for help cleaning up: [Deleting an Amazon EBS Volume](ebs-deleting-volume.md) and [Terminate Your Instance](terminating-instances.md)\.

### Benchmarking io1 Volumes<a name="piops_benchmarking"></a>

Run fio on the stripe set that you created\.

The following command performs 16 KB random write operations\.

```
[ec2-user ~]$ sudo fio --directory=/mnt/p_iops_vol0 --name fio_test_file --direct=1 --rw=randwrite --bs=16k --size=1G --numjobs=16 --time_based --runtime=180 --group_reporting --norandommap
```

The following command performs 16 KB random read operations\.

```
[ec2-user ~]$ sudo fio --directory=/mnt/p_iops_vol0 --name fio_test_file --direct=1 --rw=randread --bs=16k --size=1G --numjobs=16 --time_based --runtime=180 --group_reporting --norandommap 
```

For more information about interpreting the results, see this tutorial: [Inspecting disk IO performance with fio](https://www.linux.com/tutorials/inspecting-disk-io-performance-fio/)\.

### Benchmarking `st1` and `sc1` Volumes<a name="hdd_benchmarking"></a>

Run fio on your `st1` or `sc1` volume\.

**Note**  
Prior to running these tests, set buffered I/O on your instance as described in [Increase Read\-Ahead for High\-Throughput, Read\-Heavy Workloads on `st1` and `sc1`](EBSPerformance.md#read_ahead)\. 

The following command performs 1 MiB sequential read operations against an attached `st1` block device \(e\.g\., `/dev/xvdf`\):

```
[ec2-user ~]$ sudo fio --filename=/dev/<device> --direct=1 --rw=read --randrepeat=0 --ioengine=libaio --bs=1024k --iodepth=8 --time_based=1 --runtime=180 --name=fio_direct_read_test
```

The following command performs 1 MiB sequential write operations against an attached `st1` block device:

```
[ec2-user ~]$ sudo fio --filename=/dev/<device> --direct=1 --rw=write --randrepeat=0 --ioengine=libaio --bs=1024k --iodepth=8 --time_based=1 --runtime=180 --name=fio_direct_write_test 
```

Some workloads perform a mix of sequential reads and sequential writes to different parts of the block device\. To benchmark such a workload, we recommend that you use separate, simultaneous fio jobs for reads and writes, and use the fio `offset_increment` option to target different block device locations for each job\. 

Running this workload is a bit more complicated than a sequential\-write or sequential\-read workload\. Use a text editor to create a fio job file, called `fio_rw_mix.cfg` in this example, that contains the following:

```
[global] 
clocksource=clock_gettime
randrepeat=0
runtime=180
offset_increment=100g
 
[sequential-write]
bs=1M
ioengine=libaio
direct=1
iodepth=8
filename=/dev/<device>
do_verify=0
rw=write
rwmixread=0
rwmixwrite=100 

[sequential-read] 
bs=1M
ioengine=libaio
direct=1
iodepth=8
filename=/dev/<device>
do_verify=0
rw=read
rwmixread=100
rwmixwrite=0
```

Then run the following command: 

```
[ec2-user ~]$ sudo fio fio_rw_mix.cfg
```

For more information about interpreting the results, see this tutorial: [Inspecting disk I/O performance with fio](https://www.linux.com/tutorials/inspecting-disk-io-performance-fio/)\.

Multiple fio jobs for direct I/O, even though using sequential read or write operations, can result in lower than expected throughput for `st1` and `sc1` volumes\. We recommend that you use one direct I/O job and use the `iodepth` parameter to control the number of concurrent I/O operations\. 