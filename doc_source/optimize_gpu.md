# Optimizing GPU settings<a name="optimize_gpu"></a>

There are several GPU setting optimizations that you can perform to achieve the best performance on G3, G4, P2, P3, P3dn, and P4 instances\. With some of these instance types, the NVIDIA driver uses an autoboost feature, which varies the GPU clock speeds\. By disabling autoboost and setting the GPU clock speeds to their maximum frequency, you can consistently achieve the maximum performance with your GPU instances\. The following procedure helps you to configure the GPU settings to be persistent, disable the autoboost feature if needed, and set the GPU clock speeds to their maximum frequency\.

**To optimize GPU settings**

1. Configure the GPU settings to be persistent\. This command can take several minutes to run\.

   ```
   [ec2-user ~]$ sudo nvidia-persistenced
   ```

1. G2, G3, and P2 instances: Disable the autoboost feature for all GPUs on the instance\.
**Note**  
GPUs on G4, P3, P3dn, and P4 instances do not support autoboost\.

   ```
   [ec2-user ~]$ sudo nvidia-smi --auto-boost-default=0
   ```

1. Set all GPU clock speeds to their maximum frequency\. Use the memory and graphics clock speeds specified in the following commands\.

   Some versions of the NVIDIA driver do not support setting the application clock speed, and display the error `"Setting applications clocks is not supported for GPU..."`, which you can ignore\.
   + G3 instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 2505,1177
     ```
   + G4 instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 5001,1590
     ```
   + P2 instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 2505,875
     ```
   + P3 and P3dn instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 877,1530
     ```
   + P4 instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 1215,1410
     ```