# SSD Instance Store Volumes<a name="ssd-instance-store"></a>

The following instances support instance store volumes that use solid state drives \(SSD\) to deliver high random I/O performance: C3, G2, I2, M3, R3, and X1\. For more information about the instance store volumes support by each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

To ensure the best IOPS performance from your SSD instance store volumes on Linux, we recommend that you use the most recent version of Amazon Linux, or another Linux AMI with a kernel version of 3\.8 or later\. If you do not use a Linux AMI with a kernel version of 3\.8 or later, your instance won't achieve the maximum IOPS performance available for these instance types\.

Like other instance store volumes, you must map the SSD instance store volumes for your instance when you launch it\. The data on an SSD instance volume persists only for the life of its associated instance\. For more information, see [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)\.

## NVMe SSD Volumes<a name="nvme-ssd-volumes"></a>

The following instances offer non\-volatile memory express \(NVMe\) SSD instance store volumes: C5d, I3, F1, M5ad, M5d, `p3dn.24xlarge`, R5ad, R5d, and z1d\. To access NVMe volumes, the [NVMe drivers](nvme-ebs-volumes.md#install-nvme-driver) must be installed\. The following AMIs meet this requirement:
+ Amazon Linux 2
+ Amazon Linux AMI 2018\.03
+ Ubuntu 14\.04 or later
+ Red Hat Enterprise Linux 7\.4 or later
+ SUSE Linux Enterprise Server 12 SP2 or later
+ CentOS 7 or later
+ FreeBSD 11\.1 or later

After you connect to your instance, you can list the NVMe devices using the lspci command\. The following is example output for an `i3.8xlarge` instance, which supports four NVMe devices\.

```
[ec2-user ~]$ lspci
00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma] (rev 02)
00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
00:01.1 IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]
00:01.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 01)
00:02.0 VGA compatible controller: Cirrus Logic GD 5446
00:03.0 Ethernet controller: Device 1d0f:ec20
00:17.0 Non-Volatile memory controller: Device 1d0f:cd01
00:18.0 Non-Volatile memory controller: Device 1d0f:cd01
00:19.0 Non-Volatile memory controller: Device 1d0f:cd01
00:1a.0 Non-Volatile memory controller: Device 1d0f:cd01
00:1f.0 Unassigned class [ff80]: XenSource, Inc. Xen Platform Device (rev 01)
```

If you are using a supported operating system but you do not see the NVMe devices, verify that the NVMe module is loaded using the following command\.
+ Amazon Linux, Amazon Linux 2, Ubuntu 14/16, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, CentOS 7

  ```
  $ lsmod | grep nvme
  nvme          48813  0
  ```
+ Ubuntu 18

  ```
  $ cat /lib/modules/$(uname -r)/modules.builtin | grep nvme
  s/nvme/host/nvme-core.ko
  kernel/drivers/nvme/host/nvme.ko
  kernel/drivers/nvmem/nvmem_core.ko
  ```

The NVMe volumes are compliant with the NVMe 1\.0e specification\. You can use the NVMe commands with your NVMe volumes\. With Amazon Linux, you can install the `nvme-cli` package from the repo using the yum install command\. With other supported versions of Linux, you can download the `nvme-cli` package if it's not available in the image\.

The data on NVMe instance storage is encrypted using an XTS\-AES\-256 block cipher implemented in a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

## Instance Store Volume TRIM Support<a name="InstanceStoreTrimSupport"></a>

The following instances support SSD volumes with TRIM: C5d, F1, I2, I3, M5ad, M5d, `p3dn.24xlarge`, R3, R5ad, R5d, and z1d\.

Instance store volumes that support TRIM are fully trimmed before they are allocated to your instance\. These volumes are not formatted with a file system when an instance launches, so you must format them before they can be mounted and used\. For faster access to these volumes, you should skip the TRIM operation when you format them\.

With instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller when you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. On Linux, use the `fstrim` command to enable periodic TRIM\. 