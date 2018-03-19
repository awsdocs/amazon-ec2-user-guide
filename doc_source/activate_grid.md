# Activate NVIDIA GRID Capabilities \(G3 Instances Only\)<a name="activate_grid"></a>

To activate the GRID capabilities on G3 instances, such as NVIDIA GRID Virtual Workstation or NVIDIA GRID Virtual Applications, you must define the product type for the driver in the `/etc/nvidia/gridd.conf` file\. For more information, see the [GRID Licensing User Guide](http://docs.nvidia.com/grid/4.6/grid-licensing-user-guide/index.html)\.

**To activate GRID features on G3 Linux instances**

1. Create the `/etc/nvidia/gridd.conf` file from the provided template file\.

   ```
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

1. Open the `/etc/nvidia/gridd.conf` file in your favorite text editor\.

1. 

   + For NVIDIA GRID Virtual Workstation, find the `FeatureType` line, and set it equal to 2\.

     ```
     FeatureType=2
     ```

   + For NVIDIA GRID Virtual Applications, find the `FeatureType` line, and set it equal to 0\. Then add a line with `IgnoreSP=TRUE`\.

     ```
     FeatureType=0
     IgnoreSP=TRUE
     ```

1. Save the file and exit\.

1. Reboot the instance to pick up the new configuration\.

   ```
   sudo reboot
   ```
**Note**  
There is a bug in the current version of the `nvidia-gridd` daemon that throws NVIDIA GRID Virtual Workstation license errors\. You can ignore these errors\.  

   ```
   nvidia-gridd: Failed to acquire NVIDIA GRID Virtual Workstation license.
   ```
You can validate that the NVIDIA GRID Virtual Workstation functionality is enabled by verifying that 4K\-resolution options are available\.