# Active Directory Home Lab - Domain Controller & Client Deployment
<img width="720" height="340" alt="image" src="https://github.com/user-attachments/assets/c6173f42-fd80-4e87-a682-ae1abc96037e" />

## Overview

This project documents a hands-on **Active Directory (AD) home lab** built entirely in **Oracle VirtualBox** using two virtual machines:

- **DC** - a **Windows Server 2019** machine configured as a **Domain Controller**, running **AD DS, DNS, DHCP, and Routing/Remote Access (NAT)**
- **CLIENT1** - a **Windows 10** machine joined to the domain as a standard client

The goal of this lab was to simulate a small business network from the ground up: standing up a domain, configuring internal networking and IP addressing, bulk-creating user accounts with PowerShell, and joining a client workstation to the domain - all isolated on an internal virtual network.

**Domain name:** `mydomain.com`

---

## Skills Learned

- Building and configuring virtual machines in **Oracle VirtualBox** (VM settings, memory, storage, internal networking)
- Installing and configuring **Windows Server 2019**
- Installing **VirtualBox Guest Additions** for improved VM performance/integration
- Configuring an **internal network** and assigning **static IP addressing** to a Domain Controller
- Installing and configuring core Windows Server roles:
  - **Active Directory Domain Services (AD DS)**
  - **DNS**
  - **DHCP** (scope creation and address pool management)
  - **Routing and Remote Access (RRAS)** for NAT, allowing the internal network to reach the internet
- Creating and managing **Organizational Units (OUs)** in **Active Directory Users and Computers (ADUC)**
- Writing and running a **PowerShell script** to bulk-create Active Directory user accounts from a text file (`names.txt`), including setting passwords, OU placement, and account properties
- Verifying **DHCP client leasing** using `ipconfig` / `ipconfig /renew`
- Joining a **Windows 10 client** to an Active Directory domain
- Confirming domain join success by locating the new computer object inside **ADUC**

---

## Step-by-Step Walkthrough

### 1. Create the Domain Controller VM
Set up a new VirtualBox VM named **DC** running Windows 11(64-bit)/Server 2019, with 2048 MB RAM, 2 processors, TPM/Secure Boot enabled, and an 80 GB virtual disk.

<img width="953" height="746" alt="01-dc-vm-settings-overview" src="https://github.com/user-attachments/assets/7db27ce0-4891-4ecd-8924-d2ce14dc9e29" />

### 2. Review General VM Settings
Confirmed base memory allocation and shared clipboard/drag-and-drop settings for the DC VM.

<img width="952" height="739" alt="02-dc-vm-general-settings" src="https://github.com/user-attachments/assets/53f74c23-f5aa-449a-b19f-250ef2a5bd3f" />

### 3. Configure Internal Networking
Set the DC's network adapter to **Internal Network** (`intnet`) so the DC and future client machines can communicate privately, isolated from the host network.

<img width="954" height="739" alt="03-dc-vm-network-internal" src="https://github.com/user-attachments/assets/b4e03f39-02b8-4e02-9aae-533989fb67e8" />

### 4. Begin Windows Server 2019 Installation
Booted the DC VM and started the Windows Server 2019 setup wizard, selecting language, time/currency format, and keyboard layout.

<img width="711" height="563" alt="04-windows-server-2019-setup-language" src="https://github.com/user-attachments/assets/6f0b9d89-7e05-4fcb-93d5-1bf651e23c3a" />

### 5. Select the Windows Server Edition
Chose **Windows Server 2019 Standard Evaluation (Desktop Experience)** to get the full GUI environment.

<img width="706" height="562" alt="05-windows-server-2019-select-edition" src="https://github.com/user-attachments/assets/72f63509-4cd2-4af9-becc-06d717712dfa" />

### 6. Install VirtualBox Guest Additions
Installed Guest Additions on the DC VM to enable better display resolution, shared folders, and overall host/guest integration.

<img width="861" height="600" alt="06-guest-additions-install" src="https://github.com/user-attachments/assets/d691ed5e-d01d-403c-a6e4-f7f52939441d" />

### 7. Locate Network Adapters
Opened **Network Connections** to identify the Ethernet adapters available on the server (Internet-facing NAT adapter and Internal adapter).

<img width="1241" height="648" alt="07-network-connections-ethernet" src="https://github.com/user-attachments/assets/4e282ec5-9b25-4acc-aaf3-d9d06d509e40" />

### 8. Assign a Static IP to the Internal Adapter
Configured the DC's internal-facing NIC with a static IP (`172.16.0.1`, subnet `255.255.255.0`) and pointed DNS to itself (`127.0.0.1`), per the planned network diagram (DC handles AD DS/DNS, DHCP scope `172.16.0.100-200`, and NAT out to the internet).

