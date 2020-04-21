# Troubleshooting the Elastic Network Adapter \(ENA\)<a name="troubleshooting-ena"></a>

The Elastic Network Adapter \(ENA\) is designed to improve operating system health and reduce the chances of long\-term disruption because of unexpected hardware behavior and or failures\. The ENA architecture keeps device or driver failures as transparent to the system as possible\. This topic provides troubleshooting information for ENA\.

If you are unable to connect to your instance, start with the [Troubleshooting connectivity issues](#ena-connectivity-issues) section\. 

If you are able to connect to your instance, you can gather diagnostic information by using the failure detection and recovery mechanisms that are covered in the later sections of this topic\.

**Topics**
+ [Troubleshooting connectivity issues](#ena-connectivity-issues)
+ [Keep\-alive mechanism](#ena-keep-alive)
+ [Register read timeout](#register-read-timeout-ena)
+ [Statistics](#statistics-ena)
+ [Driver error logs in syslog](#driver-error-logs-ena)

## Troubleshooting connectivity issues<a name="ena-connectivity-issues"></a>

If you lose connectivity while enabling enhanced networking, the `ena` module might be incompatible with your instance's current running kernel\. This can happen if you install the module for a specific kernel version \(without dkms, or with an improperly configured dkms\.conf file\) and then your instance kernel is updated\. If the instance kernel that is loaded at boot time does not have the `ena` module properly installed, your instance will not recognize the network adapter and your instance becomes unreachable\.

If you enable enhanced networking for a PV instance or AMI, this can also make your instance unreachable\.

If your instance becomes unreachable after enabling enhanced networking with ENA, you can disable the `enaSupport` attribute for your instance and it will fall back to the stock network adapter\.

**To disable enhanced networking with ENA \(EBS\-backed instances\)**

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.
**Important**  
If you are using an instance store\-backed instance, you can't stop the instance\. Instead, proceed to [To disable enhanced networking with ENA \(instance store\-backed instances\)](#disable-enhanced-networking-ena-instance-store)\.

1. From your local computer, disable the enhanced networking attribute using the following command\.
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     $ aws ec2 modify-instance-attribute --instance-id instance_id --no-ena-support
     ```

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. \(Optional\) Connect to your instance and try reinstalling the `ena` module with your current kernel version by following the steps in [Enabling enhanced networking with the Elastic Network Adapter \(ENA\) on Linux instances](enhanced-networking-ena.md)\.
<a name="disable-enhanced-networking-ena-instance-store"></a>
**To disable enhanced networking with ENA \(instance store\-backed instances\)**  
If your instance is an instance store\-backed instance, create a new AMI as described in [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. Be sure to disable the enhanced networking `enaSupport` attribute when you register the AMI\.
+ [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  $ aws ec2 register-image --no-ena-support ...
  ```
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  C:\> Register-EC2Image -EnaSupport $false ...
  ```

## Keep\-alive mechanism<a name="ena-keep-alive"></a>

The ENA device posts keep\-alive events at a fixed rate \(usually once every second\)\. The ENA driver implements a watchdog mechanism, which checks for the presence of these keep\-alive messages\. If a message or messages are present, the watchdog is rearmed, otherwise the driver concludes that the device experienced a failure and then does the following:
+ Dumps its current statistics to syslog
+ Resets the ENA device
+ Resets the ENA driver state

The above reset procedure may result in some traffic loss for a short period of time \(TCP connections should be able to recover\), but should not otherwise affect the user\.

The ENA device may also indirectly request a device reset procedure, by not sending a keep\-alive notification, for example, if the ENA device reaches an unknown state after loading an irrecoverable configuration\.

Below is an example of the reset procedure:

```
[18509.800135] ena 0000:00:07.0 eth1: Keep alive watchdog timeout. // The watchdog process initiates a reset
[18509.815244] ena 0000:00:07.0 eth1: Trigger reset is on		
[18509.825589] ena 0000:00:07.0 eth1: tx_timeout: 0 // The driver logs the current statistics
[18509.834253] ena 0000:00:07.0 eth1: io_suspend: 0
[18509.842674] ena 0000:00:07.0 eth1: io_resume: 0
[18509.850275] ena 0000:00:07.0 eth1: wd_expired: 1
[18509.857855] ena 0000:00:07.0 eth1: interface_up: 1
[18509.865415] ena 0000:00:07.0 eth1: interface_down: 0
[18509.873468] ena 0000:00:07.0 eth1: admin_q_pause: 0
[18509.881075] ena 0000:00:07.0 eth1: queue_0_tx_cnt: 0
[18509.888629] ena 0000:00:07.0 eth1: queue_0_tx_bytes: 0
[18509.895286] ena 0000:00:07.0 eth1: queue_0_tx_queue_stop: 0
.......
........
[18511.280972] ena 0000:00:07.0 eth1: free uncompleted tx skb qid 3 idx 0x7 // At the end of the down process, the driver discards incomplete packets.
[18511.420112] [ENA_COM: ena_com_validate_version] ena device version: 0.10 //The driver begins its up process
[18511.420119] [ENA_COM: ena_com_validate_version] ena controller version: 0.0.1 implementation version 1
[18511.420127] [ENA_COM: ena_com_admin_init] ena_defs : Version:[b9692e8] Build date [Wed Apr  6 09:54:21 IDT 2016]
[18512.252108] ena 0000:00:07.0: Device watchdog is Enabled
[18512.674877] ena 0000:00:07.0: irq 46 for MSI/MSI-X
[18512.674933] ena 0000:00:07.0: irq 47 for MSI/MSI-X
[18512.674990] ena 0000:00:07.0: irq 48 for MSI/MSI-X
[18512.675037] ena 0000:00:07.0: irq 49 for MSI/MSI-X
[18512.675085] ena 0000:00:07.0: irq 50 for MSI/MSI-X
[18512.675141] ena 0000:00:07.0: irq 51 for MSI/MSI-X
[18512.675188] ena 0000:00:07.0: irq 52 for MSI/MSI-X
[18512.675233] ena 0000:00:07.0: irq 53 for MSI/MSI-X
[18512.675279] ena 0000:00:07.0: irq 54 for MSI/MSI-X
[18512.772641] [ENA_COM: ena_com_set_hash_function] Feature 10 isn't supported
[18512.772647] [ENA_COM: ena_com_set_hash_ctrl] Feature 18 isn't supported
[18512.775945] ena 0000:00:07.0: Device reset completed successfully // The reset process is complete
```

## Register read timeout<a name="register-read-timeout-ena"></a>

The ENA architecture suggests a limited usage of memory mapped I/O \(MMIO\) read operations\. MMIO registers are accessed by the ENA device driver only during its initialization procedure\. 

If the driver logs \(available in dmesg output\) indicate failures of read operations, this may be caused by an incompatible or incorrectly compiled driver, a busy hardware device, or hardware failure\.

Intermittent log entries that indicate failures on read operations should not be considered an issue; the driver will retry them in this case\. However, a sequence of log entries containing read failures indicate a driver or hardware problem\.

Below is an example of driver log entry indicating a read operation failure due to a timeout:

```
[ 47.113698] [ENA_COM: ena_com_reg_bar_read32] reading reg failed for timeout. expected: req id[1] offset[88] actual: req id[57006] offset[0] 
[ 47.333715] [ENA_COM: ena_com_reg_bar_read32] reading reg failed for timeout. expected: req id[2] offset[8] actual: req id[57007] offset[0] 
[ 47.346221] [ENA_COM: ena_com_dev_reset] Reg read32 timeout occurred
```

## Statistics<a name="statistics-ena"></a>

If you experience insufficient network performance or latency issues, you should retrieve the device statistics and examine them\. These statistics can be obtained using ethtool, as shown below:

```
[ec2-user ~]$ ethtool –S ethN
NIC statistics:
     tx_timeout: 0
     io_suspend: 0
     io_resume: 0
     wd_expired: 0
     interface_up: 1
     interface_down: 0
     admin_q_pause: 0
     queue_0_tx_cnt: 4329
     queue_0_tx_bytes: 1075749
     queue_0_tx_queue_stop: 0
...
```

The following command output parameters are described below:

`tx_timeout: N`  
The number of times that the Netdev watchdog was activated\.

`io_suspend: N`  
Unsupported\. This value should always be zero\.

`io_resume: N`  
Unsupported\. This value should always be zero\.

`wd_expired: N`  
The number of times that the driver did not receive the keep\-alive event in the preceding 3 seconds\.

`interface_up: N`  
The number of times that the ENA interface was brought up\.

`interface_down: N`  
The number of times that the ENA interface was brought down\.

`admin_q_pause: N`  
The admin queue is in an unstable state\. This value should always be zero\.

`queue_N_tx_cnt: N`  
The number of transmitted packets for queue *N*\.

`queue_N_tx_bytes: N`  
The number of transmitted bytes for queue *N*\.

`queue_N_tx_queue_stop: N`  
The number of times that queue *N* was full and stopped\.

`queue_N_tx_queue_wakeup: N`  
The number of times that queue *N* resumed after being stopped\.

`queue_N_tx_dma_mapping_err: N`  
Direct memory access error count\. If this value is not 0, it indicates low system resources\.

`queue_N_tx_napi_comp: N`  
The number of times the `napi` handler called `napi_complete` for queue *N*\.

`queue_N_tx_poll: N`  
The number of times the `napi` handler was scheduled for queue *N*\.

`queue_N_tx_doorbells: N`  
The number of transmission doorbells for queue *N*\.

`queue_N_tx_linearize: N`  
The number of times SKB linearization was attempted for queue *N*\.

`queue_N_tx_linearize_failed: N`  
The number of times SKB linearization failed for queue *N*\.

`queue_N_tx_prepare_ctx_err: N`  
The number of times `ena_com_prepare_tx` failed for queue *N*\. This value should always be zero; if not, see the driver logs\.

`queue_N_tx_missing_tx_comp: codeN`  
The number of packets that were left uncompleted for queue *N*\. This value should always be zero\.

`queue_N_tx_bad_req_id: N`  
Invalid `req_id` for queue *N*\. The valid `req_id` is zero, minus the `queue_size`, minus 1\. 

`queue_N_rx_cnt: N`  
The number of received packets for queue *N*\.

`queue_N_rx_bytes: N`  
The number of received bytes for queue *N*\.

`queue_N_rx_refil_partial: N`  
The number of times the driver did not succeed in refilling the empty portion of the `rx` queue with the buffers for queue *N*\. If this value is not zero, it indicates low memory resources\.

`queue_N_rx_bad_csum: N`  
The number of times the `rx` queue had a bad checksum for queue *N* \(only if `rx` checksum offload is supported\)\.

`queue_N_rx_page_alloc_fail: N`  
The number of time that page allocation failed for queue *N*\. If this value is not zero, it indicates low memory resources\.

`queue_N_rx_skb_alloc_fail: N`  
The number of time that SKB allocation failed for queue *N*\. If this value is not zero, it indicates low system resources\.

`queue_N_rx_dma_mapping_err: N`  
Direct memory access error count\. If this value is not 0, it indicates low system resources\.

`queue_N_rx_bad_desc_num: N`  
Too many buffers per packet\. If this value is not 0, it indicates usage of very small buffers\.

`queue_N_rx_small_copy_len_pkt: N`  
Optimization: For packets smaller that this threshold, which is set by `sysfs`, the packet is copied directly to the stack to avoid allocation of a new page\.

`ena_admin_q_aborted_cmd: N`  
The number of admin commands that were aborted\. This usually happens during the auto\-recovery procedure\.

`ena_admin_q_submitted_cmd: N`  
The number of admin queue doorbells\.

`ena_admin_q_completed_cmd: N`  
The number of admin queue completions\.

`ena_admin_q_out_of_space: N`  
The number of times that the driver tried to submit new admin command, but the queue was full\.

`ena_admin_q_no_completion: N`  
The number of times that the driver did not get an admin completion for a command\.

## Driver error logs in syslog<a name="driver-error-logs-ena"></a>

The ENA driver writes log messages to syslog during system boot\. You can examine these logs to look for errors if you are experiencing issues\. Below is an example of information logged by the ENA driver in syslog during system boot, along with some annotations for select messages\.

```
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  478.416939] [ENA_COM: ena_com_validate_version] ena device version: 0.10
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  478.420915] [ENA_COM: ena_com_validate_version] ena controller version: 0.0.1 implementation version 1
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.256831] ena 0000:00:03.0: Device watchdog is Enabled
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.672947] ena 0000:00:03.0: creating 8 io queues. queue size: 1024
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.680885] [ENA_COM: ena_com_init_interrupt_moderation] Feature 20 isn't supported  // Interrupt moderation is not supported by the device
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.691609] [ENA_COM: ena_com_get_feature_ex] Feature 10 isn't supported // RSS HASH function configuration is not supported by the device
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.694583] [ENA_COM: ena_com_get_feature_ex] Feature 18 isn't supported //RSS HASH input source configuration is not supported by the device 
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.697433] [ENA_COM: ena_com_set_host_attributes] Set host attribute isn't supported
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.701064] ena 0000:00:03.0 (unnamed net_device) (uninitialized): Cannot set host attributes
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  479.704917] ena 0000:00:03.0: Elastic Network Adapter (ENA) found at mem f3000000, mac addr 02:8a:3c:1e:13:b5 Queues 8
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  480.805037] EXT4-fs (xvda1): re-mounted. Opts: (null)
Jun  3 22:37:46 ip-172-31-2-186 kernel: [  481.025842] NET: Registered protocol family 10
```

**Which errors can I ignore?**  
The following warnings that may appear in your system's error logs can be ignored for the Elastic Network Adapter:

Set host attribute isn't supported  
Host attributes are not supported for this device\.

failed to alloc buffer for rx queue  
This is a recoverable error, and it indicates that there may have been a memory pressure issue when the error was thrown\.

Feature *X* isn't supported  
The referenced feature is not supported by the Elastic Network Adapter\. Possible values for *X* include:  
+ **10**: RSS Hash function configuration is not supported for this device\.
+ **12**: RSS Indirection table configuration is not supported for this device\.
+ **18**: RSS Hash Input configuration is not supported for this device\.
+ **20**: Interrupt moderation is not supported for this device\.
+ **27**: The Elastic Network Adapter driver does not support polling the Ethernet capabilities from snmpd\.

Failed to config AENQ  
The Elastic Network Adapter does not support AENQ configuration\.

Trying to set unsupported AENQ events  
This error indicates an attempt to set an AENQ events group that is not supported by the Elastic Network Adapter\.