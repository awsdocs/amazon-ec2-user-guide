# Optimizing Disk Performance for Instance Store Volumes<a name="disk-performance"></a>

Because of the way that Amazon EC2 virtualizes disks, the first write to any location on some instance store volumes performs more slowly than subsequent writes\. For most applications, amortizing this cost over the lifetime of the instance is acceptable\. However, if you require high disk performance, we recommend that you initialize your drives by writing once to every drive location before production use\.

**Note**  
Some instance types with direct\-attached solid state drives \(SSD\) and TRIM support provide maximum performance at launch time, without initialization\. For information about the instance store for each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

If you require greater flexibility in latency or throughput, we recommend using Amazon EBS\.

To initialize the instance store volumes, use the following `dd` commands, depending on the store to initialize \(for example, `/dev/sdb` or `/dev/nvme1n1`\)\.

**Note**  
Make sure to unmount the drive before performing this command\.   
Initialization can take a long time \(about 8 hours for an extra large instance\)\.

To initialize the instance store volumes, use the following commands on the `m1.large`, `m1.xlarge`, `c1.xlarge`, `m2.xlarge`, `m2.2xlarge`, and `m2.4xlarge` instance types:

```
dd if=/dev/zero of=/dev/sdb bs=1M          
dd if=/dev/zero of=/dev/sdc bs=1M          
dd if=/dev/zero of=/dev/sdd bs=1M          
dd if=/dev/zero of=/dev/sde bs=1M
```

To perform initialization on all instance store volumes at the same time, use the following command: 

```
dd if=/dev/zero bs=1M|tee /dev/sdb|tee /dev/sdc|tee /dev/sde > /dev/sdd
```

Configuring drives for RAID initializes them by writing to every drive location\. When configuring software\-based RAID, make sure to change the minimum reconstruction speed: 

```
echo $((30*1024)) > /proc/sys/dev/raid/speed_limit_min
```