# Active Directory Home Lab

A self-directed Windows Server and Active Directory home lab built to develop hands-on experience with enterprise IT administration, networking, user management, and troubleshooting.

This project documents the process of building an Active Directory environment from the ground up in Oracle VirtualBox. As the lab develops, I am documenting the configuration process, verification steps, and troubleshooting scenarios with screenshots.

## Project Status

🚧 **In Progress**

Current stage: The Windows Server 2019 system has been successfully promoted as the first Domain Controller for the `eileenlab.test` domain. Next, I will configure Organizational Units, users, and security groups.

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | Oracle VirtualBox |
| Server OS | Windows Server 2019 Evaluation (Desktop Experience) |
| Server Hostname | `DC` |
| Active Directory | Active Directory Domain Services (AD DS) |
| Domain | `eileenlab.test` |
| NetBIOS Domain Name | `EILEENLAB` |
| Internal Network | VirtualBox Internal Network (`intnet`) |
| DC Internal IPv4 | `172.16.0.1/24` |
| Internet Connectivity | VirtualBox NAT |
| Client | Windows client VM (planned) |

## Current Network Configuration

The Windows Server VM uses two virtual network adapters:

- **NAT adapter** — provides the Windows Server VM with external network/Internet connectivity through VirtualBox.
- **Internal Network adapter (`intnet`)** — creates the private lab network that will connect the Domain Controller and Windows client.

The internal adapter on the server uses the static IPv4 address `172.16.0.1/24`.

This design will allow the Windows client to communicate with the Domain Controller over the private lab network while the server provides domain and network services.

## Active Directory Configuration

Completed so far:

- Created the Windows Server 2019 virtual machine
- Configured NAT and internal network adapters
- Assigned the server a static internal IPv4 address of `172.16.0.1/24`
- Installed the Active Directory Domain Services (AD DS) server role
- Created a new Active Directory forest
- Created the root domain `eileenlab.test`
- Configured `EILEENLAB` as the NetBIOS domain name
- Installed DNS as part of the Domain Controller deployment
- Successfully promoted `DC` to a Domain Controller
- Verified the `eileenlab.test` domain in Active Directory Users and Computers
  
## Lab Progress

| # | Task | Status |
|---|---|---|
| 1 | Create Windows Server 2019 VM | ✅ Complete |
| 2 | Configure server networking | ✅ Complete |
| 3 | Install Active Directory Domain Services | ✅ Complete |
| 4 | Create `eileenlab.test` domain and promote Domain Controller | ✅ Complete |
| 5 | Create Organizational Units, users, and groups | ⏳ Planned |
| 6 | Configure DHCP | ⏳ Planned |
| 7 | Create and configure Windows client | ⏳ Planned |
| 8 | Join client to Active Directory domain | ⏳ Planned |
| 9 | Verify domain authentication and DNS | ⏳ Planned |
| 10 | Troubleshoot incorrect DNS configuration | ⏳ Planned |
| 11 | Troubleshoot disabled/locked user accounts | ⏳ Planned |
| 12 | Configure and troubleshoot file/share permissions | ⏳ Planned |
| 13 | Configure Group Policy | ⏳ Planned |
| 14 | Practice Active Directory administration with PowerShell | ⏳ Planned |

## Skills Being Practiced

- Windows Server 2019 administration
- Active Directory Domain Services (AD DS)
- Domain Controller deployment
- DNS fundamentals
- IPv4 addressing and network configuration
- Active Directory users, groups, and Organizational Units
- Windows domain authentication
- DHCP configuration
- Group Policy
- NTFS and share permissions
- PowerShell administration
- Systematic troubleshooting and documentation

## Planned Troubleshooting Scenarios

After the core environment is operational, I will intentionally introduce common problems and document how I identify and resolve them.

Planned scenarios include:

- Incorrect DNS configuration preventing domain communication
- Disabled Active Directory user account
- Locked Active Directory user account
- Password reset and authentication issues
- Incorrect NTFS/share permissions
- Network connectivity problems

Each troubleshooting scenario will document the problem, symptoms, diagnostic process, resolution, and verification.

## About Me

**Eileen Garcia-Morales**

U.S. Army veteran with an M.S. in Cybersecurity (Cyber Operations), developing additional hands-on experience in Windows administration, Active Directory, networking, and IT troubleshooting.

Currently pursuing CompTIA Security+.
