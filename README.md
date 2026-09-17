# Active Directory Home Lab

A self-directed Windows Server and Active Directory home lab built to develop hands-on experience with enterprise IT administration, networking, user management, and troubleshooting.

This project documents the process of building an Active Directory environment from the ground up in Oracle VirtualBox. As the lab develops, I am documenting the configuration process, verification steps, and troubleshooting scenarios with screenshots.

## Project Status

🚧 **In Progress**

Current stage: The core Windows Server infrastructure is operational. Active Directory Domain Services, DNS, RRAS/NAT routing, and DHCP have been configured. Organizational Units, domain users, security groups, and a separate administrative account have also been created.

**Next step:** Deploy and configure the `CLIENT01` Windows workstation, verify DHCP/DNS connectivity, and join the workstation to the `eileenlab.test` domain.

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
| Internet Connectivity | VirtualBox NAT with RRAS/NAT routing |
| DHCP Scope | `172.16.0.100` - `172.16.0.200` |
| DHCP Gateway | `172.16.0.1` |
| DHCP DNS Server | `172.16.0.1` |
| Client | `CLIENT01` (next stage) |

## Network Configuration

The Windows Server VM uses two virtual network adapters:

- **NAT adapter** — provides the Domain Controller with external network/Internet connectivity through VirtualBox.
- **Internal Network adapter (`intnet`)** — provides a private network for communication between the Domain Controller and future domain workstations.

The Domain Controller uses the static internal IPv4 address:

`172.16.0.1/24`

Routing and Remote Access Service (RRAS) was configured to provide NAT routing between the private lab network and the external NAT interface.

The intended traffic path is:

`CLIENT01 → DC (172.16.0.1) → RRAS/NAT → Internet`

## Active Directory Configuration

The following Active Directory infrastructure has been configured:

- Installed Active Directory Domain Services (AD DS)
- Created a new Active Directory forest
- Created the root domain `eileenlab.test`
- Configured `EILEENLAB` as the NetBIOS domain name
- Installed DNS as part of the Domain Controller deployment
- Successfully promoted `DC` to a Domain Controller
- Verified the domain using Active Directory Users and Computers (ADUC)

### Organizational Units

Created the following Organizational Units:

- `Employees`
- `Groups`
- `Admins`
- `Workstations`

### Domain Users

Created fictional employee accounts for administration practice:

| User | Username | Department Group |
|---|---|---|
| Sarah Johnson | `sjohnson` | HR |
| Marcus Lee | `mlee` | IT |
| Olivia Martinez | `omartinez` | Finance |

### Security Groups

Created Global Security groups:

- `HR`
- `IT`
- `Finance`

Users were assigned to their appropriate departmental security groups.

### Administrative Account

Created a separate administrative account:

`labadmin`

The account was added to the built-in `Domain Admins` group to practice separating privileged administrative access from standard user accounts.

## DHCP Configuration

The DHCP Server role was installed and authorized in Active Directory.

A DHCP scope named **EileenLab Internal Network** was created with the following configuration:

| Setting | Value |
|---|---|
| Network | `172.16.0.0/24` |
| Address Pool | `172.16.0.100` - `172.16.0.200` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `172.16.0.1` |
| DNS Server | `172.16.0.1` |
| DNS Domain | `eileenlab.test` |

The scope is activated and ready to provide network configuration to domain workstations.

## Lab Progress

| # | Task | Status |
|---|---|---|
| 1 | Create Windows Server 2019 VM | ✅ Complete |
| 2 | Configure server networking | ✅ Complete |
| 3 | Install Active Directory Domain Services | ✅ Complete |
| 4 | Create `eileenlab.test` domain and promote Domain Controller | ✅ Complete |
| 5 | Create Organizational Units, users, and groups | ✅ Complete |
| 6 | Create separate Domain Admin account | ✅ Complete |
| 7 | Configure RRAS/NAT routing | ✅ Complete |
| 8 | Install and configure DHCP | ✅ Complete |
| 9 | Create and configure `CLIENT01` | ⏳ Next |
| 10 | Verify client DHCP, DNS, and network connectivity | ⏳ Planned |
| 11 | Join `CLIENT01` to Active Directory domain | ⏳ Planned |
| 12 | Verify domain user authentication | ⏳ Planned |
| 13 | Troubleshoot incorrect DNS configuration | ⏳ Planned |
| 14 | Troubleshoot disabled/locked user accounts | ⏳ Planned |
| 15 | Configure and troubleshoot file/share permissions | ⏳ Planned |
| 16 | Configure Group Policy | ⏳ Planned |
| 17 | Practice Active Directory administration with PowerShell | ⏳ Planned |

## Skills Being Practiced

- Windows Server 2019 administration
- Active Directory Domain Services (AD DS)
- Domain Controller deployment
- Active Directory Users and Computers (ADUC)
- Organizational Unit administration
- User and security group management
- Administrative account management
- DNS fundamentals
- DHCP installation and configuration
- IPv4 addressing and subnetting
- NAT and routing with RRAS
- Windows domain authentication
- Group Policy
- NTFS and share permissions
- PowerShell administration
- Network troubleshooting
- Systematic troubleshooting and technical documentation

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