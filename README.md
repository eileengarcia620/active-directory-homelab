# Active Directory Home Lab

A self-directed Windows Server and Active Directory home lab built to develop hands-on experience with enterprise IT administration, networking, user management, Group Policy, file sharing, and help-desk troubleshooting.

This project documents my process of building a functional Active Directory environment from the ground up in Oracle VirtualBox. The lab includes a Windows Server 2019 Domain Controller, a domain-joined Windows 10 workstation, centralized DHCP and DNS, NAT routing, Active Directory administration, Group Policy, network file sharing, and realistic user-account troubleshooting scenarios.

Screenshots throughout the repository document configuration, verification, and troubleshooting steps.

---

# Project Status

✅ **Complete**

The Active Directory environment is fully operational and has been tested from both the server and client sides.

A Windows 10 Pro workstation (`CLIENT01`) successfully receives its network configuration through DHCP, accesses the Internet through RRAS/NAT, resolves DNS through the Domain Controller, authenticates domain users, and accesses network resources using Active Directory security-group permissions.

I also used the completed environment to simulate common help-desk scenarios involving password resets, disabled accounts, account lockouts, Group Policy, and network share access.

---

# Lab Environment

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
| DHCP Scope | `172.16.0.100 - 172.16.0.200` |
| DHCP Gateway | `172.16.0.1` |
| DHCP DNS Server | `172.16.0.1` |
| Client OS | Windows 10 Pro |
| Client Hostname | `CLIENT01` |
| Client IPv4 | `172.16.0.100` (DHCP) |
| Client Gateway | `172.16.0.1` |
| Client DNS | `172.16.0.1` |
| Client Domain | `eileenlab.test` |

---

# Network Architecture

The Windows Server VM uses two virtual network adapters:

- **NAT adapter** — provides the Domain Controller with external Internet connectivity through VirtualBox.
- **Internal Network adapter (`intnet`)** — provides a private network for communication between the Domain Controller and domain workstation.

The Domain Controller uses the static internal IPv4 address:

`172.16.0.1/24`

Routing and Remote Access Service (RRAS) provides NAT routing between the private lab network and the external VirtualBox NAT interface.

The traffic path is:

`CLIENT01 → DC (172.16.0.1) → RRAS/NAT → Internet`

This allows CLIENT01 to remain on the private Active Directory network while using the Domain Controller as its gateway to external networks.

---

# Active Directory Configuration

I installed Active Directory Domain Services (AD DS) and promoted `DC` as the first Domain Controller for a new forest.

**Domain:** `eileenlab.test`

**NetBIOS domain:** `EILEENLAB`

DNS was installed as part of the Domain Controller deployment.

I verified the domain and its objects using Active Directory Users and Computers (ADUC).

## Organizational Units

Created the following OUs:

- `Employees`
- `Groups`
- `Admins`
- `Workstations`

These provide an organized structure for managing users, administrative accounts, security groups, and domain workstations.

## Domain Users

Created fictional employee accounts for administration practice:

| User | Username | Department Group |
|---|---|---|
| Sarah Johnson | `sjohnson` | HR |
| Marcus Lee | `mlee` | IT |
| Olivia Martinez | `omartinez` | Finance |

## Security Groups

Created Global Security groups:

- `HR`
- `IT`
- `Finance`

Users were assigned to their appropriate departmental groups.

These groups were later used to control access to network resources. For example, the `HR` security group was granted access to the HR departmental network share rather than assigning access directly to Sarah's individual account.

## Administrative Account

Created a dedicated administrative account:

`labadmin`

The account was added to the built-in `Domain Admins` group.

This allowed privileged administrative tasks to be performed using a separate administrator account instead of a standard domain-user account.

---

# DNS Configuration

The Domain Controller provides DNS services for the internal Active Directory network.

CLIENT01 uses:

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

Both tests were successful.

---

# DHCP Configuration

The DHCP Server role was installed and authorized in Active Directory.

A DHCP scope named **EileenLab Internal Network** was configured:

| Setting | Value |
|---|---|
| Network | `172.16.0.0/24` |
| Address Pool | `172.16.0.100 - 172.16.0.200` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `172.16.0.1` |
| DNS Server | `172.16.0.1` |
| DNS Domain | `eileenlab.test` |

The scope successfully assigned `172.16.0.100` to CLIENT01.

The lease was also verified through the DHCP management console on the Domain Controller.

---

# RRAS / NAT Routing

Routing and Remote Access Service (RRAS) was configured on `DC` to provide NAT routing for the private lab network.

CLIENT01 is connected only to the VirtualBox `intnet` network and uses:

`172.16.0.1`

as its default gateway.

Internet connectivity was verified from CLIENT01 using:

```cmd
ping 8.8.8.8
```

The test returned four successful replies with 0% packet loss.

External DNS resolution was verified using:

```cmd
nslookup google.com
```

Together, these tests confirmed that CLIENT01 could reach external networks through the Domain Controller and resolve external DNS names.

