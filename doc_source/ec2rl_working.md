# Work with EC2Rescue for Linux<a name="ec2rl_working"></a>

The following are common tasks you can perform to get started using this tool\.

**Topics**
+ [Run EC2Rescue for Linux](#ec2rl_running_module)
+ [Upload the results](#ec2rl_uploading_results)
+ [Create backups](#ec2rl_creating_backups)
+ [Get help](#ec2rl_getting_help)

## Run EC2Rescue for Linux<a name="ec2rl_running_module"></a>

You can run EC2Rescue for Linux as shown in the following examples\.

**Example: Run all modules**  
To run all modules, run EC2Rescue for Linux with no options:  

```
./ec2rl run
```
Some modules require root access\. If you are not a root user, use sudo to run these modules as follows:  

```
 sudo ./ec2rl run
```

**Example: Run a specific module**  
To run only specific modules, use the \-\-only\-modules parameter:   

```
./ec2rl run --only-modules=module_name --arguments
```
For example, this command runs the dig module to query the `amazon.com` domain:  

```
./ec2rl run --only-modules=dig --domain=amazon.com
```

**Example: View the results**  
You can view the results in `/var/tmp/ec2rl`:  

```
cat /var/tmp/ec2rl/logfile_location
```
For example, view the log file for the dig module:  

```
cat /var/tmp/ec2rl/2017-05-11T15_39_21.893145/mod_out/run/dig.log
```

## Upload the results<a name="ec2rl_uploading_results"></a>

If AWS Support has requested the results or to share the results from an S3 bucket, upload them using the EC2Rescue for Linux CLI tool\. The output of the EC2Rescue for Linux commands should provide the commands that you need to use\.

**Example: Upload results to AWS Support**  

```
./ec2rl upload --upload-directory=/var/tmp/ec2rl/2017-05-11T15_39_21.893145 --support-url="URLProvidedByAWSSupport"
```

**Example: Upload results to an S3 bucket**  

```
./ec2rl upload --upload-directory=/var/tmp/ec2rl/2017-05-11T15_39_21.893145 --presigned-url="YourPresignedS3URL"
```
For more information about generating pre\-signed URLs for Amazon S3, see [Uploading Objects Using Pre\-Signed URLs](https://docs.aws.amazon.com/AmazonS3/latest/dev/PresignedUrlUploadObject.html)\.

## Create backups<a name="ec2rl_creating_backups"></a>

Create a backup for your instance, one or more volumes, or a specific device ID using the following commands\.

**Example: Back up an instance using an Amazon Machine Image \(AMI\)**  

```
./ec2rl run --backup=ami
```

**Example: Back up all volumes associated with the instance**  

```
./ec2rl run --backup=allvolumes
```

**Example: Back up a specific volume**  

```
./ec2rl run --backup=volumeID
```

## Get help<a name="ec2rl_getting_help"></a>

EC2Rescue for Linux includes a help file that gives you information and syntax for each available command\.

**Example: Display the general help**  

```
./ec2rl help
```

**Example: List the available modules**  

```
./ec2rl list
```

**Example: Display the help for a specific module**  

```
./ec2rl help module_name
```
For example, use the following command to show the help file for the dig module:  

```
./ec2rl help dig
```