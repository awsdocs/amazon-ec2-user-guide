# Developing EC2Rescue Modules<a name="ec2rl_moduledev"></a>

Modules are written in YAML, a data serialization standard\. A module's YAML file consists of a single document, representing the module and its attributes\.

## Adding Module Attributes<a name="ec2rl-adding-modules"></a>

The following table lists the available module attributes\.


****  

| Attribute | Description | 
| --- | --- | 
| name | The name of the module\. The name should be less than or equal to 18 characters in length\. | 
| version | The version number of the module\. | 
| title | A short, descriptive title for the module\. This value should be less than or equal to 50 characters in length\. | 
| helptext |  The extended description of the module\. Each line should be less than or equal to 75 characters in length\. If the module consumes arguments, required or optional, include them in the helptext value\. For example:<pre>helptext: !!str |<br />  Collect output from ps for system analysis<br />  Consumes --times= for number of times to repeat<br />  Consumes --period= for time period between repetition</pre> | 
| placement | The stage in which the module should be run\. Supported values:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html) | 
| language | The language that the module code is written in\. Supported values:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html) Python code must be compatible with both Python 2\.7\.9\+ and Python 3\.2\+\.  | 
| remediation |  Indicates whether the module supports remediation\. Supported values are `True` or `False`\. The module defaults to `False` if this is absent, making it an optional attribute for those modules that do not support remediation\.  | 
| content | The entirety of the script code\. | 
| constraint | The name of the object containing the constraint values\. | 
| domain | A descriptor of how the module is grouped or classified\. The set of included modules uses the following domains:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html) | 
| class | A descriptor of the type of task performed by the module\. The set of included modules uses the following classes: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html) | 
| distro | The list of Linux distributions that this module supports\. The set of included modules uses the following distributions: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html) | 
| required | The required arguments that the module is consuming from the CLI options\. | 
| optional | The optional arguments that the module can use\. | 
| software | The software executables used in the module\. This attribute is intended to specify software that is not installed by default\. The EC2Rescue for Linux logic ensures that these programs are present and executable before running the module\. | 
| package | The source software package for an executable\. This attribute is intended to provide extended details on the package with the software, including a URL for downloading or getting further information\. | 
| sudo | Indicates whether root access is required to run the module\.  You do not need to implement sudo checks in the module script\. If the value is true, then the EC2Rescue for Linux logic only runs the module when the executing user has root access\. | 
| perfimpact | Indicates whether the module can have significant performance impact upon the environment in which it is run\. If the value is true and the `--perfimpact=true` argument is not present, then the module is skipped\. | 
| parallelexclusive | Specifies a program that requires mutual exclusivity\. For example, all modules specifying "bpf" run in a serial manner\. | 

## Adding Environment Variables<a name="ec2rl_adding_envvars"></a>

The following table lists the available environment variables\.


****  

| Environment Variable | Description | 
| --- | --- | 
|  `EC2RL_CALLPATH`  | The path to ec2rl\.py\. This path can be used to locate the lib directory and use vendored Python modules\. | 
|  `EC2RL_WORKDIR`  |  The main tmp directory for the diagnostic tool\. Default value: `/var/tmp/ec2rl`\. | 
|  `EC2RL_RUNDIR`  |  The directory where all output is stored\. Default value: `/var/tmp/ec2rl/<date&timestamp>`\.  | 
|  `EC2RL_GATHEREDDIR`  |  The root directory for placing gathered module data\. Default value:`/var/tmp/ec2rl/<date&timestamp>/mod_out/gathered/`\.  | 
|  `EC2RL_NET_DRIVER`  |  The driver in use for the first, alphabetically ordered, non\-virtual network interface on the instance\. Examples: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html)  | 
|  `EC2RL_SUDO`  |  True if EC2Rescue for Linux is running as root; otherwise, false\.  | 
|  `EC2RL_VIRT_TYPE`  |  The virtualization type as provided by the instance metadata\. Examples: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2rl_moduledev.html)  | 
|  `EC2RL_INTERFACES`  |  An enumerated list of interfaces on the system\. The value is a string containing names, such as `eth0`, `eth1`, etc\. This is generated via the `functions.bash` and is only available for modules that have sourced it\.  | 

## Using YAML Syntax<a name="ec2rl_yamlsyntax"></a>

The following should be noted when constructing your module YAML files:
+ The triple hyphen \(`---`\) denotes the explicit start of a document\.
+ The `!ec2rlcore.module.Module` tag tells the YAML parser which constructor to call when creating the object from the data stream\. You can find the constructor inside the `module.py` file\.
+ The `!!str` tag tells the YAML parser to not attempt to determine the type of data, and instead interpret the content as a string literal\.
+ The pipe character \(`|`\) tells the YAML parser that the value is a literal\-style scalar\. In this case, the parser includes all whitespace\. This is important for modules because indentation and newline characters are kept\.
+ The YAML standard indent is two spaces, which can be seen in the following examples\. Ensure that you maintain standard indentation \(for example, four spaces for Python\) for your script and then indent the entire content two spaces inside the module file\.

## Example Modules<a name="ec2rl_example"></a>

Example one \(`mod.d/ps.yaml`\):

```
--- !ec2rlcore.module.Module
# Module document. Translates directly into an almost-complete Module object
name: !!str ps
path: !!str
version: !!str 1.0
title: !!str Collect output from ps for system analysis
helptext: !!str |
  Collect output from ps for system analysis
  Requires --times= for number of times to repeat
  Requires --period= for time period between repetition
placement: !!str run
package: 
  - !!str
language: !!str bash
content: !!str |
  #!/bin/bash
  error_trap()
  {
      printf "%0.s=" {1..80}
      echo -e "\nERROR:	"$BASH_COMMAND" exited with an error on line ${BASH_LINENO[0]}"
      exit 0
  }
  trap error_trap ERR

  # read-in shared function
  source functions.bash
  echo "I will collect ps output from this $EC2RL_DISTRO box for $times times every $period seconds."
  for i in $(seq 1 $times); do
      ps auxww
      sleep $period
  done
constraint:
  requires_ec2: !!str False
  domain: !!str performance
  class: !!str collect
  distro: !!str alami ubuntu rhel suse
  required: !!str period times
  optional: !!str
  software: !!str
  sudo: !!str False
  perfimpact: !!str False
  parallelexclusive: !!str
```