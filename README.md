# Active Directory Home Lab

A self-directed Windows Server and Active Directory home lab built to develop hands-on experience with enterprise IT administration, networking, user management, and troubleshooting.

This project documents my process of building an Active Directory environment from the ground up in Oracle VirtualBox. I am documenting the configuration, verification, and troubleshooting process with screenshots as the lab develops.

## Project Status

🚧 **In Progress – Core Infrastructure Complete**

The core Active Directory environment is now operational. Active Directory Domain Services, DNS, DHCP, and RRAS/NAT routing have been configured and verified.

A Windows 10 Pro workstation (`CLIENT01`) has been deployed on the internal network. It successfully received its network configuration through DHCP, accessed the Internet through the Domain Controller's RRAS/NAT configuration, and joined the `eileenlab.test` domain.

Domain authentication was successfully tested by signing into `CLIENT01` with the `EILEENLAB\sjohnson` domain account. The workstation's computer object was also moved into the `Workstations` OU for future management through Group Policy.

**Current stage:** Expanding the completed core environment with realistic IT administration and help-desk troubleshooting scenarios.

---

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
| Client OS | Windows 10 Pro |
| Client Hostname | `CLIENT01` |
| Client IPv4 | `172.16.0.100` (DHCP) |
| Client Gateway | `172.16.0.1` |
| Client DNS | `172.16.0.1` |
| Client Domain | `eileenlab.test` |

---

## Network Configuration

The Windows Server VM uses two virtual network adapters:

- **NAT adapter** — provides the Domain Controller with external network/Internet connectivity through VirtualBox.
- **Internal Network adapter (`intnet`)** — provides a private network for communication between the Domain Controller and domain workstations.

The Domain Controller uses the static internal IPv4 address:

`172.16.0.1/24`

Routing and Remote Access Service (RRAS) was configured to provide NAT routing between the private lab network and the external NAT interface.

The traffic path is:

`CLIENT01 → DC (172.16.0.1) → RRAS/NAT → Internet`

This allows `CLIENT01` to remain on the private Active Directory network while using the Domain Controller as its gateway to reach external networks.

---

## Active Directory Configuration

I installed Active Directory Domain Services (AD DS) and promoted `DC` as the first Domain Controller for a new forest.

**Domain:**

`eileenlab.test`

**NetBIOS domain name:**

`EILEENLAB`

DNS was installed as part of the Domain Controller deployment.

I verified the domain and its objects using Active Directory Users and Computers (ADUC).

### Organizational Units

Created the following Organizational Units:

- `Employees`
- `Groups`
- `Admins`
- `Workstations`

These OUs provide an organized structure for managing users, administrative accounts, security groups, and domain workstations.

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

This structure will later be used to practice assigning access to resources based on group membership rather than assigning permissions directly to individual users.

### Administrative Account

Created a separate administrative account:

`labadmin`

The account was added to the built-in `Domain Admins` group.

This allows administrative tasks to be performed using a dedicated privileged account instead of a standard domain user account.

---

## DNS Configuration

The Domain Controller also provides DNS services for the internal Active Directory network.

`CLIENT01` uses:

`172.16.0.1`

as its DNS server.

Internal DNS resolution was tested using:

```cmd
nslookup eileenlab.test
```

External DNS resolution was tested using:

```cmd
nslookup google.com
```

Both tests were successful, confirming that `CLIENT01` could use the Domain Controller for DNS resolution.

---

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

The scope was activated and successfully assigned `172.16.0.100` to `CLIENT01`.

The lease was later verified through the DHCP management console on the Domain Controller.

---

## RRAS / NAT Routing

Routing and Remote Access Service (RRAS) was configured on `DC` to provide NAT routing for the internal lab network.

`CLIENT01` is connected only to the private VirtualBox `intnet` network. It uses the Domain Controller at `172.16.0.1` as its default gateway.

Internet connectivity was verified from `CLIENT01` using:

```cmd
ping 8.8.8.8
```

The test returned four successful replies with 0% packet loss.

External DNS resolution was also verified using:

```cmd
nslookup google.com
```

Together, these tests confirmed that the client could reach the Internet through the Domain Controller and resolve external DNS names.

---

## CLIENT01 Deployment

A Windows 10 Pro virtual machine was created to act as the first workstation in the lab.

The VM was connected to the VirtualBox Internal Network:

`intnet`

The workstation initially had a Windows-generated computer name and was later renamed:

`CLIENT01`

The hostname was verified using:

```cmd
hostname
```

DHCP automatically provided the workstation with:

| Setting | Value |
|---|---|
| IPv4 Address | `172.16.0.100` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `172.16.0.1` |
| DHCP Server | `172.16.0.1` |
| DNS Server | `172.16.0.1` |
| DNS Suffix | `eileenlab.test` |

---

## Joining CLIENT01 to Active Directory

After verifying network and DNS connectivity, `CLIENT01` was joined to:

`eileenlab.test`

