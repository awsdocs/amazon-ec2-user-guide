# Working with EC2Rescue for Linux<a name="ec2rl_working"></a>

The following are common tasks to get you started using this tool\.


+ [Getting Help](#ec2rl_getting_help)
+ [Running a Module](#ec2rl_running_module)
+ [Uploading the Results](#ec2rl_uploading_results)
+ [Creating Backups](#ec2rl_creating_backups)

## Getting Help<a name="ec2rl_getting_help"></a>

EC2Rescue for Linux includes a help file that gives you information and syntax for each available command\.

**To use the help command**

+ Use the `help` command to get general help:

  ```
  [ec2-user ~]$ ./ec2rl help
  ```

**To list the available modules**

+ List all available modules:

  ```
  [ec2-user ~]$ ./ec2rl list
  ```

**To get help for a specific module**

+ List the help details for a specific command:

  ```
  [ec2-user ~]$ ./ec2rl help module_name
  ```

  Example command for showing the help file for the `dig` module:

  ```
  [ec2-user ~]$ ./ec2rl help dig
  ```

## Running a Module<a name="ec2rl_running_module"></a>

You can run an EC2Rescue for Linux using these steps\.

**To run a module**

1. Run a module:

   ```
   [ec2-user ~]$ ./ec2rl run --only-modules=module_name --arguments
   ```

   Example command, using the `dig` module to query the `amazon.com` domain:

   ```
   [ec2-user ~]$ ./ec2rl run --only-modules=dig --domain=amazon.com
   ```

1. View the results:

   ```
   [ec2-user ~]$ cat /var/tmp/ec2rl/logfile_location
   ```

   Example:

   ```
   [ec2-user ~]$ cat /var/tmp/ec2rl/2017-05-11T15_39_21.893145/mod_out/run/dig.log
   ```

## Uploading the Results<a name="ec2rl_uploading_results"></a>

If AWS Support has requested the results or to share the results from an S3 bucket, upload using the EC2Rescue for Linux CLI tool\. The output of the EC2Rescue for Linux commands should provide the commands to achieve this\.

**To upload to support**

+ Upload the results to support:

  ```
  [ec2-user ~]$ ./ec2rl upload --upload-directory=/var/tmp/ec2rl/2017-05-11T15_39_21.893145 --support-url="URLProvidedByAWSSupport"
  ```

**To upload to an S3 bucket**

+ To upload the results to an S3 bucket:

  ```
  [ec2-user ~]$ ./ec2rl upload --upload-directory=/var/tmp/ec2rl/2017-05-11T15_39_21.893145 --presigned-url="YourPresignedS3URL"
  ```
**Note**  
For more information about generating presigned URLs for Amazon S3, see [Uploading Objects Using Pre\-Signed URLs](http://docs.aws.amazon.com/AmazonS3/latest/dev/PresignedUrlUploadObject.html)\.

## Creating Backups<a name="ec2rl_creating_backups"></a>

Create a backup for your instance, one or more volumes, or a specific device ID using the following commands\.

**To back up an instance**

+ Create a backup of your instance:

  ```
  [ec2-user ~]$ ./ec2rl run --backup=ami
  ```

**To back up all volumes**

+ Create a backup of all volumes associated with the instance:

  ```
  [ec2-user ~]$ ./ec2rl run --backup=allvolumes
  ```

**To back up a volume**

+ Create a backup of a single volume:

  ```
  [ec2-user ~]$ ./ec2rl run --backup=volume ID
  ```