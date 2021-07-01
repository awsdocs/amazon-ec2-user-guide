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