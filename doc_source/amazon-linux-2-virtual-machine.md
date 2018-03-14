# Running Amazon Linux 2 as a Virtual Machine On\-Premises<a name="amazon-linux-2-virtual-machine"></a>

Use the Amazon Linux 2 virtual machine images for on\-premises development and testing\. These images are available for use on the following virtualization platforms:

+ VMWare

+ Oracle VM VirtualBox

+ Microsoft Hyper\-V

+ KVM

**To run Amazon Linux on a virtual machine**

1. Download the VM image for your virtualization platform from [https://cdn\.amazonlinux\.com/os\-images/latest/](https://cdn.amazonlinux.com/os-images/latest/)\.

1. Create a cloud\-init configuration ISO\.

   1. Create `meta-data` and `user-data` configuration files\. The following is an example `meta-data` configuration file:

      ```
      local-hostname: amazonlinux.onprem
      
      # eth0 is the default network interface enabled in the image. You can configure static network settings with an entry like below.
      #network-interfaces: |
      #  iface eth0 inet static
      #  address 192.168.1.10
      #  network 192.168.1.0
      #  netmask 255.255.255.0
      #  broadcast 192.168.1.255
      #  gateway 192.168.1.254
      ```

      The following is an example `user-data` configuration file, which creates multiple users and provides access mechanisms for each \(plaintext password, hashed password, and a key pair\)\.

      ```
      #cloud-config
      # vim:syntax=yaml
      users:
      # A user by the name ec2-user is created in the image by default.
        - default
      # The following entry creates user1 and assigns a plain text password.
      # Please note that the use of a plain text password is not recommended from security best practises standpoint.
        - name: user1
          groups: sudo
          sudo: ['ALL=(ALL) NOPASSWD:ALL']
          plain_text_passwd: plain-text-password
          lock_passwd: false
      # The following entry creates user2 and attaches a hashed password to the user. Hashed passwords can be generated with:
      # python -c 'import crypt,getpass; print crypt.crypt(getpass.getpass())'
        - name: user2
          passwd: hashed-password
          lock_passwd: false
      # The following entry creates user3, disables password-based login and enables an SSH public key.
        - name: user3
          ssh-authorized-keys:
                  - ssh-public-key-information
          lock_passwd: true
      ssh_pwauth: True
      chpasswd:
        list: |
          ec2-user:plain-text-password
      # In the above line, do not add any spaces after 'ec2-user:'.
      ```

      For more information about supported directives, see [http://cloudinit\.readthedocs\.io/en/latest/topics/modules\.html](http://cloudinit.readthedocs.io/en/latest/topics/modules.html)\.

   1. Create an ISO image\.

      For Linux, use a tool such as genisoimage to create an ISO image from the `user-data` and `meta-data` configuration files\. The following example creates `seed.iso`:

      ```
      $ genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
      ```

      For macOS, you can use the hdiutil tool\. Copy the `user-data` and `meta-data` configuration files to a folder, and then specify the folder name in the command\. The following example creates `seed.iso` from the files in the `seedconfig/` directory:

      ```
      $ hdiutil makehybrid -o seed.iso -hfs -joliet -iso -default-volume-name cidata seedconfig/
      ```

1. Create a virtual machine using the VM image that you downloaded, and attach the configuration ISO that you created\. Connect to your virtual machine using the information that you provided in the `user-data` configuration file\. For more information, see the documentation for your virtualization platform\.
**Important**  
The ISO must be attached to the virtual machine on first boot so that the configuration can be applied\.
