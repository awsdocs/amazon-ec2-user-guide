# Troubleshooting<a name="ei-troubleshooting"></a>

The following are common errors and troubleshooting steps\.

**Topics**
+ [Issues Launching Accelerators](#ei-issues)
+ [Resolving Configuration Issues](#ei-activation)
+ [Resolving Connectivity Issues](#ei-resolving-connectivity)
+ [Resolving Unhealthy Status Issues](#ei-unhealthy-status)
+ [Stop and Start the Instance](#ei-stop-start)
+ [Troubleshooting Model Performance](#troubleshoot-model-performance)
+ [Submitting Feedback](#ei-feedback)

## Issues Launching Accelerators<a name="ei-issues"></a>

Ensure that you are launching in a Region where Amazon EI accelerators are available\. For more information, see the [Region Table](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\.

## Resolving Configuration Issues<a name="ei-activation"></a>

If you launched your instance with the Deep Learning AMI \(DLAMI\), run `python ~/anaconda3/bin/EISetupValidator.py` to verify that the instance is correctly configured to use the Amazon EI service\. Or, you can download the [ script](https://s3.console.aws.amazon.com/s3/buckets/amazonei/) and execute `'python EISetupValidator.py`\.

## Resolving Connectivity Issues<a name="ei-resolving-connectivity"></a>

If you are unable to successfully connect to accelerators, verify that you have completed the following:
+ You have set up a VPC endpoint for Amazon EI for the subnet in which you have launched your instance\.
+ You have configured security groups for the instance and VPC endpoints with outbound rules that allow communications for HTTPS \(Port 443\)\. You have configured the VPC endpoint security group with an inbound rule that allows HTTPS traffic\.
+ You have added an IAM instance role with the "elastic\-inference:Connect" permission to the instance from which you are connecting to the accelerator\.
+ You have checked CloudWatch Logs to verify that your accelerator is healthy\. The EC2 instance details from the Amazon EC2 console contain a link to CloudWatch, which allows you to view the health of its associated accelerator\.

## Resolving Unhealthy Status Issues<a name="ei-unhealthy-status"></a>

If the Amazon EI accelerator is in an unhealthy state, the following are troubleshooting steps that you can use to resolve the issue\. 

## Stop and Start the Instance<a name="ei-stop-start"></a>

If your Amazon EI accelerator is in an unhealthy state, stopping and starting it again is the simplest option\. For more information, see [Stopping and Starting Your Instances](Stop_Start.md#starting-stopping-instances)\.

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. If you have any data to preserve on instance store volumes, make sure to back it up to persistent storage\.

## Troubleshooting Model Performance<a name="troubleshoot-model-performance"></a>

Amazon EI accelerates operations defined by frameworks like TensorFlow and MXNet\. While Amazon EI accelerates most neural network, math, array manipulation, and control flow operators, there are many operators that Amazon EI does not accelerate\. These include training\-related operators, input/output operators, and some operators in contrib\. 

When a model contains operators that Amazon EI does not accelerate, the framework runs them on the instance\. The frequency and location of these operators within a model graph can have an impact on the model's inference performance with Amazon EI accelerators\. If your model is known to benefit from GPU acceleration and does not perform well on Amazon EI, contact AWS Support or amazon\-ei\-feedback@amazon\.com\. 

## Submitting Feedback<a name="ei-feedback"></a>

Contact AWS Support or send feedback to: amazon\-ei\-feedback@amazon\.com\. 