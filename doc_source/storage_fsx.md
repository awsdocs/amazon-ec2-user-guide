# Use Amazon FSx with Amazon EC2<a name="storage_fsx"></a>

The Amazon FSx family of services makes it easy to launch, run, and scale shared storage powered by popular commercial and open\-source file systems\. You can use the *new launch instance wizard* to automatically attach the following types of Amazon FSx file systems to your Amazon EC2 instances at launch:
+ Amazon FSx for NetApp ONTAP provides fully managed shared storage in the AWS Cloud with the popular data access and management capabilities of NetApp ONTAP\. 
+ Amazon FSx for OpenZFS provides fully managed cost\-effective shared storage powered by the popular OpenZFS file system\. 

**Note**  
This functionality is available in the new launch instance wizard only\. For more information, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)
Amazon FSx for Windows File Server and Amazon FSx for Lustre file systems can't be mounted at launch\. You must mount these file systems manually after launch\.

You can choose to mount an existing file system that you created previously, or you can create a new file system to mount to an instance at launch\.

**Topics**
+ [Security groups and user data script](#sg-user-data)
+ [Mount an Amazon FSx file system at launch](#mount-fsx)

## Security groups and user data script<a name="sg-user-data"></a>

When you mount an Amazon FSx file system to an instance using the launch instance wizard, you can choose whether to automatically create and attach the security groups needed to enable access to the file system, and whether to automatically include the user data scripts needed to mount the file system and make it available for use\.

**Topics**
+ [Security groups](#fsx-sg)
+ [User data script](#fsx-user-data)

### Security groups<a name="fsx-sg"></a>

If you choose to automatically create the security groups that are needed to enable access to the file system, the launch instance wizard creates and attaches two security groups \- one security group is attached to the instance, and the other is attached to the file system\. For more information about the security group requirements, see [FSx for ONTAP file system access control with Amazon VPC](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limit-access-security-groups.html) and [FSx for OpenZFS file system access control with Amazon VPC](https://docs.aws.amazon.com/fsx/latest/OpenZFSGuide/limit-access-security-groups.html)\.

We add the tag `Name=instance-sg-1` to the security group that is created and attached to the instance\. The value in the tag is automatically incremented each time the launch instance wizard creates a security group for Amazon FSx file systems\.

The security group includes the following output rules, but no inbound rules\.


**Outbound rules**  

| Protocol type | Port number | Destination | 
| --- | --- | --- | 
| UDP | 111 | file system security group | 
| UDP | 20001 \- 20003 | file system security group | 
| UDP | 4049 | file system security group | 
| UDP | 2049 | file system security group | 
| UDP | 635 | file system security group | 
| UDP | 4045 \- 4046 | file system security group | 
| TCP | 4049 | file system security group | 
| TCP | 635 | file system security group | 
| TCP | 2049 | file system security group | 
| TCP | 111 | file system security group | 
| TCP | 4045 \- 4046 | file system security group | 
| TCP | 20001 \- 20003 | file system security group | 
| All | All | file system security group | 

The security group that is created and attached to the file system is tagged with `Name=fsx-sg-1`\. The value in the tag is automatically incremented each time the launch instance wizard creates a security group for Amazon FSx file systems\.

The security group includes the following rules\.


**Inbound rules**  

| Protocol type | Port number | Source | 
| --- | --- | --- | 
| UDP | 2049 | instance security group | 
| UDP | 20001 \- 20003 | instance security group | 
| UDP | 4049 | instance security group | 
| UDP | 111 | instance security group | 
| UDP | 635 | instance security group | 
| UDP | 4045 \- 4046 | instance security group | 
| TCP | 4045 \- 4046 | instance security group | 
| TCP | 635 | instance security group | 
| TCP | 2049 | instance security group | 
| TCP | 4049 | instance security group | 
| TCP | 20001 \- 20003 | instance security group | 
| TCP | 111 | instance security group | 


**Outbound rules**  

| Protocol type | Port number | Destination | 
| --- | --- | --- | 
| All | All | 0\.0\.0\.0/0 | 

### User data script<a name="fsx-user-data"></a>

If you choose to automatically attach user data scripts, the launch instance wizard adds the following user data to the instance\. This script installs the necessary packages, mounts the file system, and updates your instance settings so that the file system will automatically re\-mount whenever the instance restarts\.

```
#cloud-config
package_update: true
package_upgrade: true
runcmd:
- yum install -y nfs-utils
- apt-get -y install nfs-common
- svm_id_1=svm_id
- file_system_id_1=file_system_id
- vol_path_1=/vol1
- fsx_mount_point_1=/mnt/fsx/fs1
- mkdir -p "${fsx_mount_point_1}"
- if [ -z "$svm_id_1" ]; then printf "\n${file_system_id_1}.fsx.eu-north-1.amazonaws.com:/${vol_path_1} ${fsx_mount_point_1} nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0\n" >> /etc/fstab; else printf "\n${svm_id_1}.${file_system_id_1}.fsx.eu-north-1.amazonaws.com:/${vol_path_1} ${fsx_mount_point_1} nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0\n" >> /etc/fstab; fi
- retryCnt=15; waitTime=30; while true; do mount -a -t nfs4 defaults; if [ $? = 0 ] || [ $retryCnt -lt 1 ]; then echo File system mounted successfully; break; fi; echo File system not available, retrying to mount.; ((retryCnt--)); sleep $waitTime; done;
```

## Mount an Amazon FSx file system at launch<a name="mount-fsx"></a>



**To mount a new or existing Amazon FSx file system at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then choose **Launch instance** to open the launch instance wizard\.

1. In the **Application and OS Images** section, select the AMI to use\.

1. In the **Instance type** section, select the instance type\.

1. In the **Key pair** section, select an existing key pair or create a new one\.

1. In the **Network settings** section, do the following:

   1. Choose **Edit**\.

   1. If you want to **mount an existing file system**, for **Subnet**, choose the file system's preferred subnet\. We recommend that you launch the instance into the sameAvailability Zone as the file system's preferred subnet to optimizeperformance\.

      If you want to **create a new file system** to mount to an instance, for **Subnet**, choose the subnet into which to launch the instance\.
**Important**  
You must select a subnet to enable the Amazon FSx functionality in the new launch instance wizard\. If you do not select a subnet, you will not be able to mount an existing file system or create a new one\.

1. In the **Storage** section, do the following:

   1. Configure the volumes as needed\.

   1. Expand the **File systems** section and select **FSx**\.

   1. Choose **Add shared file system**\.

   1. For **File system**, select the file system to mount\.
**Note**  
The list displays all Amazon FSx for NetApp ONTAP and Amazon FSx for OpenZFS file systems in the in your account in the selected Region\.

   1. To automatically create and attach the security groups needed to enable access to the file system, select **Automatically create and attach security groups**\. If you prefer to create the security groups manually, clear the check box\. For more information, see [Security groups](#fsx-sg)\.

   1. To automatically attach the user data scripts needed to mount the file system, select **Automatically mount shared file system by attaching required user data script**\. If you prefer to provide the user data scripts manually, clear the check box\. For more information, see [User data script](#fsx-user-data)\.

1. In the **Advanced** section, configure the additional instance settings as needed\.

1. Choose **Launch**\.