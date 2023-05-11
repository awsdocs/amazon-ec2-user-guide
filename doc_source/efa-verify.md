# Verify the EFA installer using a checksum<a name="efa-verify"></a>

You can optionally verify the EFA tarball \(\.tar\.gz file\) using an MD5 or SHA256 checksum\. We recommend that you do this to verify the identity of the software publisher and to check that the application has not been altered or corrupted since it was published\.

**To verify the tarball**  
Use the **md5sum** utility for the MD5 checksum, or the **sha256sum** utility for the SHA256 checksum, and specify the tarball filename\. You must run the command from the directory in which you saved the tarball file\.
+ MD5

  ```
  $  md5sum tarball_filename.tar.gz
  ```
+ SHA256

  ```
  $  sha256sum tarball_filename.tar.gz
  ```

The commands should return a checksum value in the following format\.

```
checksum_value tarball_filename.tar.gz
```

Compare the checksum value returned by the command with the checksum value provided in the table below\. If the checksums match, then it is safe to run the installation script\. If the checksums do not match, do not run the installation script, and contact AWS Support\.

For example, the following command verifies the EFA 1\.9\.4 tarball using the SHA256 checksum\.

```
$  sha256sum aws-efa-installer-1.9.4.tar.gz
```

```
1009b5182693490d908ef0ed2c1dd4f813cc310a5d2062ce9619c4c12b5a7f14 aws-efa-installer-1.9.4.tar.gz
```

The following table lists the checksums for recent versions of EFA\.


