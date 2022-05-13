# Create three key pairs<a name="uefi-secure-boot-create-three-key-pairs"></a>

UEFI Secure Boot is based on the following three key databases, which are used in a chain of trust: the platform key \(PK\), the key exchange key \(KEK\), and the signature database \(db\)\.¹ 

You create each key on the instance\. To prepare the public keys in a format that is valid for the UEFI Secure Boot standard, you create a certificate for each key\. `DER` defines the SSL format \(binary encoding of a format\)\. You then convert each certificate into a UEFI signature list, which is the binary format that is understood by UEFI Secure Boot\. And finally, you sign each certificate with the relevant key\.

**Topics**
+ [Prepare to create the key pairs](#uefisb-prepare-to-create-key-pairs)
+ [Key pair 1: Create the platform key \(PK\)](#uefisb-create-key-pair-1)
+ [Key pair 2: Create the key exchange key \(KEK\)](#uefisb-create-key-pair-2)
+ [Key pair 3: Create the signature database \(db\)](#uefisb-create-key-pair-3.title)
+ [Sign the boot image \(kernel\) with the private key](#uefi-secure-boot-sign-kernel)

## Prepare to create the key pairs<a name="uefisb-prepare-to-create-key-pairs"></a>

Before creating the key pairs, create a globally unique identifier \(GUID\) to be used in key generation\.

1. [Connect to the instance\.](AccessingInstances.md)

1. Run the following command in a shell prompt\.

   ```
   uuidgen --random > GUID.txt
   ```

## Key pair 1: Create the platform key \(PK\)<a name="uefisb-create-key-pair-1"></a>

The PK is the root of trust for UEFI Secure Boot instances\. The private PK is used to update the KEK, which in turn can be used to add authorized keys to the signature database \(db\)\.

The X\.509 standard is used for creating the key pair\. For information about the standard, see [X\.509](https://en.wikipedia.org/wiki/X.509) on *Wikipedia*\.

**To create the PK**

1. Create the key\. You must name the variable `PK`\.

   ```
   openssl req -newkey rsa:4096 -nodes -keyout PK.key -new -x509 -sha256 -days 3650 -subj "/CN=Platform key/" -out PK.crt
   ```

   The following parameters are specified:
   + `-keyout PK.key` – The private key file\.
   + `-days 3650` – The number of days that the certificate is valid\.
   + `-out PK.crt` – The certificate that is used to create the UEFI variable\.
   + `CN=Platform key` – The common name \(CN\) for the key\. You can enter the name of your own organization instead of *Platform key*\.

1. Create the certificate\.

   ```
   openssl x509 -outform DER -in PK.crt -out PK.cer
   ```

1. Convert the certificate into a UEFI signature list\.

   ```
   cert-to-efi-sig-list -g "$(< GUID.txt)" PK.crt PK.esl
   ```

1. Sign the UEFI signature list with the private PK \(self\-signed\)\.

   ```
   sign-efi-sig-list -g "$(< GUID.txt)" -k PK.key -c PK.crt PK PK.esl PK.auth
   ```

## Key pair 2: Create the key exchange key \(KEK\)<a name="uefisb-create-key-pair-2"></a>

The private KEK is used to add keys to the db, which is the list of authorized signatures to boot on the system\. 

**To create the KEK**

1. Create the key\.

   ```
   openssl req -newkey rsa:4096 -nodes -keyout KEK.key -new -x509 -sha256 -days 3650 -subj "/CN=Key Exchange Key/" -out KEK.crt
   ```

1. Create the certificate\.

   ```
   openssl x509 -outform DER -in KEK.crt -out KEK.cer
   ```

1. Convert the certificate into a UEFI signature list\.

   ```
   cert-to-efi-sig-list -g "$(< GUID.txt)" KEK.crt KEK.esl
   ```

1. Sign the signature list with the private PK\.

   ```
   sign-efi-sig-list -g "$(< GUID.txt)" -k PK.key -c PK.crt KEK KEK.esl KEK.auth
   ```

## Key pair 3: Create the signature database \(db\)<a name="uefisb-create-key-pair-3.title"></a>

The db list contains authorized keys that are authorized to be booted on the system\. To modify the list, the private KEK is necessary\. Boot images will be signed with the private key that is created in this step\.

**To create the db**

1. Create the key\.

   ```
   openssl req -newkey rsa:4096 -nodes -keyout db.key -new -x509 -sha256 -days 3650 -subj "/CN=Signature Database key/" -out db.crt
   ```

1. Create the certificate\.

   ```
   openssl x509 -outform DER -in db.crt -out db.cer
   ```

1. Convert the certificate into a UEFI signature list\.

   ```
   cert-to-efi-sig-list -g "$(< GUID.txt)" db.crt db.esl
   ```

1. Sign the signature list with the private KEK\.

   ```
   sign-efi-sig-list -g "$(< GUID.txt)" -k KEK.key -c KEK.crt db db.esl db.auth
   ```

## Sign the boot image \(kernel\) with the private key<a name="uefi-secure-boot-sign-kernel"></a>

For Ubuntu 22\.04, the following images require signatures\.

```
/boot/efi/EFI/ubuntu/shimx64.efi
/boot/efi/EFI/ubuntu/mmx64.efi
/boot/efi/EFI/ubuntu/grubx64.efi
/boot/vmlinuz
```

**To sign an image**  
Use the following syntax to sign an image\.

```
sbsign --key db.key --cert db.crt --output /boot/vmlinuz /boot/vmlinuz
```

**Note**  
You must sign all new kernels\. *`/boot/vmlinuz`* will usually symlink to the last installed kernel\.

Refer to the documentation for your distribution to find out about your boot chain and required images\.

¹ Thanks to the ArchWiki community for all of the work they have done\. The commands for creating the PK, creating the KEK, creating the DB, and signing the image are from [Creating keys](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Creating_keys), authored by the ArchWiki Maintenance Team and/or the ArchWiki contributors\.