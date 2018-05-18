# Running Amazon Linux 2 as a Virtual Machine On\-Premises<a name="amazon-linux-2-virtual-machine"></a>

Use the Amazon Linux 2 virtual machine images for on\-premises development and testing\. These images are available for use on the following virtualization platforms:
+ VMWare
+ VirtualBox (Oracle VM)
+ Microsoft Hyper\-V
+ KVM

## Before you begin ##

For security reasons, the Amazon Linux 2 distribution requires a `seed.iso` disc for the first boot. This disc includes all the initial configuration information needed to boot your new virtual machine, such as the network configuration, host name, and the information required to create the users. The `seed.iso` also includes the passwords for your users.

**Important:** The 'seed.iso' disc is not the Amazon Linux 2 system image\. The Amazon Linux 2 image is available as a virtual hard drive for each supported virtualization platform.

## Create the seed.iso ##

To create a `seed.iso` follow this steps:

1. Create a new folder named `seedconfig` to contain the files that are necessary for this operation\.
1. Inside the `seedconfig` folder, create the following files: `meta-data` and `user-data`\.
1. Add the following to the `meta-data` file to specify the virtual machine's host name\.

      ```
      local-hostname: amazonlinux.onprem
      ```

1. Add the following to the `user-data` file to specify a password for the default `ec2-user` user account\. Be sure to replace the `PLAIN_TEXT_PASSWORD` placeholder with a password of your choice\.

      ```
      #cloud-config
      users:
      # A user by the name ec2-user is created in the image by default.
        - default

      chpasswd:
        list: |
          ec2-user:PLAIN_TEXT_PASSWORD
      # In the above line, do not add any spaces after 'ec2-user:'.
      ```

1. Create the `seed.iso` disc using the `meta-data` and `user-data` configuration files.

      For Linux, use a tool such as **genisoimage**. Navigate into the `seedconfig` folder and execute the following command:

      ```
      $ genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
      ```

      For macOS, use a tool such as **hdiutil**\. Navigate one level up from the `seedconfig` folder and execute the following command:

      ```
      $ hdiutil makehybrid -o seed.iso -hfs -joliet -iso -default-volume-name cidata seedconfig/
      ```

    **Note:** After creating the `seed.iso` disc, you need to mount it in your virtual CD drive\.

## Get the virtual HD ##

In your web browser, navigate to [https://cdn\.amazonlinux\.com/os\-images/latest/](https://cdn.amazonlinux.com/os-images/latest/) to find all the virtual hard drives for the virtualization platforms that we support\. Download the correct virtual hard drive for your virtualization platform\.

**IMPORTANT:** The `README.cloud-init` and `Seed.iso` files are examples, and should not be used to boot your system for the first time\.

## Set up your VM ##

Now that you have created the `seed.iso` file and downloaded the virtual HD, you can create a new runtime in your VM\. When creating a new system, use the virtual HD that you downloaded, and before booting the new system, make sure that you have mounted the `seed.iso` in your virtual CD drive\. 

When the new virtual machine boots for the first time, the Amazon Linux 2 distribution will use the `seed.iso` disc mounted in your virtual CD drive to set up to virtual machine. After this is done, you can log in using the user data that you specified in the `user-data` configuration file. After you have logged in, stop the system and remove the `seed.iso` disc from the virtual CD drive.

## Examples ##

The following example `meta-data` and `user-data` files show the other options you can set in the seed configuration files\.

+ `meta-data` file:

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

+ `user-data` file:

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

      chpasswd:
        list: |
          ec2-user:plain-text-password
      # In the above line, do not add any spaces after 'ec2-user:'.
      ```
