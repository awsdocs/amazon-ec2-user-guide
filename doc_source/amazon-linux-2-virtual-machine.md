# Running Amazon Linux 2 as a Virtual Machine On\-Premises<a name="amazon-linux-2-virtual-machine"></a>

Use the Amazon Linux 2 virtual machine images for on\-premises development and testing\. These images are available for use on the following virtualization platforms:
+ VMWare
+ VirtualBox (Oracle VM)
+ Microsoft Hyper\-V
+ KVM

**Before you start**

For security reasons the Amazon Linux 2 distribution requires a `seed.iso` disc for the first boot. This disc contains all the initial configuration of the system, for example: the network configuration, host name, while also having all the information necessary regarding the creation of a user(s). Meaning the `seed.iso` is the place where you put the password(s) for your user(s).

**IMPORTANT:** this iso file is not the Amazon Linux 2 system\. The Linux image is provided to you as a virtual hard drive for each individual VM that we support - more on this bellow\.

**How to make the seed.iso**

To create a `seed.iso` follow this steps:

1. Create a new folder that will contain all that is necessary for this operation\.
1. Inside the folder create for example a `seedconfig` folder\.
1. Inside the `seedconfig` folder, create the following files: `meta-data` and `user-data`\.
1. In the `meta-data` file paste the following content\.

      ```
      local-hostname: amazonlinux.onprem
      ```

1. In the `user-data` file paste the following content and replace `PLAIN_TEXT_PASSWORD` with a password of your choosing\.

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

1. Create the `seed.iso` by making sure you are outside the `seedconfig` folder, and then use one of this commands\.

      Under Linux, use for example `genisoimage`.

      ```
      $ genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
      ```

      On macOS, you can use the `hdiutil` tool\.

      ```
      $ hdiutil makehybrid -o seed.iso -hfs -joliet -iso -default-volume-name cidata seedconfig/
      ```

1.  After you run one of this commands you'll end up with the `seed.iso` that you'll have to mount in your virtual CD drive\.

**Get the virtual HD**

Visit this URL [https://cdn\.amazonlinux\.com/os\-images/latest/](https://cdn.amazonlinux.com/os-images/latest/) to find all the virtual hard drives that we support\.

**IMPORTANT:** the following files are examples, and should not be used to boot your system for the first time: `README.cloud-init`, `Seed.iso`\.

**How to setup your VM**

Now that you have created the `seed.iso` file and downloaded the virtual HD, you can go ahead and create a new runtime in your VM\. When creating a new system use the virtual HD that you just downloaded, and before booting the new system, make sure you mounted the virtual CD with the `seed.iso` that you created\. 

When you'll start the new machine for the first time, you'll see that the Amazon Linux 2 distribution will use the CD to set everything up. Once this is done, you will be able to log-in. After this step you can stop the system and remove the virtual CD.

**Examples**

Bellow you can find an example of what other options you can set in the seed configuration files\.

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
