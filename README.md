NEH Hospital — Identity & Access Management Lab
Enterprise IAM, Hybrid Identity, Zero Trust & Healthcare Security
A hands-on Identity and Access Management (IAM) lab designed for a simulated 500-bed healthcare organization. The environment demonstrates enterprise identity management, role-based access control, privileged access management, hybrid identity, conditional access, security policy enforcement, and identity lifecycle automation.

Platform: Windows Server 2016 + Microsoft Entra ID P2
Virtualization: VirtualBox
Workstation: Windows 11
Domain: NEH.local
Compliance Alignment: HIPAA Security Rule | NIST SP 800-63 | Zero Trust

Project Overview
This project simulates the design and implementation of an enterprise IAM environment for NEH Hospital.

The lab covers the identity lifecycle from account creation and provisioning through access control, authentication, privileged access, periodic access reviews, and account termination.

Core Technologies
Technology	Implementation
Active Directory Domain Services	OU structure, users, groups, delegation
Windows Server 2016	Domain Controller, DNS, file services
Microsoft Entra ID P2	Cloud identity and access management
Microsoft Entra Connect	Hybrid identity synchronization
Conditional Access	MFA, risk-based controls, network restrictions
Privileged Identity Management	Just-in-time privileged access
Identity Protection	Risk-based identity security
Access Reviews	Periodic access certification
Group Policy	Security configuration and endpoint restrictions
PowerShell	Identity lifecycle automation
VirtualBox	Lab virtualization

IAM Architecture
                         ┌─────────────────────────┐
                         │   Microsoft Entra ID     │
                         │          P2             │
                         │                         │
                         │ Conditional Access      │
                         │ PIM                     │
                         │ Identity Protection     │
                         │ Access Reviews          │
                         └────────────┬────────────┘
                                      │
                              Entra Connect
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │    Windows Server 2016   │
                         │         NEH01           │
                         │                         │
                         │ Active Directory        │
                         │ DNS                     │
                         │ File Shares             │
                         │ Group Policy             │
                         └────────────┬────────────┘
                                      │
                              `NEH.local`
                                      │
                 ┌────────────────────┼────────────────────┐
                 │                    │                    │
                 ▼                    ▼                    ▼
          Clinical Users       Administrative        IT Users
                                  Users
                 │                    │                    │
                 └────────────────────┼────────────────────┘
                                      │
                                      ▼
                              Windows 11
                              Workstation

Active Directory Design
Organizational Units
The Active Directory environment uses a structured OU hierarchy to separate users by department and apply targeted Group Policy.

NEH.local
│
├── NEH-Users
│   ├── NEH-IT
│   │   ├── NEH-IT-Support
│   │   ├── NEH-IT-Admins
│   │   ├── NEH-IT-Server-Admins
│   │   └── NEH-IT-Network
│   │
│   ├── Clinical
│   │   ├── Physicians
│   │   ├── Nursing
│   │   ├── Pharmacy
│   │   └── Radiology
│   │
│   ├── Administrative
│   │   ├── HR
│   │   └── Billing
│   │
│   ├── Contractors
│   └── Vendors
│
├── NEH-Workstation
├── Disabled
└── ServiceAccounts

Design Principles
OUs determine where accounts are stored and where GPOs apply.
Security groups determine resource access.
A dedicated Disabled OU provides a controlled location for terminated accounts.
Workstations are separated from user accounts for computer-based policy targeting.
Human accounts are organized under NEH-Users.
Role-Based Access Control
Security groups were designed around job responsibilities rather than individual users.

IT
Role	Security Group
IT Administrators	GRP-IT-Admins
Helpdesk	GRP-IT-Support
Server Administrators	GRP-IT-Server-Admins
Network Engineers	GRP-IT-Network

Clinical
Role	Security Group
Physicians	GRP-Physicians
Registered Nurses	GRP-Nursing-RN
Charge Nurses	GRP-Nursing-Charge
Pharmacists	GRP-Pharmacy-PharmD
Pharmacy Technicians	GRP-Pharmacy-Tech
Radiologists	GRP-Radiology-MD
Radiology Technicians	GRP-Radiology-Tech

Administrative
Role	Security Group
HR Management	GRP-HR-Manager
HR Staff	GRP-HR-Staff
Billing Management	GRP-Billing-Manager
Billing Staff	GRP-Billing-Staff

External Users
Contractors and vendors were separated into dedicated security groups with restricted access and account expiration dates.

Group Policy
Department-specific GPOs were implemented to enforce security controls and least privilege.

Password Policy
The domain password policy includes:

12-character minimum password length
Password complexity enabled
90-day maximum password age
10-password history
5-attempt account lockout threshold
30-minute lockout duration
Minimum password age of 1 day
Endpoint Security
Additional GPO controls include:

