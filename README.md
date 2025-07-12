# 🔐 Azure Point-to-Site (P2S) VPN Setup

This guide explains how to set up a secure Point-to-Site (P2S) VPN connection in Microsoft Azure using certificate authentication.

Point-to-Site Virtual Private Network (VPN) connections are helpful when you want to connect to your VNet from a remote location. This helps us securely connect individual clients running Windows, Linux, or macOS to an Azure VNet. This blog will outline steps to create and test a Point to Site VPN while using an Azure Certificate Authentication method.
---

## 📘 Table of Contents

1. [What is Point-to-Site VPN?](#1-what-is-point-to-site-vpn)
2. [Use Case](#2-use-case)
3. [Prerequisites](#3-prerequisites)
4. [Step-by-Step Setup](#4-step-by-step-setup)
   - 4.1 Create a Virtual Network (VNet)
   - 4.2 Create a Gateway Subnet
   - 4.3 Create Virtual Network Gateway
   - 4.4 Generate and Upload Root Certificate
   - 4.5 Configure Point-to-Site Settings
   - 4.6 Download & Connect VPN Client
5. [Test VPN Connection](#5-test-vpn-connection)
6. [Screenshot Checklist](#6-screenshot-checklist)

---

## 1. What is Point-to-Site VPN?

A Point-to-Site VPN allows you to securely connect a single client device (like a laptop) to your Azure virtual network using encrypted communication over the internet.

---

## 2. Use Case

- Remote developers needing access to Azure VMs
- Admins managing Azure infrastructure
- Secure testing environment

---

## 3. Prerequisites

- Azure Subscription
- A Virtual Network (e.g., `10.1.0.0/16`)
- A client PC (Windows/macOS/Linux)
- PowerShell (for certificate generation)

---

## 4. Step-by-Step Setup

### 4.1 Create a Virtual Network (VNet)
- Name: `MyVNet`
- Address space: `10.1.0.0/16`
- Subnet: `10.1.0.0/24`
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20135859.png)
---

### 4.2 Create Gateway Subnet

- Go to your VNet → Subnets → `+ Gateway subnet`
- Address Range: `10.1.1.0/27`
- Name auto-fills as `GatewaySubnet`


---

### 4.3 Create Virtual Network Gateway

| Field               | Value             |
|---------------------|-------------------|
| Name                | MyVNetGateway     |
| Gateway type        | VPN               |
| VPN type            | Route-based       |
| SKU                 | VpnGw2            |
| Generation          | Generation2       |
| Virtual Network     | Select your VNet  |
| Public IP           | Create new        |



- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20135917.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20135925.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20135935.png)
---

### 4.4 Generate and Upload Root Certificate

#### Generate Certificate (Windows PowerShell)

```powershell
# Root Certificate
$rootCert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=AzureP2SRootCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign `
-KeyUsage CertSign

# Export to file
Export-Certificate -Cert $rootCert -FilePath "C:\AzureP2SRootCert.cer"


- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140047.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140059.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140120.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140127.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140138.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140147.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140154.png)



```

---
## Upload to Azure
- Go to VPN Gateway → Point-to-site configuration → Configure now

- Add Root Certificate:

- Name: MyRootCert

- Public Certificate Data: Paste .cer contents
---

---
## 4.5 Configure Point-to-Site Settings
- Setting	Value
- Address pool	172.16.201.0/24
- Tunnel type	IKEv2 and OpenVPN
- Authentication type	Azure certificate

- Click Save.

- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140314.png)


---

---
## 4.6 Download & Connect VPN Client
- Go to VPN Gateway → Point-to-site config

- Click Download VPN client

- Run installer (VpnClientSetupAmd64.exe)

- Go to Windows VPN settings → Click Connect

- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140324.png)
---
---
## 5. Test VPN Connection
- bash
- Copy
- Edit
- ipconfig
- You should get an IP from 172.16.201.0/24


- ping 10.1.0.4
- You should be able to reach Azure VMs if NSGs allow.


- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140332.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140338.png)
- ![Homepage Screenshot](assets/Screenshot%202025-07-12%20140345.png)

