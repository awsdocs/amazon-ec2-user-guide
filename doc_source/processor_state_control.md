# Processor state control for your EC2 instance<a name="processor_state_control"></a>

C\-states control the sleep levels that a core can enter when it is idle\. C\-states are numbered starting with C0 \(the shallowest state where the core is totally awake and executing instructions\) and go to C6 \(the deepest idle state where a core is powered off\)\.

P\-states control the desired performance \(in CPU frequency\) from a core\. P\-states are numbered starting from P0 \(the highest performance setting where the core is allowed to use Intel Turbo Boost Technology to increase frequency if possible\), and they go from P1 \(the P\-state that requests the maximum baseline frequency\) to P15 \(the lowest possible frequency\)\.

The following instance types provide the ability for an operating system to control processor C\-states and P\-states:
+ General purpose: `m4.10xlarge` \| `m4.16xlarge` \| `m5.metal` \| `m5d.metal` \| `m5zn.metal` \| `m6i.metal`
+ Compute optimized: `c4.8xlarge` \| `c5.metal` \| `c5n.metal` \| `c6i.metal`
+ Memory optimized: `r4.8xlarge` \| `r4.16xlarge` \| `r5.metal` \| `r5b.metal` \| `r5d.metal` \| `r6i.metal` \| `u-6tb1.metal` \| `u-9tb1.metal` \| `u-12tb1.metal` \| `u-18tb1.metal` \| `u-24tb1.metal` \| `x1.16xlarge` \| `x1.32xlarge` \| `x1e.8xlarge` \| `x1e.16xlarge` \| `x1e.32xlarge` \| `z1d.metal`
+ Storage optimized: `d2.8xlarge` \| `i3.8xlarge` \| `i3.16xlarge` \| `i3.metal` \| `i3en.metal` \| `h1.8xlarge` \| `h1.16xlarge`
+ Accelerated computing: `f1.16xlarge` \| `g3.16xlarge` \| `g4dn.metal` \| `p2.16xlarge` \| `p3.16xlarge`

The following instance types provide the ability for an operating system to control processor C\-states:
+ General purpose: `m5.12xlarge` \| `m5.24xlarge` \| `m5d.12xlarge` \| `m5d.24xlarge` \| `m5n.12xlarge` \| `m5n.24xlarge` \| `m5dn.12xlarge` \| `m5dn.24xlarge` \| `m6a.24xlarge` \| `m6a.48xlarge` \| `m6i.16xlarge` \| `m6i.32xlarge`
+ Compute optimized: `c5.9xlarge` \| `c5.12xlarge` \| `c5.18xlarge` \| `c5.24xlarge` \| `c5a.24xlarge` \| `c5ad.24xlarge` \| `c5d.9xlarge` \| `c5d.12xlarge` \| `c5d.18xlarge` \| `c5d.24xlarge` \| `c5n.9xlarge` \| `c5n.18xlarge` \| `c6a.24xlarge` \| `c6a.48xlarge` \| `c6i.16xlarge` \| `c6i.32xlarge`
+ Memory optimized: `r5.12xlarge` \| `r5.24xlarge` \| `r5d.12xlarge` \| `r5d.24xlarge` \| `r5n.12xlarge` \| `r5n.24xlarge` \| `r5dn.12xlarge` \| `r5dn.24xlarge` \| `r6i.16xlarge` \| `r6i.32xlarge` \| `u-6tb1.56xlarge` \| `u-6tb1.112xlarge` \| `u-9tb1.112xlarge` \| `u-12tb1.112xlarge` \| `z1d.6xlarge` \| `z1d.12xlarge`
+ Storage optimized: `d3en.12xlarge` \| `dl1.24xlarge` \| `i3en.12xlarge` \| `i3en.24xlarge` \| `i4i.metal` \| `r5b.12xlarge` \| `r5b.24xlarge`
+ Accelerated computing: `dl1.24xlarge` \| `g5.24xlarge` \| `g5.48xlarge` \| `inf1.24xlarge` \| `p3dn.24xlarge` \| `p4d.24xlarge` \| `vt1.24xlarge`

AWS Graviton processors have built\-in power saving modes and operate at a fixed frequency\. Therefore, they do not provide the ability for the operating system to control C\-states and P\-states\.