Audit account management
Audit logon events
Audit directory service access
HIPAA security warning banners
Automatic screen locking
USB storage restrictions
Registry Editor restrictions
Control Panel restrictions
PowerShell/CMD restrictions for appropriate users
Task Manager restrictions for vendor accounts
Hybrid Identity
Microsoft Entra Connect was used to synchronize on-premises Active Directory identities with Microsoft Entra ID.

Synchronization
Active Directory
      │
      │ Users
      │ Groups
      │ Attributes
      │ Password Hashes
      ▼
Microsoft Entra Connect
      │
      ▼
Microsoft Entra ID

Password Hash Synchronization was used as the hybrid authentication method.

The lab also includes a Windows 11 workstation configured for hybrid identity.

Conditional Access
Conditional Access policies were designed around Zero Trust principles.

Implemented Policies
Policy	Purpose
NEH-MFA-All-Users	MFA for hospital users
NEH-Block-Legacy-Auth	Prevent legacy authentication
NEH-MFA-IT-Admins	Stronger controls for IT administrators
NEH-Block-Outside-Network	Restrict external network access
NEH-External-Restrictions	Additional controls for contractors/vendors
NEH-User-Risk-Policy	Respond to high-risk users
NEH-Sign-In-Risk-Policy	Respond to risky authentication attempts

These policies demonstrate the Zero Trust concept of verify explicitly and continuously evaluate access conditions.

Privileged Identity Management
Microsoft Entra Privileged Identity Management (PIM) was configured to eliminate unnecessary standing administrative privileges.

Administrative roles were configured as eligible rather than permanently assigned.

Activation Process
Administrator
     │
     ▼
Open My Roles
     │
     ▼
Activate Role
     │
     ▼
Provide Business Justification
     │
     ▼
Temporary Privileged Access
     │
     ▼
Automatic Expiration

This approach reduces the attack surface associated with permanently assigned privileged roles.

Access Reviews
Periodic access reviews were configured for sensitive security groups.

Reviews include:

Physicians
Nursing
IT Administrators
HR Managers
Billing Managers
Clinical Contractors
EHR Vendors
Review Configuration
Frequency: Quarterly
Review duration: 14 days
Reviewer: User's manager
Automatic application of results: Enabled
Non-response: Remove access
Review lifecycle: Ongoing
This provides a repeatable process for validating that users continue to require their assigned access.

Identity Lifecycle Automation
PowerShell automation was used to support identity lifecycle processes.

Joiner
New employee provisioning includes:

Account creation
OU placement
Security group membership
Department attributes
Initial account configuration
Mover
Department changes can trigger:

OU changes
Group membership updates
Removal of previous access
Assignment of new role-based access
Leaver
Offboarding includes:

Account disabling
Access removal
Group membership cleanup
Account expiration
Moving the account to the Disabled OU
Shared Resources
Department-specific network shares were configured with group-based permissions.

Examples include:

\\NEH01\NEH-IT-Support
\\NEH01\NEH-IT-Admins
\\NEH01\NEH-IT-Server-Admins
\\NEH01\NEH-IT-Network
\\NEH01\NEH-HR
\\NEH01\NEH-Billing

Access is assigned through security groups rather than individual user permissions.

Security Testing & Findings
Application Control Bypass
During testing, a limitation was identified in the Windows application restriction configuration.

Finding
Blocked executables could potentially be launched indirectly through a batch file.

Severity
Medium

Affected Policies
Helpdesk
Clinical
Contractor
Vendor
Recommended Remediation
Implement AppLocker script rules to restrict:

*.bat
*.cmd
*.vbs
*.ps1

Security filtering can be used to exempt authorized IT administrators where scripting is required for legitimate administrative functions.

Security Lesson
The finding demonstrates the importance of validating security controls through hands-on testing. A policy that appears effective during normal use may still have alternate execution paths.

Compliance Alignment
HIPAA
The lab demonstrates controls supporting:

Unique user identification
Least privilege
Minimum necessary access
Audit logging
Automatic workstation locking
Account termination procedures
Periodic access certification
NIST SP 800-63
The environment incorporates:

Strong authentication
MFA
Password controls
Identity lifecycle management
Zero Trust
The architecture incorporates:

Explicit verification
MFA
Risk-based authentication
Least privilege
Just-in-time privileged access
Continuous access evaluation concepts
Portfolio Highlights
This project demonstrates hands-on experience with:

Active Directory administration
Windows Server administration
Microsoft Entra ID
Hybrid identity
Conditional Access
Privileged Identity Management
Identity Protection
Access Reviews
Group Policy
RBAC
PowerShell automation
Identity lifecycle management
Security testing
Healthcare security concepts
Zero Trust architecture
Repository Structure
NEH-Hospital-IAM-Lab/
│
├── README.md
├── docs/
├── architecture/
├── active-directory/
├── group-policy/
├── entra-id/
├── powershell/
├── screenshots/
└── security-findings/

Disclaimer
This project is a controlled educational laboratory environment.

All organizations, identities, credentials, network addresses, and other infrastructure details are simulated and are not intended to represent a real healthcare organization's production environment.
