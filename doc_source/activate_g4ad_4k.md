# Setting up Dual 4K displays on G4ad<a name="activate_g4ad_4k"></a>

**Launch a G4ad instance**

1. Connect to your Linux instance to get the PCI Bus address of the GPU you want to target for dual 4K \(2x4k\):

   ```
   lspci -vv | grep -i amd
   ```

   You will get output similar to the following:

   ```
   00:1e.0 Display controller: Advanced Micro Devices, Inc. [*AMD*/ATI] Device 7362 (rev c3)
   Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Device 0a34
   ```

1. Note the PCI bus address is 00:1e\.0 in the above output\. Create a file named `/etc/modprobe.d/amdgpu.conf` and add:

   ```
   options amdgpu virtual_display=0000:00:1e.0,2
   ```

1. Follow the instructions [here](install-amd-driver.md) to install the AMD drivers on Linux\. If you already have the AMD GPU driver installed, you will need to rebuild the amdgpu kernel modules through dkms\.

1. Use the below xorg\.conf file to define the dual \(2x4K\) screen topology and save the file in `/etc/X11/xorg.conf:`

   ```
   ~$ cat /etc/X11/xorg.conf 
   Section "ServerLayout"
       Identifier     "Layout0"
       Screen          0 "Screen0"
       Screen        1 "Screen1"
       InputDevice     "Keyboard0" "CoreKeyboard"
       InputDevice     "Mouse0" "CorePointer"
       Option          "Xinerama" "1"
   EndSection
   Section "Files"
       ModulePath "/opt/amdgpu/lib64/xorg/modules/drivers"
       ModulePath "/opt/amdgpu/lib/xorg/modules"
       ModulePath "/opt/amdgpu-pro/lib/xorg/modules/extensions"
       ModulePath "/opt/amdgpu-pro/lib64/xorg/modules/extensions"
       ModulePath "/usr/lib64/xorg/modules"
       ModulePath "/usr/lib/xorg/modules"
   EndSection
   Section "InputDevice"
       # generated from default
       Identifier     "Mouse0"
       Driver         "mouse"
       Option         "Protocol" "auto"
       Option         "Device" "/dev/psaux"
       Option         "Emulate3Buttons" "no"
       Option         "ZAxisMapping" "4 5"
   EndSection
   Section "InputDevice"
       # generated from default
       Identifier     "Keyboard0"
       Driver         "kbd"
   EndSection
   
   Section "Monitor"
       Identifier     "Virtual"
       VendorName     "Unknown"
       ModelName      "Unknown"
       Option         "Primary" "true"
   EndSection
   
   Section "Monitor"
       Identifier     "Virtual-1"
       VendorName     "Unknown"
       ModelName      "Unknown"
       Option         "RightOf" "Virtual"
   EndSection
   
   Section "Device"
       Identifier     "Device0"
       Driver         "amdgpu"
       VendorName     "AMD"
       BoardName      "Radeon MxGPU V520"
       BusID          "PCI:0:30:0"
   EndSection
   
   Section "Device"
       Identifier     "Device1"
       Driver         "amdgpu"
       VendorName     "AMD"
       BoardName      "Radeon MxGPU V520"
       BusID          "PCI:0:30:0"
   EndSection
   
   Section "Extensions"
       Option         "DPMS" "Disable"
   EndSection
   
   Section "Screen"
       Identifier     "Screen0"
       Device         "Device0"
       Monitor        "Virtual"
       DefaultDepth   24
       Option         "AllowEmptyInitialConfiguration" "True"
       SubSection "Display"
           Virtual    3840 2160
           Depth      32
       EndSubSection
   EndSection
   
   Section "Screen"
       Identifier     "Screen1"
       Device         "Device1"
       Monitor        "Virtual"
       DefaultDepth   24
       Option         "AllowEmptyInitialConfiguration" "True"
       SubSection "Display"
           Virtual    3840 2160
           Depth      32
       EndSubSection
   EndSection
   ```

1. Set up DCV by following the instructions in setting up an [interactive desktop](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-amd-driver.html#amd-interactive-desktop)\.

1. After the DCV set up is complete, reboot\.

1. Confirm that the driver is functional:

   ```
    dmesg | grep amdgpu
   ```

   The response should look like the following:

   ```
   Initialized amdgpu
   ```

1. You should see in the output for `DISPLAY=:0 xrandr -q` that you have 2 virtual displays connected:

   ```
   ~$ DISPLAY=:0 xrandr -q
   Screen 0: minimum 320 x 200, current 3840 x 1080, maximum 16384 x 16384
   Virtual connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
    4096x3112  60.00  
    3656x2664  59.99  
    4096x2160  60.00  
    3840x2160  60.00  
    1920x1200  59.95  
    1920x1080  60.00 
    1600x1200  59.95  
    1680x1050  60.00  
    1400x1050  60.00  
    1280x1024  59.95  
    1440x900 59.99  
    1280x960 59.99  
    1280x854 59.95  
    1280x800 59.96  
    1280x720 59.97  
    1152x768 59.95  
    1024x768 60.00 59.95  
    800x600  60.32 59.96 56.25  
    848x480  60.00 59.94  
    720x480  59.94  
    640x480  59.94 59.94  
   Virtual-1 connected 1920x1080+1920+0 (normal left inverted right x axis y axis) 0mm x 0mm
    4096x3112  60.00  
    3656x2664  59.99  
    4096x2160  60.00  
    3840x2160  60.00  
    1920x1200  59.95  
    1920x1080  60.00 
    1600x1200  59.95  
    1680x1050  60.00  
    1400x1050  60.00  
    1280x1024  59.95  
    1440x900 59.99  
    1280x960 59.99  
    1280x854 59.95  
    1280x800 59.96  
    1280x720 59.97  
    1152x768 59.95  
    1024x768 60.00 59.95  
    800x600  60.32 59.96 56.25  
    848x480  60.00 59.94  
    720x480  59.94
   640x480  59.94 59.94
   ```

1. When you connect into DCV, change the resolution to 2x4K, confirming the dual monitor support is registered by DCV\.  
![\[DCV resolution changes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/dm-dcv-example.png)