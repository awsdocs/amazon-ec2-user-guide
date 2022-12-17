# UEFI variables<a name="uefi-variables"></a>

When you launch an instance where the boot mode is set to UEFI, a key\-value store for variables is created\. The store can be used by UEFI and the instance operating system for storing UEFI variables\.

UEFI variables are used by the boot loader and the operating system to configure early system startup\. They allow the operating system to manage certain settings of the boot process, like the boot order, or managing the keys for UEFI Secure Boot\.

**Warning**  
You can only access UEFI variables from within an instance\. Anyone who can connect to an instance, and potentially any software running on the instance, can read the variables\. You should never store sensitive data, such as passwords or personally identifiable information, in the UEFI variable store\.

**UEFI variable persistence**
+ For instances that were launched on or before May 10, 2022, UEFI variables are wiped on reboot or stop\.
+ For instances that are launched on or after May 11, 2022, UEFI variables that are marked as non\-volatile are persisted on reboot and stop/start\.
+ Bare metal instances do not preserve UEFI non\-volatile variables across instance stop/start operations\.