You might want to change the C\-state or P\-state settings to increase processor performance consistency, reduce latency, or tune your instance for a specific workload\. The default C\-state and P\-state settings provide maximum performance, which is optimal for most workloads\. However, if your application would benefit from reduced latency at the cost of higher single\- or dual\-core frequencies, or from consistent performance at lower frequencies as opposed to bursty Turbo Boost frequencies, consider experimenting with the C\-state or P\-state settings that are available to these instances\.

The following sections describe the different processor state configurations and how to monitor the effects of your configuration\. These procedures were written for, and apply to Amazon Linux; however, they may also work for other Linux distributions with a Linux kernel version of 3\.9 or newer\. For more information about other Linux distributions and processor state control, see your system\-specific documentation\.

**Note**  
The examples on this page use the following:  
The turbostat utility to display processor frequency and C\-state information\. The turbostat utility is available on Amazon Linux by default\.
The stress command to simulate a workload\. To install stress, first enable the EPEL repository by running sudo amazon\-linux\-extras install epel, and then run sudo yum install \-y stress\.
If the output does not display the C\-state information, include the \-\-debug option in the command \(sudo turbostat \-\-debug stress *<options>*\)\.

**Topics**
+ [Highest performance with maximum Turbo Boost frequency](#turbo-perf)
+ [High performance and low latency by limiting deeper C\-states](#c-states)
+ [Baseline performance with the lowest variability](#baseline-perf)

## Highest performance with maximum Turbo Boost frequency<a name="turbo-perf"></a>

This is the default processor state control configuration for the Amazon Linux AMI, and it is recommended for most workloads\. This configuration provides the highest performance with lower variability\. Allowing inactive cores to enter deeper sleep states provides the thermal headroom required for single or dual core processes to reach their maximum Turbo Boost potential\.

The following example shows a `c4.8xlarge` instance with two cores actively performing work reaching their maximum processor Turbo Boost frequency\.

```
[ec2-user ~]$ sudo turbostat stress -c 2 -t 10
stress: info: [30680] dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
stress: info: [30680] successful run completed in 10s
pk cor CPU    %c0  GHz  TSC SMI    %c1    %c3    %c6    %c7   %pc2   %pc3   %pc6   %pc7  Pkg_W RAM_W PKG_% RAM_%
             5.54 3.44 2.90   0   9.18   0.00  85.28   0.00   0.00   0.00   0.00   0.00  94.04 32.70 54.18  0.00
 0   0   0   0.12 3.26 2.90   0   3.61   0.00  96.27   0.00   0.00   0.00   0.00   0.00  48.12 18.88 26.02  0.00
 0   0  18   0.12 3.26 2.90   0   3.61
 0   1   1   0.12 3.26 2.90   0   4.11   0.00  95.77   0.00
 0   1  19   0.13 3.27 2.90   0   4.11
 0   2   2   0.13 3.28 2.90   0   4.45   0.00  95.42   0.00
 0   2  20   0.11 3.27 2.90   0   4.47
 0   3   3   0.05 3.42 2.90   0  99.91   0.00   0.05   0.00
 0   3  21  97.84 3.45 2.90   0   2.11
...
 1   1  10   0.06 3.33 2.90   0  99.88   0.01   0.06   0.00
 1   1  28  97.61 3.44 2.90   0   2.32
...
10.002556 sec
```

In this example, vCPUs 21 and 28 are running at their maximum Turbo Boost frequency because the other cores have entered the `C6` sleep state to save power and provide both power and thermal headroom for the working cores\. vCPUs 3 and 10 \(each sharing a processor core with vCPUs 21 and 28\) are in the `C1` state, waiting for instruction\.

In the following example, all 18 cores are actively performing work, so there is no headroom for maximum Turbo Boost, but they are all running at the "all core Turbo Boost" speed of 3\.2 GHz\.

```
[ec2-user ~]$ sudo turbostat stress -c 36 -t 10
stress: info: [30685] dispatching hogs: 36 cpu, 0 io, 0 vm, 0 hdd
stress: info: [30685] successful run completed in 10s
pk cor CPU    %c0  GHz  TSC SMI    %c1    %c3    %c6    %c7   %pc2   %pc3   %pc6   %pc7  Pkg_W RAM_W PKG_% RAM_%
            99.27 3.20 2.90   0   0.26   0.00   0.47   0.00   0.00   0.00   0.00   0.00 228.59 31.33 199.26  0.00
 0   0   0  99.08 3.20 2.90   0   0.27   0.01   0.64   0.00   0.00   0.00   0.00   0.00 114.69 18.55 99.32  0.00
 0   0  18  98.74 3.20 2.90   0   0.62
 0   1   1  99.14 3.20 2.90   0   0.09   0.00   0.76   0.00
 0   1  19  98.75 3.20 2.90   0   0.49
 0   2   2  99.07 3.20 2.90   0   0.10   0.02   0.81   0.00
 0   2  20  98.73 3.20 2.90   0   0.44
 0   3   3  99.02 3.20 2.90   0   0.24   0.00   0.74   0.00
 0   3  21  99.13 3.20 2.90   0   0.13
 0   4   4  99.26 3.20 2.90   0   0.09   0.00   0.65   0.00
 0   4  22  98.68 3.20 2.90   0   0.67
 0   5   5  99.19 3.20 2.90   0   0.08   0.00   0.73   0.00
 0   5  23  98.58 3.20 2.90   0   0.69
 0   6   6  99.01 3.20 2.90   0   0.11   0.00   0.89   0.00
 0   6  24  98.72 3.20 2.90   0   0.39
...
```

## High performance and low latency by limiting deeper C\-states<a name="c-states"></a>

C\-states control the sleep levels that a core may enter when it is inactive\. You may want to control C\-states to tune your system for latency versus performance\. Putting cores to sleep takes time, and although a sleeping core allows more headroom for another core to boost to a higher frequency, it takes time for that sleeping core to wake back up and perform work\. For example, if a core that is assigned to handle network packet interrupts is asleep, there may be a delay in servicing that interrupt\. You can configure the system to not use deeper C\-states, which reduces the processor reaction latency, but that in turn also reduces the headroom available to other cores for Turbo Boost\.

A common scenario for disabling deeper sleep states is a Redis database application, which stores the database in system memory for the fastest possible query response time\.

**To limit deeper sleep states on Amazon Linux 2**

1. Open the `/etc/default/grub` file with your editor of choice\.

   ```
   [ec2-user ~]$ sudo vim /etc/default/grub
   ```

1. Edit the `GRUB_CMDLINE_LINUX_DEFAULT` line and add the `intel_idle.max_cstate=1` and `processor.max_cstate=1` options to set `C1` as the deepest C\-state for idle cores\.

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 intel_idle.max_cstate=1 processor.max_cstate=1"
   GRUB_TIMEOUT=0
   ```

   The `intel_idle.max_cstate=1` option configures the C\-state limit for Intel\-based instances, and the `processor.max_cstate=1` option configures the C\-state limit for AMD\-based instances\. It is safe to add both options to your configuration\. This allows a single configuration to set the desired behavior on both Intel and AMD\.

1. Save the file and exit your editor\.

1.  Run the following command to rebuild the boot configuration\.

   ```
   [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

1. Reboot your instance to enable the new kernel option\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

**To limit deeper sleep states on Amazon Linux AMI**

1. Open the `/boot/grub/grub.conf` file with your editor of choice\.

   ```
   [ec2-user ~]$ sudo vim /boot/grub/grub.conf
   ```

1. Edit the `kernel` line of the first entry and add the `intel_idle.max_cstate=1` and `processor.max_cstate=1` options to set `C1` as the deepest C\-state for idle cores\.

   ```
   # created by imagebuilder
   default=0
   timeout=1
   hiddenmenu
   
   title Amazon Linux 2014.09 (3.14.26-24.46.amzn1.x86_64)
   root (hd0,0)
   kernel /boot/vmlinuz-3.14.26-24.46.amzn1.x86_64 root=LABEL=/ console=ttyS0 intel_idle.max_cstate=1  processor.max_cstate=1
   initrd /boot/initramfs-3.14.26-24.46.amzn1.x86_64.img
   ```

   The `intel_idle.max_cstate=1` option configures the C\-state limit for Intel\-based instances, and the `processor.max_cstate=1` option configures the C\-state limit for AMD\-based instances\. It is safe to add both options to your configuration\. This allows a single configuration to set the desired behavior on both Intel and AMD\.

1. Save the file and exit your editor\.

1. Reboot your instance to enable the new kernel option\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

The following example shows a `c4.8xlarge` instance with two cores actively performing work at the "all core Turbo Boost" core frequency\.

```
[ec2-user ~]$ sudo turbostat stress -c 2 -t 10
stress: info: [5322] dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
stress: info: [5322] successful run completed in 10s
pk cor CPU    %c0  GHz  TSC SMI    %c1    %c3    %c6    %c7   %pc2   %pc3   %pc6   %pc7  Pkg_W RAM_W PKG_% RAM_%
             5.56 3.20 2.90   0  94.44   0.00   0.00   0.00   0.00   0.00   0.00   0.00 131.90 31.11 199.47  0.00
 0   0   0   0.03 2.08 2.90   0  99.97   0.00   0.00   0.00   0.00   0.00   0.00   0.00  67.23 17.11 99.76  0.00
 0   0  18   0.01 1.93 2.90   0  99.99
 0   1   1   0.02 1.96 2.90   0  99.98   0.00   0.00   0.00
 0   1  19  99.70 3.20 2.90   0   0.30
...
 1   1  10   0.02 1.97 2.90   0  99.98   0.00   0.00   0.00
 1   1  28  99.67 3.20 2.90   0   0.33
 1   2  11   0.04 2.63 2.90   0  99.96   0.00   0.00   0.00
 1   2  29   0.02 2.11 2.90   0  99.98
...
```

In this example, the cores for vCPUs 19 and 28 are running at 3\.2 GHz, and the other cores are in the `C1` C\-state, awaiting instruction\. Although the working cores are not reaching their maximum Turbo Boost frequency, the inactive cores will be much faster to respond to new requests than they would be in the deeper `C6` C\-state\.

## Baseline performance with the lowest variability<a name="baseline-perf"></a>

You can reduce the variability of processor frequency with P\-states\. P\-states control the desired performance \(in CPU frequency\) from a core\. Most workloads perform better in P0, which requests Turbo Boost\. But you may want to tune your system for consistent performance rather than bursty performance that can happen when Turbo Boost frequencies are enabled\. 

Intel Advanced Vector Extensions \(AVX or AVX2\) workloads can perform well at lower frequencies, and AVX instructions can use more power\. Running the processor at a lower frequency, by disabling Turbo Boost, can reduce the amount of power used and keep the speed more consistent\. For more information about optimizing your instance configuration and workload for AVX, see [http://www\.intel\.com/content/dam/www/public/us/en/documents/white\-papers/performance\-xeon\-e5\-v3\-advanced\-vector\-extensions\-paper\.pdf](http://www.intel.com/content/dam/www/public/us/en/documents/white-papers/performance-xeon-e5-v3-advanced-vector-extensions-paper.pdf)\.

CPU idle drivers control P\-state\. Newer CPU generations require updated CPU idle drivers that correspond to the kernel level as follows:
+ Linux kernel versions 5\.6 and higher \(for example, m6i\) – Supports Intel Icelake\.
+ Linux kernel versions 5\.10 and higher \(for example, m6a\) – Supports AMD Milan\.

To detect if a running system's kernel recognizes the CPU, run the following command\.

```
if [ -d /sys/devices/system/cpu/cpu0/cpuidle ]; then echo "C-state control enabled"; else echo "Kernel cpuidle driver does not recognize this CPU generation"; fi
```

If the output of this command indicates a lack of support, we recommend that you upgrade the kernel\.

This section describes how to limit deeper sleep states and disable Turbo Boost \(by requesting the `P1` P\-state\) to provide low\-latency and the lowest processor speed variability for these types of workloads\.

**To limit deeper sleep states and disable Turbo Boost on Amazon Linux 2**

1. Open the `/etc/default/grub` file with your editor of choice\.

   ```
   [ec2-user ~]$ sudo vim /etc/default/grub
   ```

1. Edit the `GRUB_CMDLINE_LINUX_DEFAULT` line and add the `intel_idle.max_cstate=1` and `processor.max_cstate=1` options to set `C1` as the deepest C\-state for idle cores\.

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 intel_idle.max_cstate=1 processor.max_cstate=1"
   GRUB_TIMEOUT=0
   ```

   The `intel_idle.max_cstate=1` option configures the C\-state limit for Intel\-based instances, and the `processor.max_cstate=1` option configures the C\-state limit for AMD\-based instances\. It is safe to add both options to your configuration\. This allows a single configuration to set the desired behavior on both Intel and AMD\.

1. Save the file and exit your editor\.

1.  Run the following command to rebuild the boot configuration\.

   ```
   [ec2-user ~]$ grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

1. Reboot your instance to enable the new kernel option\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. When you need the low processor speed variability that the `P1` P\-state provides, run the following command to disable Turbo Boost\.

   ```
   [ec2-user ~]$ sudo sh -c "echo 1 > /sys/devices/system/cpu/intel_pstate/no_turbo"
   ```

1. When your workload is finished, you can re\-enable Turbo Boost with the following command\.

   ```
   [ec2-user ~]$ sudo sh -c "echo 0 > /sys/devices/system/cpu/intel_pstate/no_turbo"
   ```

**To limit deeper sleep states and disable Turbo Boost on Amazon Linux AMI**

1. Open the `/boot/grub/grub.conf` file with your editor of choice\.

   ```
   [ec2-user ~]$ sudo vim /boot/grub/grub.conf
   ```

1. Edit the `kernel` line of the first entry and add the `intel_idle.max_cstate=1` and `processor.max_cstate=1` options to set `C1` as the deepest C\-state for idle cores\.

   ```
   # created by imagebuilder
   default=0
   timeout=1
   hiddenmenu
   
   title Amazon Linux 2014.09 (3.14.26-24.46.amzn1.x86_64)
   root (hd0,0)
   kernel /boot/vmlinuz-3.14.26-24.46.amzn1.x86_64 root=LABEL=/ console=ttyS0 intel_idle.max_cstate=1 processor.max_cstate=1
   initrd /boot/initramfs-3.14.26-24.46.amzn1.x86_64.img
   ```

   The `intel_idle.max_cstate=1` option configures the C\-state limit for Intel\-based instances, and the `processor.max_cstate=1` option configures the C\-state limit for AMD\-based instances\. It is safe to add both options to your configuration\. This allows a single configuration to set the desired behavior on both Intel and AMD\.

1. Save the file and exit your editor\.

1. Reboot your instance to enable the new kernel option\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. When you need the low processor speed variability that the `P1` P\-state provides, run the following command to disable Turbo Boost\.

   ```
   [ec2-user ~]$ sudo sh -c "echo 1 > /sys/devices/system/cpu/intel_pstate/no_turbo"
   ```

1. When your workload is finished, you can re\-enable Turbo Boost with the following command\.

   ```
   [ec2-user ~]$ sudo sh -c "echo 0 > /sys/devices/system/cpu/intel_pstate/no_turbo"
   ```

The following example shows a `c4.8xlarge` instance with two vCPUs actively performing work at the baseline core frequency, with no Turbo Boost\.

```
[ec2-user ~]$ sudo turbostat stress -c 2 -t 10
stress: info: [5389] dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
stress: info: [5389] successful run completed in 10s
pk cor CPU    %c0  GHz  TSC SMI    %c1    %c3    %c6    %c7   %pc2   %pc3   %pc6   %pc7  Pkg_W RAM_W PKG_% RAM_%
             5.59 2.90 2.90   0  94.41   0.00   0.00   0.00   0.00   0.00   0.00   0.00 128.48 33.54 200.00  0.00
 0   0   0   0.04 2.90 2.90   0  99.96   0.00   0.00   0.00   0.00   0.00   0.00   0.00  65.33 19.02 100.00  0.00
 0   0  18   0.04 2.90 2.90   0  99.96
 0   1   1   0.05 2.90 2.90   0  99.95   0.00   0.00   0.00
 0   1  19   0.04 2.90 2.90   0  99.96
 0   2   2   0.04 2.90 2.90   0  99.96   0.00   0.00   0.00
 0   2  20   0.04 2.90 2.90   0  99.96
 0   3   3   0.05 2.90 2.90   0  99.95   0.00   0.00   0.00
 0   3  21  99.95 2.90 2.90   0   0.05
...
 1   1  28  99.92 2.90 2.90   0   0.08
 1   2  11   0.06 2.90 2.90   0  99.94   0.00   0.00   0.00
 1   2  29   0.05 2.90 2.90   0  99.95
```

The cores for vCPUs 21 and 28 are actively performing work at the baseline processor speed of 2\.9 GHz, and all inactive cores are also running at the baseline speed in the `C1` C\-state, ready to accept instructions\.