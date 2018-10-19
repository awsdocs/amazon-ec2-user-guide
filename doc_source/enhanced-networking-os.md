# Operating System Optimizations<a name="enhanced-networking-os"></a>

To achieve the maximum network performance on instances with enhanced networking, you may need to modify the default operating system configuration\. We recommend the following configuration changes for applications that require high network performance\.

In addition to these operating system optimizations, you should also consider the maximum transmission unit \(MTU\) of your network traffic, and adjust according to your workload and network architecture\. For more information, see [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](network_mtu.md)\.

These procedures were written for Amazon Linux 2 and Amazon Linux AMI\. However, they may also work for other Linux distributions with kernel version 3\.9 or newer\. For more information, see your system\-specific documentation\.

**To optimize your Amazon Linux instance for enhanced networking**

1. Check the clock source for your instance:

   ```
   cat /sys/devices/system/clocksource/clocksource0/current_clocksource
   ```

1. If the clock source is `xen`, complete the following substeps\. Otherwise, skip to [Step 3](#post-xen-step)\.

   1. Edit the GRUB configuration and add `xen_nopvspin=1` and `clocksource=tsc` to the kernel boot options\.
      + For Amazon Linux 2, edit the `/etc/default/grub` file and add these options to the `GRUB_CMDLINE_LINUX_DEFAULT` line, as shown below:

        ```
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 xen_nopvspin=1 clocksource=tsc"
        GRUB_TIMEOUT=0
        ```
      + For Amazon Linux AMI, edit the `/boot/grub/grub.conf` file and add these options to the `kernel` line, as shown below:

        ```
        kernel /boot/vmlinuz-4.14.62-65.117.amzn1.x86_64 root=LABEL=/ console=tty1 console=ttyS0 selinux=0 nvme_core.io_timeout=4294967295 xen_nopvspin=1 clocksource=tsc
        ```

   1. \(Amazon Linux 2 only\) Rebuild your GRUB configuration file to pick up these changes:

      ```
      sudo grub2-mkconfig -o /boot/grub2/grub.cfg
      ```

1. <a name="post-xen-step"></a>If your instance type is listed as supported on [Processor State Control for Your EC2 Instance](processor_state_control.md), prevent the system from using deeper C\-states to ensure low\-latency system performance\. For more information, see [High Performance and Low Latency by Limiting Deeper C\-states](processor_state_control.md#c-states)\.

   1. Edit the GRUB configuration and add `intel_idle.max_cstate=1` to the kernel boot options\.
      + For Amazon Linux 2, edit the `/etc/default/grub` file and add this option to the `GRUB_CMDLINE_LINUX_DEFAULT` line, as shown below:

        ```
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 xen_nopvspin=1 clocksource=tsc intel_idle.max_cstate=1"
        GRUB_TIMEOUT=0
        ```
      + For Amazon Linux AMI, edit the `/boot/grub/grub.conf` file and add this option to the `kernel` line, as shown below:

        ```
        kernel /boot/vmlinuz-4.14.62-65.117.amzn1.x86_64 root=LABEL=/ console=tty1 console=ttyS0 selinux=0 nvme_core.io_timeout=4294967295 xen_nopvspin=1 clocksource=tsc intel_idle.max_cstate=1
        ```

   1. \(Amazon Linux 2 only\) Rebuild your GRUB configuration file to pick up these changes:

      ```
      sudo grub2-mkconfig -o /boot/grub2/grub.cfg
      ```

1. Ensure that your reserved kernel memory is sufficient to sustain a high rate of packet buffer allocations \(the default value may be too small\)\.

   1. Open \(as `root` or with sudo\) the `/etc/sysctl.conf` file with the editor of your choice\.

   1. Add the `vm.min_free_kbytes` line to the file with the reserved kernel memory value \(in kilobytes\) for your instance type\. As a rule of thumb, you should set this value to between 1\-3% of available system memory, and adjust this value up or down to meet the needs of your application\.

      ```
      vm.min_free_kbytes = 1048576
      ```

   1. Apply this configuration with the following command:

      ```
      sudo sysctl -p
      ```

   1. Verify that the setting was applied with the following command:

      ```
      sudo sysctl -a 2>&1 | grep min_free_kbytes
      ```

1. Reboot your instance to load the new configuration:

   ```
   sudo reboot
   ```

1. \(Optional\) Manually distribute packet receive interrupts so that they are associated with different CPUs that all belong to the same NUMA node\. Use this carefully, however, because irqbalancer is disabled globally\.
**Note**  
The configuration change in this step does not survive a reboot\.

   1. Create a file called `smp_affinity.sh` and paste the following code block into it:

      ```
      #/bin/sh
      service irqbalance stop
      affinity_values=(00000001 00000002 00000004 00000008 00000010 00000020 00000040 00000080)
      irqs=($(grep eth /proc/interrupts|awk '{print $1}'|cut -d : -f 1))
      irqLen=${#irqs[@]}
      for (( i=0; i<${irqLen}; i++ ));
      do
        echo $(printf "0000,00000000,00000000,00000000,${affinity_values[$i]}") > /proc/irq/${irqs[$i]}/smp_affinity;
        echo "IRQ ${irqs[$i]} =" $(cat /proc/irq/${irqs[$i]}/smp_affinity);
      done
      ```

   1. Run the script with the following command:

      ```
      sudo bash ./smp_affinity.sh
      ```

1. \(Optional\) If the vCPUs that handle receive IRQs are overloaded, or if your application network processing is demanding on CPU, you can offload part of the network processing to other cores with receive packet steering \(RPS\)\. Ensure that cores used for RPS belong to the same NUMA node to avoid inter\-NUMA node locks\. For example, to use cores 8\-15 for packet processing, use the following command\.
**Note**  
The configuration change in this step does not survive a reboot\.

   ```
   for i in `seq 0 7`; do echo $(printf "0000,00000000,00000000,00000000,0000ff00") | sudo tee /sys/class/net/eth0/queues/rx-$i/rps_cpus; done
   ```

1. \(Optional\) If possible, keep all processing on the same NUMA node\.

   1. Install numactl:

      ```
      sudo yum install -y numactl
      ```

   1. When you run your network processing program, bind it to a single NUMA node\. For example, the following command binds the shell script, `run.sh`, to NUMA node 0:

      ```
      numactl --cpunodebind=0 --membind=0 run.sh
      ```

   1. If you have hyperthreading enabled, you can configure your application to only use a single hardware thread per CPU core\.
      + You can view which CPU cores map to a NUMA node with the lscpu command:

        ```
        lscpu | grep NUMA
        ```

        Output:

        ```
        NUMA node(s):      2
        NUMA node0 CPU(s): 0-15,32-47
        NUMA node1 CPU(s): 16-31,48-63
        ```
      + You can view which hardware threads belong to a physical CPU with the following command:

        ```
        cat /sys/devices/system/cpu/cpu0/topology/thread_siblings_list
        ```

        Output:

        ```
        0,32
        ```

        In this example, threads 0 and 32 map to CPU 0\.
      + To avoid running on threads 32\-47 \(which are actually hardware threads of the same CPUs as 0\-15, use the following command:

        ```
        numactl --physcpubind=+0-15 --membind=0 ./run.sh
        ```

1. Use multiple elastic network interfaces for different classes of traffic\. For example, if you are running a web server that uses a backend database, use one elastic network interfaces for the web server front end, and another for the database connection\.
