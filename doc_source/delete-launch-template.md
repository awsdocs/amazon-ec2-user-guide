# Delete a launch template<a name="delete-launch-template"></a>

If you no longer require a launch template, you can delete it\. Deleting a launch template deletes all of its versions\. To delete a specific version of a launch template, see [Delete a launch template version](manage-launch-template-versions.md#delete-launch-template-version)\.

------
#### [ Console ]

**To delete a launch template**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Delete template**\.

1. Enter **Delete** to confirm deletion, and then choose **Delete**\.

------
#### [ AWS CLI ]

**To delete a launch template**
+ Use the [delete\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-launch-template.html) \(AWS CLI\) command and specify the launch template\.

  ```
  aws ec2 delete-launch-template --launch-template-id lt-01238c059e3466abc
  ```

------