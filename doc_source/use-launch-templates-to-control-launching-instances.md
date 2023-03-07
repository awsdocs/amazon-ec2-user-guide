# Use launch templates to control launching instances<a name="use-launch-templates-to-control-launching-instances"></a>

You can specify that users can only launch instances if they use a launch template, and that they can only use a specific launch template\. You can also control who can create, modify, describe, and delete launch templates and launch template versions\.

## Use launch templates to control launch parameters<a name="launch-templates-authorization"></a>

A launch template can contain all or some of the parameters to launch an instance\. When you launch an instance using a launch template, you can override parameters that are specified in the launch template\. Or, you can specify additional parameters that are not in the launch template\.

**Note**  
You cannot remove launch template parameters during launch \(for example, you cannot specify a null value for the parameter\)\. To remove a parameter, create a new version of the launch template without the parameter and use that version to launch the instance\.

To launch instances, users must have permissions to use the `ec2:RunInstances` action\. Users must also have permissions to create or use the resources that are created or associated with the instance\. You can use resource\-level permissions for the `ec2:RunInstances` action to control the launch parameters that users can specify\. Alternatively, you can grant users permissions to launch an instance using a launch template\. This enables you to manage launch parameters in a launch template rather than in an IAM policy, and to use a launch template as an authorization vehicle for launching instances\. For example, you can specify that users can only launch instances using a launch template, and that they can only use a specific launch template\. You can also control the launch parameters that users can override in the launch template\. For example policies, see [Launch templates](ExamplePolicies_EC2.md#iam-example-runinstances-launch-templates)\.

## Control the use of launch templates<a name="launch-template-permissions"></a>

By default, users do not have permissions to work with launch templates\. You can create a policy that grants users permissions to create, modify, describe, and delete launch templates and launch template versions\. You can also apply resource\-level permissions to some launch template actions to control a user's ability to use specific resources for those actions\. For more information, see the following example policies: [Example: Work with launch templates](ExamplePolicies_EC2.md#iam-example-launch-templates)\.

Take care when granting users permissions to use the `ec2:CreateLaunchTemplate` and `ec2:CreateLaunchTemplateVersion` actions\. You cannot use resource\-level permissions to control which resources users can specify in the launch template\. To restrict the resources that are used to launch an instance, ensure that you grant permissions to create launch templates and launch template versions only to appropriate administrators\.