<img width="1217" height="621" alt="08-static-ip-configuration" src="https://github.com/user-attachments/assets/2d2cca56-4a47-4095-a7d1-4a059d8a455b" />
### 9. Promote to Domain Controller
After promoting the server and installing AD DS, opened **Active Directory Users and Computers** and confirmed the new domain `mydomain.com` with its default containers (Builtin, Computers, Domain Controllers, Users, etc.).

<img width="1239" height="648" alt="09-active-directory-users-computers" src="https://github.com/user-attachments/assets/ee9c7402-6c1d-483c-aa3c-5fbfc3f4c292" />

### 10. Configure Routing and Remote Access (NAT)
Ran the **Routing and Remote Access Server Setup Wizard** to configure NAT, allowing internal network clients to reach the internet through the DC.

<img width="1240" height="644" alt="10-routing-remote-access-wizard" src="https://github.com/user-attachments/assets/bc4db146-9fcf-4260-b5f3-55643cd1fdf7" />

### 11. Configure DHCP Scope
Set up a DHCP scope (`172.16.0.0` range `172.16.0.100-200`) so future client machines automatically receive an IP address, gateway, and DNS server from the DC.

<img width="1241" height="647" alt="11-dhcp-scope-configuration" src="https://github.com/user-attachments/assets/c81f329e-53a7-415f-9bff-4c27b3617cc7" />

### 12. Write a PowerShell Script to Bulk-Create Users
Authored a PowerShell script (`1_CREATE_USERS.ps1`) that reads a list of names from `names.txt`, generates usernames, creates a dedicated `_USERS` Organizational Unit, and provisions each account with a preset password, display name, and Employee ID.

<img width="1248" height="650" alt="12-powershell-bulk-user-script" src="https://github.com/user-attachments/assets/06570a0c-0582-47a3-8bbd-22c9ef2acdd0" />

### 13. Prepare the Name List
Populated `names.txt` with a list of full names to be converted into AD user accounts by the script.

<img width="1250" height="650" alt="13-names-txt-user-list" src="https://github.com/user-attachments/assets/9ae44c3c-a6eb-4466-bf40-e9a2f7e4632d" />

### 14. Run the Script and Verify Users
Executed the script, which looped through every name, generated a username, and created the account in Active Directory - confirmed by cross-checking the new user objects inside ADUC.

<img width="1240" height="650" alt="14-bulk-users-created-adusc" src="https://github.com/user-attachments/assets/9d212b5b-aa60-482f-a1e1-53dcb1c7701c" />

### 15. Create the Client VM
Built a second VirtualBox VM named **CLIENT1** (Windows 10, 64-bit) to act as a domain-joined workstation, attached to both NAT and the internal network (`intnet`).

<img width="1289" height="676" alt="15-client-vm-creation" src="https://github.com/user-attachments/assets/8a27a575-a20e-40ca-8b62-4b3c42242441" />

### 16. Verify DHCP Addressing on the Client
Booted CLIENT1 and used `ipconfig` / `ipconfig /renew` to confirm it received a DHCP-assigned IP (`172.16.0.100`), subnet mask, and default gateway from the DC.

<img width="1293" height="573" alt="16-client-ipconfig-dhcp-lease" src="https://github.com/user-attachments/assets/00b12594-77e5-4f7f-bda7-d4d3f4e5d1b0" />

### 17. Join the Client to the Domain
From System Properties, changed CLIENT1's domain membership to `mydomain.com`, joining it to the Active Directory domain hosted by the DC.

<img width="1289" height="565" alt="17-client-domain-join" src="https://github.com/user-attachments/assets/ca2b1b90-09d9-40e6-b910-18952c1ca287" />

### 18. Confirm the Client Appears in Active Directory
After a restart, verified that **CLIENT1** now appears as a computer object inside the `Computers` container in Active Directory Users and Computers - confirming a successful domain join.

<img width="1218" height="575" alt="18-client-joined-in-adusc" src="https://github.com/user-attachments/assets/49f9b0d4-1320-4d23-a1c9-018905583970" />

---

## Network Summary

| Component        | Detail                                  |
|-------------------|------------------------------------------|
| Domain / FQDN     | `mydomain.com`                          |
| DC Internal IP    | `172.16.0.1` / `255.255.255.0`          |
| DHCP Scope        | `172.16.0.100 - 172.16.0.200`            |
| Roles on DC       | AD DS, DNS, DHCP, RRAS (NAT)             |
| Client            | CLIENT1 (Windows 10), domain-joined      |

Mission Complete! 