---

# CLIENT01 Deployment

A Windows 10 Pro virtual machine was deployed as the first workstation in the lab.

The VM was connected to the VirtualBox Internal Network:

`intnet`

The Windows-generated hostname was changed to:

`CLIENT01`

The hostname was verified using:

```cmd
hostname
```

DHCP automatically provided CLIENT01 with:

| Setting | Value |
|---|---|
| IPv4 Address | `172.16.0.100` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `172.16.0.1` |
| DHCP Server | `172.16.0.1` |
| DNS Server | `172.16.0.1` |
| DNS Suffix | `eileenlab.test` |

---

# Joining CLIENT01 to Active Directory

After verifying network and DNS connectivity, CLIENT01 was joined to:

`eileenlab.test`

The dedicated `labadmin` Domain Admin account was used to authorize the domain join.

Windows successfully returned:

**Welcome to the eileenlab.test domain.**

CLIENT01 was restarted to complete the domain join.

---

# Domain User Authentication

After the restart, I signed into CLIENT01 using:

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

# Active Directory Computer Management

When CLIENT01 joined the domain, Active Directory automatically created a computer object for it.

Using Active Directory Users and Computers, I moved the CLIENT01 computer object into the custom:

`Workstations`

OU.

This provides an organized location for workstation management and Group Policy targeting.

---

# Help-Desk Troubleshooting Scenarios

After completing the core environment, I used the lab to simulate common entry-level IT support scenarios.

## Password Reset and Forced Password Change

I simulated a user who forgot their domain password.

Using Active Directory Users and Computers, I:

1. Located Sarah Johnson's domain account.
2. Verified the account status.
3. Reset the user's password.
4. Enabled **User must change password at next logon**.
5. Attempted authentication from CLIENT01.
6. Verified Windows required the user to change the temporary password.
7. Successfully changed the password.
8. Verified Sarah could authenticate with the new password.

This demonstrated the difference between an administrator resetting a password and the user establishing their own password during the next authentication.

## Disabled User Account

I intentionally disabled Sarah Johnson's Active Directory account.

When attempting to authenticate from CLIENT01, Windows returned:

> Your account has been disabled. Please see your system administrator.

I returned to Active Directory Users and Computers, enabled the account, and verified that Sarah could authenticate again using the same password.

This demonstrated that authentication failures are not always password-related and that account status should be checked during troubleshooting.

## Account Lockout and Group Policy

Repeated incorrect password attempts initially did not lock the account.

I investigated the domain's Account Lockout Policy and found that the lockout threshold was not configured to trigger a lockout.

Using Group Policy Management, I configured:

| Setting | Value |
|---|---|
| Account lockout threshold | 2 invalid logon attempts |
| Account lockout duration | 10 minutes |
| Reset account lockout counter after | 10 minutes |

I then intentionally entered incorrect credentials and successfully triggered an Active Directory account lockout.

CLIENT01 returned:

> The referenced account is currently locked out and may not be logged on to.

Using Sarah Johnson's Account properties in ADUC, I confirmed that Active Directory showed the account as locked.

I unlocked the account and successfully verified authentication from CLIENT01 using the correct password.

This scenario provided hands-on experience with Group Policy configuration, account lockout behavior, Active Directory account administration, and client-side verification.

---

# Department File Sharing

I created departmental folders on the Domain Controller:

```text
C:\DepartmentShares
├── HR
├── IT
└── Finance
```

The HR folder was configured as a network share.

Network path:

```text
\\DC\hr
```

Instead of assigning access directly to an individual user, I granted the Active Directory `HR` security group **Read/Write** access.

Because Sarah Johnson is a member of the HR security group, her account received access through group membership.

## Client Access Verification

From CLIENT01 while authenticated as Sarah Johnson, I opened:

```text
\\DC\hr
```

The network share opened successfully.

To verify write access, I created:

```text
HR-Access-Test.txt
```

directly inside the network share from CLIENT01.

The successful creation of the file confirmed that the following access model was functioning:

`Domain User → Security Group → Network Share Permissions → Resource Access`

---

# Final CLIENT01 Verification

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

# Challenges & Troubleshooting Process

Building the environment involved several issues that required troubleshooting rather than simply following the planned configuration steps. I documented these because diagnosing unexpected behavior was an important part of the project.

## CLIENT01 Time Configuration

Before completing the domain join, I discovered that CLIENT01 and the Domain Controller were configured with different time zones.

I checked both systems using:

```cmd
date /t
time /t
tzutil /g
```

The Domain Controller was configured for Eastern Standard Time while CLIENT01 was configured for Pacific Standard Time.

Because Active Directory authentication relies on Kerberos and accurate system time is important for domain authentication, I corrected the CLIENT01 time-zone configuration before continuing.

After correcting the configuration, I successfully continued with the domain setup.

**Troubleshooting process:**

