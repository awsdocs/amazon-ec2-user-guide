# Attestation with AMD SEV\-SNP<a name="snp-attestation"></a>

Attestation is a process that allows your instance to prove its state and identity\. When you turn on AMD SEV\-SNP for your instance, you can request an AMD SEV\-SNP attestation report from the underlying processor\. The AMD SEV\-SNP attestation report contains a cryptographic hash, called the launch measurement, of the initial guest memory contents and initial vCPU state\. The attestation report is signed with a VLEK signature that chains back to an AMD root of trust\. You can use the launch measurement included in the attestation report to validate that the instance is running in a genuine AMD environment and to validate the initial boot code that was used to launch the instance\.

To perform attestation with AMD SEV\-SNP, complete the following steps\.

## Step 1: Get the attestation report<a name="snp-att-get-report"></a>

In this step, you install the required tools, and then request the AMD SEV\-SNP attestation report from the processor and the VLEK signing key for the processor\.

1. You must use the `sev-guest` utility to request the attestation report from the CPU\. To install the `sev-guest` utility from the [https://github.com/AMDESE/sev-guest](https://github.com/AMDESE/sev-guest), run the following commands\.

   ```
   $ git clone https://github.com/AMDESE/sev-guest.git
   $ cd sev-guest
   $ make sev-guest-get-report
   $ make sev-guest-parse-report
   ```

1. Use the `sev-guest` utility to request the attestation report and the VLEK certificate that was used to sign the attestation report\.

   ```
   $ sudo ./sev-guest-get-report guest_report.bin -x
   ```

   The command creates two files:
   + `guest_report.bin` – The signed attestation report\.
   + `a8074bc2-a25a-483e-aae6-39c045a0b8a1` – The VLEK certificate, in Distinguished Encoding Rules \(DER\) format, that was used to sign the attestation report\.

## Step 2: Validate the attestation report's signature<a name="snp-att-validate-signature"></a>

The attestation report is signed with a certificate, called the Versioned Loaded Endorsement Key \(VLEK\), which is issued by AMD for AWS\. In this step, you will validate that the VLEK certificate is issued by AMD, and that the attestation report is signed by that VLEK certificate\.

1. You must use the `sev-tool` utility to validate that the attestation report is signed by the VLEK certificate\. The utility requires that the attestation report and the VLEK certificate be in a folder named `/certs`\. Run the following command to create the `/certs` directory\.

   ```
   $ sudo mkdir certs
   ```

1. Convert the DER\-encoded VLEK certificate \(`a8074bc2-a25a-483e-aae6-39c045a0b8a1`\) into the PEM format, which is required by the `sev-guest` utility\.

   ```
   $ sudo openssl x509 -inform der -in a8074bc2-a25a-483e-aae6-39c045a0b8a1 -out certs/vcek.pem
   ```

   The VLEK certificate is written to a new file, named `vcek.pem`, in the `/certs` directory\.

1. Download the VLEK root of trust certificates from the official AMD website to the `/certs` directory\.

   ```
   $ sudo curl --proto '=https' --tlsv1.2 -sSf https://kdsintf.amd.com/vlek/v1/Milan/cert_chain -o certs/cert_chain.pem
   ```

1. Use `openssl` to validate that the VLEK certificate is signed by the AMD root of trust certificates\.

   ```
   $ sudo openssl verify --CAfile certs/cert_chain.pem certs/vcek.pem
   ```

   Expected output\.

   ```
   certs/vcek.pem: OK
   ```

1. Copy the attestation report to the `/certs` directory\.

   ```
   $ sudo cp guest_report.bin certs/
   ```

1. You must use the `sev-tool` utility to validate the attestation report signature\. Run the following commands to install the `sev-tool` utility\.

   ```
   $ cd ..
   $ git clone https://github.com/AMDESE/sev-tool.git
   $ cd sev-tool
   $ autoreconf -vif && ./configure && make
   ```

1. Use the `sev-tool` utility to validate that the attestation report is signed by the VLEK certificate\.

   ```
   $ sudo ./src/sevtool --ofolder ../sev-guest/certs --validate_guest_report
   ```

   Expected output\.

   ```
   Guest report validated successfully!
   ```