The dedicated `labadmin` Domain Admin account was used to authorize the domain join.

Windows successfully returned:

> Welcome to the eileenlab.test domain.

The workstation was restarted to complete the domain join.

---

## Domain User Authentication

After the restart, I signed into `CLIENT01` using the Active Directory user account:

`EILEENLAB\sjohnson`

I verified the logged-in identity using:

```cmd
whoami
```

Result:

```text
eileenlab\sjohnson
```

I verified the workstation hostname using:

```cmd
hostname
```

Result:

```text
CLIENT01
```

I also verified which Domain Controller authenticated the session using:

```cmd
echo %logonserver%
```

Result:

```text
\\DC
```

This confirmed that Sarah's domain account was successfully authenticated against the Domain Controller rather than using a local Windows account.

---

## Active Directory Computer Management

When `CLIENT01` joined the domain, Active Directory automatically created a computer object for it in the default `Computers` container.

Using Active Directory Users and Computers, I moved the `CLIENT01` computer object into the custom:

`Workstations`

OU.

This will allow workstation-specific Group Policy settings to be applied later in the project.

---

## Troubleshooting Experience

During the setup, I found that `CLIENT01` and the Domain Controller had different time zone configurations.

I checked the systems using:

```cmd
date /t
time /t
tzutil /g
```

The Domain Controller was configured for Eastern Standard Time while `CLIENT01` was configured for Pacific Standard Time.

I corrected the client time zone before completing the domain join.

This was an important troubleshooting step because Active Directory authentication relies on Kerberos, which is sensitive to significant time differences between domain systems.

---

## Final CLIENT01 Verification

After the domain join, I ran:

```cmd
ipconfig /all
```

and verified:

- Hostname: `CLIENT01`
- Primary DNS suffix: `eileenlab.test`
- IPv4 address: `172.16.0.100`
- Subnet mask: `255.255.255.0`
- Default gateway: `172.16.0.1`
- DHCP server: `172.16.0.1`
- DNS server: `172.16.0.1`

This confirmed that the domain-joined workstation retained the expected network configuration.

---

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
| 9 | Deploy and configure Windows 10 `CLIENT01` | ✅ Complete |
| 10 | Verify DHCP, DNS, and Internet connectivity | ✅ Complete |
| 11 | Rename workstation to `CLIENT01` | ✅ Complete |
| 12 | Join `CLIENT01` to `eileenlab.test` | ✅ Complete |
| 13 | Authenticate to `CLIENT01` with a domain user | ✅ Complete |
| 14 | Verify Domain Controller as logon server | ✅ Complete |
| 15 | Move `CLIENT01` into the `Workstations` OU | ✅ Complete |
| 16 | Verify `CLIENT01` DHCP lease | ✅ Complete |
| 17 | Troubleshoot common Active Directory account issues | ⏳ Planned |
| 18 | Configure shared folders and permissions | ⏳ Planned |
| 19 | Configure Group Policy | ⏳ Planned |
| 20 | Configure mapped network drives | ⏳ Planned |
| 21 | Practice Active Directory administration with PowerShell | ⏳ Planned |
| 22 | Create and document troubleshooting scenarios | ⏳ Planned |

---

## Skills Being Practiced

- Windows Server 2019 administration
- Active Directory Domain Services (AD DS)
- Domain Controller deployment
- Active Directory Users and Computers (ADUC)
- Organizational Unit administration
- User and security group management
- Administrative account management
- Windows domain joining
- Domain user authentication
- DNS configuration and troubleshooting
- DHCP installation and configuration
- DHCP lease management
- IPv4 addressing and subnetting
- NAT and routing with RRAS
- Windows client administration
- Group Policy
- NTFS and share permissions
- PowerShell administration
- Network troubleshooting
- Systematic troubleshooting
- Technical documentation

---

## Next Phase: Help Desk & Administration Scenarios

With the core environment operational, the next phase of the project will focus on realistic entry-level IT and help-desk tasks.

Planned exercises include:

- Resetting Active Directory user passwords
- Unlocking locked user accounts
- Enabling and disabling accounts
- Managing security group membership
- Creating shared folders
- Configuring NTFS permissions
- Configuring share permissions
- Creating and applying Group Policy Objects
- Mapping network drives using Group Policy
- Using Event Viewer for troubleshooting
- Managing Active Directory with PowerShell
- Bulk user creation
- DNS troubleshooting
- DHCP and network troubleshooting
- Intentionally creating configuration problems and diagnosing them
- Potentially deploying an additional workstation (`CLIENT02`)

Each troubleshooting scenario will document the **problem, symptoms, diagnostic process, resolution, and verification**.

---

## About Me

**Eileen Garcia-Morales**

U.S. Army veteran with an M.S. in Cybersecurity (Cyber Operations), developing additional hands-on experience in Windows administration, Active Directory, networking, and IT troubleshooting.

Currently pursuing CompTIA Security+.
