# Terminate an EC2 Serial Console session<a name="terminate-serial-console-session.title"></a>

The way to terminate a serial console session depends on the client\.

**Browser\-based client**  
To terminate the serial console session, close the serial console in\-browser terminal window\.

**Standard OpenSSH client**  
To terminate the serial console session, use the following command to close the SSH connection\. This command must be run immediately following a new line\.

```
$ ~.
```

**Note**  
The command that you use for closing an SSH connection might be different depending on the SSH client that you're using\.