# How the AWS binary blob is created<a name="aws-binary-blob-creation"></a>

You can use the following steps to customize the UEFI Secure Boot variables during AMI creation\. The KEK that is used in these steps is current as of September 2021\. If Microsoft updates the KEK, you must use the latest KEK\.

**To create the AWS binary blob**

1. Create an empty PK signature list\.

   ```
   touch empty_key.crt
   cert-to-efi-sig-list empty_key.crt PK.esl
   ```

1. Download the KEK certificates\.

   ```
   https://go.microsoft.com/fwlink/?LinkId=321185
   ```

1. Wrap the KEK certificates in a UEFI signature list \(`siglist`\)\.

   ```
   sbsiglist --owner 77fa9abd-0359-4d32-bd60-28f4e78f784b --type x509 --output MS_Win_KEK.esl MicCorKEKCA2011_2011-06-24.crt 
   ```

1. Download Microsoft's db certificates\.

   ```
   https://www.microsoft.com/pkiops/certs/MicWinProPCA2011_2011-10-19.crt
   https://www.microsoft.com/pkiops/certs/MicCorUEFCA2011_2011-06-27.crt
   ```

1. Generate the db signature list\.

   ```
   sbsiglist --owner 77fa9abd-0359-4d32-bd60-28f4e78f784b --type x509 --output MS_Win_db.esl MicWinProPCA2011_2011-10-19.crt
   sbsiglist --owner 77fa9abd-0359-4d32-bd60-28f4e78f784b --type x509 --output MS_UEFI_db.esl MicCorUEFCA2011_2011-06-27.crt
   cat MS_Win_db.esl MS_UEFI_db.esl > MS_db.esl
   ```

1. Download an updated dbx change request from the following link\.

   ```
   https://uefi.org/revocationlistfile
   ```

1. The dbx change request that you downloaded in the previous step is already signed with the Microsoft KEK, so you need to strip or unpack it\. You can use the following links\.

   ```
   https://gist.github.com/out0xb2/f8e0bae94214889a89ac67fceb37f8c0
   ```

   ```
   https://support.microsoft.com/en-us/topic/microsoft-guidance-for-applying-secure-boot-dbx-update-e3b9e4cb-a330-b3ba-a602-15083965d9ca
   ```

1. Build a UEFI variable store using the `uefivars.py` script\.

   ```
   ./uefivars.py -i none -o aws -O uefiblob-microsoft-keys-empty-pk.bin -P ~/PK.esl -K ~/MS_Win_KEK.esl --db ~/MS_db.esl  --dbx ~/dbx-2021-April.bin 
   ```

1. Check the binary blob and the UEFI variable store\.

   ```
   ./uefivars.py -i aws -I uefiblob-microsoft-keys-empty-pk.bin -o json | less
   ```

1. You can update the blob by passing it to the same tool again\.

   ```
   ./uefivars.py -i aws -I uefiblob-microsoft-keys-empty-pk.bin -o aws -O uefiblob-microsoft-keys-empty-pk.bin -P ~/PK.esl -K ~/MS_Win_KEK.esl --db ~/MS_db.esl  --dbx ~/dbx-2021-April.bin						
   ```

   Expected output

   ```
   Replacing PK
   Replacing KEK
   Replacing db
   Replacing dbx
   ```