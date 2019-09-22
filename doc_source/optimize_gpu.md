# Optimizing GPU Settings<a name="optimize_gpu"></a>

There are several GPU setting optimizations that you can perform to achieve the best performance on G3, G4, P2, and P3 instances\. By default, the NVIDIA driver uses an autoboost feature, which varies the GPU clock speeds\. By disabling the autoboost feature and setting the GPU clock speeds to their maximum frequency, you can consistently achieve the maximum performance with your GPU instances\. The following procedure helps you to configure the GPU settings to be persistent, disable the autoboost feature, and set the GPU clock speeds to their maximum frequency\.

**To optimize GPU settings**

1. Configure the GPU settings to be persistent\. This command can take several minutes to run\.

   ```
   [ec2-user ~]$ sudo nvidia-persistenced
   ```

1. Disable the autoboost feature for all GPUs on the instance\.

   ```
   [ec2-user ~]$ sudo nvidia-smi --auto-boost-default=0
   ```
**Note**  
GPUs on P3 and G4 instances do not support autoboost\.

1. Set all GPU clock speeds to their maximum frequency\. Use the memory and graphics clock speeds specified in the following commands\.
**Note**  
Some versions of the NVIDIA driver do not allow setting application clock speed and throw a `"Setting applications clocks is not supported for GPU …"` error, which you can ignore\.
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
   + P3 instances:

     ```
     [ec2-user ~]$ sudo nvidia-smi -ac 877,1530
     ```