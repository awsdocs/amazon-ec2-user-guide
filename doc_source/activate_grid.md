# Activate NVIDIA GRID Virtual Applications on G3 Instances<a name="activate_grid"></a>

To activate the GRID Virtual Applications on G3 instances \(NVIDIA GRID Virtual Workstation is enabled by default\), you must define the product type for the driver in the `/etc/nvidia/gridd.conf` file\.

**To activate GRID Virtual Applications**

1. Create the `/etc/nvidia/gridd.conf` file from the provided template file\.

   ```
   [ec2-user ~]$ sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

1. Open the `/etc/nvidia/gridd.conf` file in your favorite text editor\.

1. Find the `FeatureType` line, and set it equal to 0\. Then add a line with `IgnoreSP=TRUE`\.

   ```
   FeatureType=0
   IgnoreSP=TRUE
   ```

1. Save the file and exit\.

1. Reboot the instance to pick up the new configuration\.

   ```
   [ec2-user ~]$ sudo reboot
   ```