`Identify inconsistency → Compare client/server configuration → Correct configuration → Retry → Verify success`

## Testing Account Lockout Behavior

While testing failed login attempts, I expected Sarah's account to become locked after repeated incorrect passwords. However, the account initially remained available.

Rather than assuming the test itself was broken, I investigated the domain's Account Lockout Policy.

I reviewed:

`Default Domain Policy → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

I determined that the lockout threshold needed to be configured for the behavior I was attempting to test.

I configured:

| Setting | Value |
|---|---|
| Account lockout threshold | 2 invalid logon attempts |
| Account lockout duration | 10 minutes |
| Reset account lockout counter after | 10 minutes |

I then repeated the failed-login test.

CLIENT01 returned:

> The referenced account is currently locked out and may not be logged on to.

I verified the locked status in Active Directory Users and Computers, unlocked the account, and successfully authenticated again.

**Troubleshooting process:**

`Observe unexpected behavior → Check relevant policy → Identify configuration → Configure policy → Reproduce test → Verify expected behavior`

This reinforced the importance of checking configuration and policy rather than assuming a system is using a particular default behavior.

## Distinguishing Authentication Failures

I intentionally created multiple account problems to observe how Windows and Active Directory behaved in each situation.

I tested:

- Incorrect credentials
- Forced password change
- Disabled account
- Locked account

Although these scenarios can all prevent normal authentication, the underlying causes and resolutions are different.

For example, resetting a password would not resolve a disabled account, and entering the correct password would not allow normal authentication while an account remained locked.

I used the message presented on CLIENT01 together with the user's account status in Active Directory Users and Computers to determine the appropriate administrative action.

**Troubleshooting process:**

`Observe symptom → Check account state → Identify cause → Apply targeted fix → Test from CLIENT01 → Confirm resolution`

This gave me practice diagnosing the cause of an authentication problem before making administrative changes.

## Verifying Group-Based File Access

When configuring the HR network share, I used an Active Directory security group rather than granting access directly to Sarah Johnson.

The access path was:

`Sarah Johnson → HR Security Group → HR Network Share`

After configuring the share, I tested access from CLIENT01 while signed in as Sarah.

Opening the folder confirmed that the network resource was reachable, but I also wanted to verify that the assigned permissions allowed the expected action.

I created:

`HR-Access-Test.txt`

from CLIENT01 directly inside the shared folder.

The successful file creation provided end-to-end verification that the user could both reach the share and write to it through Active Directory group membership.

**Verification process:**

`Configure permission → Test as domain user → Perform expected action → Verify result`

---

# Lab Progress

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
| 9 | Deploy Windows 10 CLIENT01 | ✅ Complete |
| 10 | Verify DHCP, DNS, and Internet connectivity | ✅ Complete |
| 11 | Rename workstation to CLIENT01 | ✅ Complete |
| 12 | Join CLIENT01 to `eileenlab.test` | ✅ Complete |
| 13 | Authenticate with a domain user | ✅ Complete |
| 14 | Verify Domain Controller as logon server | ✅ Complete |
| 15 | Move CLIENT01 into Workstations OU | ✅ Complete |
| 16 | Verify CLIENT01 DHCP lease | ✅ Complete |
| 17 | Perform domain password reset | ✅ Complete |
| 18 | Force password change at next logon | ✅ Complete |
| 19 | Troubleshoot disabled domain account | ✅ Complete |
| 20 | Configure Account Lockout Policy | ✅ Complete |
| 21 | Trigger and troubleshoot account lockout | ✅ Complete |
| 22 | Create departmental network share | ✅ Complete |
| 23 | Configure group-based share access | ✅ Complete |
| 24 | Verify network share access from CLIENT01 | ✅ Complete |
| 25 | Verify write access to HR share | ✅ Complete |

---

# Skills Demonstrated

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
- Windows 10 client administration
- Group Policy Management
- Account lockout policies
- Password resets
- Account enable/disable administration
- Account unlock troubleshooting
- SMB/network file sharing
- Security-group-based resource access
- Network troubleshooting
- Systematic troubleshooting
- Technical documentation

---

# Key Takeaways

This project helped connect individual Windows Server concepts into a functioning domain environment.

Rather than configuring each service independently, I was able to see how Active Directory, DNS, DHCP, routing, Group Policy, user authentication, security groups, and shared resources interact.

The troubleshooting scenarios reinforced the importance of identifying the actual cause of an authentication or access problem before making changes.

For example, a user who cannot sign in may have:

- An incorrect password
- A disabled account
- A locked account
- A DNS or network connectivity problem
- A domain communication problem

The lab provided hands-on practice identifying these differences, making administrative changes, testing solutions from the client side, and documenting the results.

---

# About Me

**Eileen Garcia-Morales**

U.S. Army veteran with an M.S. in Cybersecurity (Cyber Operations), developing hands-on experience in Windows administration, Active Directory, networking, and IT troubleshooting.

Currently pursuing CompTIA Security+.