| Version | Download URL | Checksums | 
| --- | --- | --- | 
| EFA 1\.23\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.23\.0\.tar\.gz  |  **MD5: **`38a6d7c1861f5038dba4e441ca7683ca` **SHA256: **`555d497a60f22e3857fdeb3dfc53aa86d05926023c68c916d15d2dc3df6525bd`  | 
| EFA 1\.22\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.22\.1\.tar\.gz  |  **MD5: **`600c0ad7cdbc06e8e846cb763f92901b` **SHA256: **`f90f3d5f59c031b9a964466b5401e86fd0429272408f6c207c3f9048254e9665`  | 
| EFA 1\.22\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.22\.0\.tar\.gz  |  **MD5: **`8f100c93dc8ab519c2aeb5dab89e98f8` **SHA256: **`f329e7d54a86a03ea51da6ea9a5b68fb354fbae4a57a02f9592e21fce431dc3a`  | 
| EFA 1\.21\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.21\.0\.tar\.gz  |  **MD5: **`959ccc3a4347461909ec02ed3ba7c372` **SHA256: **`c64e6ca34ccfc3ebe8e82d08899ae8442b3ef552541cf5429c43d11a04333050`  | 
| EFA 1\.20\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.20\.0\.tar\.gz  |  **MD5: **`7ebfbb8e85f1b94709df4ab3db47913b` **SHA256: **`aeefd2681ffd5c4c631d1502867db5b831621d6eb85b61fe3ec80df983d1dcf0`  | 
| EFA 1\.19\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.19\.0\.tar\.gz  |  **MD5: **`2fd45324953347ec5518da7e3fefa0ec` **SHA256: **`99b77821b9e72c8dea015cc92c96193e8db307deee05b91a58094cc331f16709`  | 
| EFA 1\.18\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.18\.0\.tar\.gz  |  **MD5: **`fc2571a72f5d3c7b7b576ce2de38d91e` **SHA256: **`acb18a0808aedb9a5e485f1469225b9ac97f21db9af78e4cd6939700debe1cb6`  | 
| EFA 1\.17\.3 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.17\.3\.tar\.gz  |  **MD5: **`0517df4a190356ab559235147174cafd` **SHA256: **`5130998b0d2883bbae189b21ab215ecbc1b01ae0231659a9b4a17b0a33ebc6ca`  | 
| EFA 1\.17\.2 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.17\.2\.tar\.gz  |  **MD5: **`a329dedab53c4832df218a24449f4c9a` **SHA256: **`bca1fdde8b32b00346e175e597ffab32a09a08ee9ab136875fb38283cc4cd099`  | 
| EFA 1\.17\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.17\.1\.tar\.gz  |  **MD5: **`733ae2cfc9d14b52017eaf0a2ab6b0ff` **SHA256: **`f29322640a88ae9279805993cb836276ea240623820848463ca686c8ce02136f`  | 
| EFA 1\.17\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.17\.0\.tar\.gz  |  **MD5: **`d430fc841563c11c3805c5f82a4746b1` **SHA256: **`75ab0cee4fb6bd38889dce313183f5d3a83bd233e0a6ef6205d8352821ea901d`  | 
| EFA 1\.16\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.16\.0\.tar\.gz  |  **MD5: **`399548d3b0d2e812d74dd67937b696b4` **SHA256: **`cecec36495a1bc6fdc82f97761a541e4fb6c9a3cbf3cfcb145acf25ea5dbd45b`  | 
| EFA 1\.15\.2 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.15\.2\.tar\.gz  |  **MD5: **`955fea580d5170b05823d51acde7ca21` **SHA256: **`84df4fbc1b3741b6c073176287789a601a589313accc8e6653434e8d4c20bd49`  | 
| EFA 1\.15\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.15\.1\.tar\.gz  |  **MD5: **`c4610267039f72bbe4e35d7bf53519bc` **SHA256: **`be871781a1b9a15fca342a9d169219260069942a8bda7a8ad06d4baeb5e2efd7`  | 
| EFA 1\.15\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.15\.0\.tar\.gz  |  **MD5: **`9861694e1cc00d884fadac07d22898be` **SHA256: **`b329862dd5729d2d098d0507fb486bf859d7c70ce18b61c302982234a3a5c88f`  | 
| EFA 1\.14\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.14\.1\.tar\.gz  |  **MD5: **`50ba56397d359e57872fde1f74d4168a` **SHA256: **`c7b1b48e86fe4b3eaa4299d3600930919c4fe6d88cc6e2c7e4a408a3f16452c7`  | 
| EFA 1\.14\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.14\.0\.tar\.gz  |  **MD5: **`40805e7fd842c36ececb9fd7f921b1ae` **SHA256: **`662d62c12de85116df33780d40e0533ef7dad92709f4f613907475a7a1b60a97`  | 
| EFA 1\.13\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.13\.0\.tar\.gz  |  **MD5: **`c91d16556f4fd53becadbb345828221e` **SHA256: **`ad6705eb23a3fce44af3afc0f7643091595653a723ad0374084f4f2b715192e1`  | 
| EFA 1\.12\.3 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.12\.3\.tar\.gz  |  **MD5: **`818aee81f097918cfaebd724eddea678` **SHA256: **`2c225321824788b8ca3fbc118207b944cdb096b847e1e0d1d853ef2f0d727172`  | 
| EFA 1\.12\.2 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.12\.2\.tar\.gz  |  **MD5: **`956bb1fc5ae0d6f0f87d2e481d49fccf` **SHA256: **`083a868a2c212a5a4fcf3e4d732b685ce39cceb3ca7e5d50d0b74e7788d06259`  | 
| EFA 1\.12\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.12\.1\.tar\.gz  |  **MD5: **`f5bfe52779df435188b0a2874d0633ea` **SHA256: **`5665795c2b4f09d5f3f767506d4d4c429695b36d4a17e5758b27f033aee58900`  | 
| EFA 1\.12\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.12\.0\.tar\.gz  |  **MD5: **`d6c6b49fafb39b770297e1cc44fe68a6` **SHA256: **`28256c57e9ecc0b0778b41c1f777a9982b4e8eae782343dfe1246079933dca59`  | 
| EFA 1\.11\.2 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.11\.2\.tar\.gz  |  **MD5: **`2376cf18d1353a4551e35c33d269c404` **SHA256: **`a25786f98a3628f7f54f7f74ee2b39bc6734ea9374720507d37d3e8bf8ee1371`  | 
| EFA 1\.11\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.11\.1\.tar\.gz  |  **MD5: **`026b0d9a0a48780cc7406bd51997b1c0` **SHA256: **`6cb04baf5ffc58ddf319e956b5461289199c8dd805fe216f8f9ab8d102f6d02a`  | 
| EFA 1\.11\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.11\.0\.tar\.gz  |  **MD5: **`7d9058e010ad65bf2e14259214a36949` **SHA256: **`7891f6d45ae33e822189511c4ea1d14c9d54d000f6696f97be54e915ce2c9dfa`  | 
| EFA 1\.10\.1 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.10\.1\.tar\.gz  |  **MD5: **`78521d3d668be22976f46c6fecc7b730` **SHA256: **`61564582de7320b21de319f532c3a677d26cc46785378eb3b95c636506b9bcb4`  | 
| EFA 1\.10\.0 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.10\.0\.tar\.gz  |  **MD5: **`46f73f5a7afe41b4bb918c81888fefa9` **SHA256: **`136612f96f2a085a7d98296da0afb6fa807b38142e2fc0c548fa986c41186282`  | 
| EFA 1\.9\.5 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.9\.5\.tar\.gz  |  **MD5: **`95edb8a209c18ba8d250409846eb6ef4` **SHA256: **`a4343308d7ea4dc943ccc21bcebed913e8868e59bfb2ac93599c61a7c87d7d25`  | 
| EFA 1\.9\.4 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.9\.4\.tar\.gz  |  **MD5: **`f26dd5c350422c1a985e35947fa5aa28` **SHA256: **`1009b5182693490d908ef0ed2c1dd4f813cc310a5d2062ce9619c4c12b5a7f14`  | 
| EFA 1\.9\.3 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.9\.3\.tar\.gz  |  **MD5: **`95755765a097802d3e6d5018d1a5d3d6` **SHA256: **`46ce732d6f3fcc9edf6a6e9f9df0ad136054328e24675567f7029edab90c68f1`  | 
| EFA 1\.8\.4 |  https://efa\-installer\.amazonaws\.com/aws\-efa\-installer\-1\.8\.4\.tar\.gz  |  **MD5: **`85d594c41e831afc6c9305263140457e` **SHA256: **`0d974655a09b213d7859e658965e56dc4f23a0eee2dc44bb41b6d039cc5bab45`  | 