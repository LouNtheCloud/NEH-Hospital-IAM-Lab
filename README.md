NEH Hospital
Identity and Access Management Lab
Full Technical Documentation

Platform: Windows Server 2016 + Microsoft Entra ID P2
Domain: NEH.local
Compliance: HIPAA | NIST SP 800-63 | Zero Trust
Environment: VirtualBox | Windows Server 2016 | Windows 11
 
Executive Summary
This document describes the design, implementation, and configuration of a complete enterprise Identity and Access Management (IAM) environment built for NEH Hospital. The lab simulates a real-world healthcare IAM deployment aligned to HIPAA Security Rule requirements, NIST SP 800-63 identity standards, and Zero Trust architecture principles.

The environment covers the full IAM lifecycle including identity provisioning, role-based access control, group policy enforcement, privileged access management, conditional access, and access certification reviews.

Component	Technology Used
On-Premises Directory	Windows Server 2016 Active Directory
Cloud Identity	Microsoft Entra ID (Azure AD) P2
Hybrid Sync	Microsoft Entra Connect
Workstation	Windows 11 (VirtualBox VM)
Policy Enforcement	Group Policy Objects (GPOs)
Privileged Access	Privileged Identity Management (PIM)
Access Control	Conditional Access Policies
Risk Management	Identity Protection
Access Certification	Entra ID Access Reviews
Compliance Framework	HIPAA | NIST 800-63 | Zero Trust
 
Lab Architecture
Environment Overview
Component	Details
Server Hostname	NEH01
Domain Name	NEH.local
Server IP	192.168.1.x (static)
Operating System	Windows Server 2016
Workstation OS	Windows 11
Network Type	VirtualBox Bridged Adapter
Entra ID Tenant	*.onmicrosoft.com
Entra ID License	P2 (PIM, Identity Protection, Access Reviews)

Compliance Alignment
Standard	How Lab Addresses It
HIPAA - Unique User ID	Every staff member has a unique AD account, no shared logins
HIPAA - Minimum Necessary	RBAC via security groups, least privilege per role
HIPAA - Audit Controls	Audit logging GPO on all OUs, Entra ID sign-in logs
HIPAA - Automatic Logoff	Screensaver timeout GPOs on all workstations
HIPAA - Access Termination	Disabled OU, account expiry for contractors/vendors
NIST 800-63	12-character minimum password, MFA enforcement
Zero Trust	PIM no standing access, Conditional Access always verify
CIS Controls	GPO restrictions, USB blocking, application control
 
Active Directory OU Structure
Design Principles
•	OUs organize where accounts live and where GPOs apply
•	Security Groups control what resources users can access
•	Disabled OU at top level catches terminated accounts from any department
•	Workstations OU separate from user OUs for computer GPO targeting
•	All human accounts live inside NEH-Users regardless of department

Full OU Hierarchy
NEH.local
├── OU=NEH-Users
│   ├── OU=NEH-IT
│   │   ├── OU=NEH-IT-Support
│   │   ├── OU=NEH-IT-Admins
│   │   ├── OU=NEH-IT-Server-Admins
│   │   └── OU=NEH-IT-Network
│   ├── OU=Clinical
│   │   ├── OU=Physicians
│   │   ├── OU=Nursing
│   │   ├── OU=Pharmacy
│   │   └── OU=Radiology
│   ├── OU=Administrative
│   │   ├── OU=HR
│   │   └── OU=Billing
│   ├── OU=Contractors
│   └── OU=Vendors
├── OU=NEH-Workstation
├── OU=Disabled
└── OU=ServiceAccounts
 
Users and Security Groups
IT Department
User	Title	Security Group
Michael Torres	IT Manager	GRP-IT-Admins
James Carter	IT Engineer	GRP-IT-Admins
Rachel Gomez	IT Engineer	GRP-IT-Admins
David Okonkwo	Helpdesk Technician	GRP-IT-Support
Lisa Tran	Helpdesk Technician	GRP-IT-Support
Kevin Patel	Helpdesk Technician	GRP-IT-Support
Marcus Webb	Server Administrator	GRP-IT-Server-Admins
Angela Brooks	Server Administrator	GRP-IT-Server-Admins
Chris Nguyen	Network Engineer	GRP-IT-Network
Sarah Mitchell	Network Engineer	GRP-IT-Network

Clinical Department
User	Title	Security Group
William Hayes	Attending Physician	GRP-Physicians
Amanda Rivera	Resident Physician	GRP-Physicians
Robert Chen	Chief of Medicine	GRP-Physicians
Patricia Williams	Registered Nurse	GRP-Nursing-RN
Thomas Jackson	Registered Nurse	GRP-Nursing-RN
Sandra Lee	Charge Nurse	GRP-Nursing-Charge
Jennifer Kim	Pharmacist	GRP-Pharmacy-PharmD
Daniel Park	Pharmacist	GRP-Pharmacy-PharmD
Michelle Scott	Pharmacy Technician	GRP-Pharmacy-Tech
Richard Morgan	Radiologist	GRP-Radiology-MD
Elizabeth Turner	Radiologist	GRP-Radiology-MD
Carlos Reyes	Radiology Technician	GRP-Radiology-Tech

Administrative Department
User	Title	Security Group
Angela Brooks	HR Manager	GRP-HR-Manager
Nicole Washington	HR Coordinator	GRP-HR-Staff
Steven Hall	Recruiter	GRP-HR-Staff
Kevin Patel	Billing Manager	GRP-Billing-Manager
Diana Foster	Medical Coder	GRP-Billing-Staff
Raymond Price	Billing Specialist	GRP-Billing-Staff

External Accounts
User	Title	Security Group
John Smith	Travel Nurse	GRP-Contractors-Clinical
Maria Santos	Locum Physician	GRP-Contractors-Clinical
Brian Wallace	Temp Medical Coder	GRP-Contractors-Admin
TechCorp Vendor1	EHR Support Tech	GRP-Vendors-EHR
MedSupply Vendor2	Medical Equipment Rep	GRP-Vendors-Equipment
NetVendor Vendor3	Network Vendor Rep	GRP-Vendors-Network

All contractor accounts expire 09/01/2026. All vendor accounts expire 08/25/2026. Account expiry is set at the AD account level aligned to HIPAA access termination requirements.
 
Group Policy Objects (GPOs)
GPO Stack Overview
GPO Name	Linked To
NEH-Password-Policy	Domain Root (NEH.local)
NEH-IT-Baseline	OU=NEH-IT
NEH-IT-Helpdesk-Policy	OU=NEH-IT-Support
NEH-IT-Admins-Policy	OU=NEH-IT-Admins
NEH-IT-Server-Policy	OU=NEH-IT-Server-Admins
NEH-IT-Network-Policy	OU=NEH-IT-Network
NEH-Clinical-Baseline	OU=Clinical
NEH-Physicians-Policy	OU=Physicians
NEH-Nursing-Policy	OU=Nursing
NEH-Pharmacy-Policy	OU=Pharmacy
NEH-Radiology-Policy	OU=Radiology
NEH-Administrative-Baseline	OU=Administrative
NEH-HR-Policy	OU=HR
NEH-Billing-Policy	OU=Billing
NEH-Contractors-Policy	OU=Contractors
NEH-Vendors-Policy	OU=Vendors

Password Policy — NEH-Password-Policy
Linked at domain root. Password policies only apply when linked at this level.

Setting	Value	HIPAA Rationale
Minimum password length	12 characters	NIST 800-63 requirement
Password complexity	Enabled	Reduces brute force risk
Maximum password age	90 days	Regular rotation reduces breach risk
Minimum password age	1 day	Prevents immediate reuse
Password history	10 passwords	Prevents cycling back to old passwords
Account lockout threshold	5 attempts	Prevents brute force attacks
Account lockout duration	30 minutes	Reduces attack window
Reset lockout counter	30 minutes	Standard security practice

IT Baseline — NEH-IT-Baseline
Setting	Purpose
Audit account management — Success/Failure	Logs all account changes
Audit logon events — Success/Failure	Logs all login attempts
Audit directory service access — Success/Failure	Logs AD object access
HIPAA logon warning message	Legal notice before login

Helpdesk Policy — NEH-IT-Helpdesk-Policy
Setting	Purpose
Block PowerShell and PowerShell ISE	Helpdesk has no scripting need
Block Registry Editor	Prevent system tampering
Block Control Panel	Prevent system configuration changes
Screensaver timeout — 15 minutes	HIPAA automatic logoff requirement

Clinical Baseline — NEH-Clinical-Baseline
Setting	Purpose
HIPAA clinical logon warning message	Legal notice for PHI systems
Block USB drives	Prevent PHI data exfiltration
Block Registry Editor	Prevent system tampering
Audit logon events — Success/Failure	HIPAA audit control requirement
Screensaver timeout — 10 minutes	HIPAA automatic logoff requirement

External Policy — NEH-Contractors-Policy and NEH-Vendors-Policy
Setting	Purpose
External staff logon warning message	Legal notice for external access
Block PowerShell and CMD	No scripting access for external staff
Block Control Panel	No system configuration access
Block Registry Editor	No system tampering
Block USB drives	No data removal from hospital network
Remove Run menu	Close additional launch vectors
Screensaver — 5 min (contractors) / 3 min (vendors)	Stricter timeout for external accounts
Block Task Manager (vendors only)	Maximum restriction for vendor accounts
 
Active Directory Delegation
Overview
Delegation grants security groups specific permissions over AD objects without requiring Domain Admin rights. This aligns with HIPAA minimum necessary access — each group only gets what they need to perform their job function.

Security Group	Delegated Permissions
GRP-IT-Support	Reset user passwords, force password change at next logon
GRP-IT-Admins	Create/delete/manage users, reset passwords, read all user info, manage groups
GRP-HR-Manager	Read all user information
GRP-HR-Staff	Read all user information
GRP-Billing-Manager	Read all user information
GRP-Billing-Staff	Read all user information
 
Shared Drive Configuration
Drive Mapping Overview
Shared drives are automatically mapped to users via Group Policy Drive Maps. Users see their department drive in File Explorer when they log in — no manual path entry required.

Share Path	Access Group	Permission
\\NEH01\NEH-IT-Support	GRP-IT-Support	Read
\\NEH01\NEH-IT-Admins	GRP-IT-Admins	Full Control
\\NEH01\NEH-IT-Server-Admins	GRP-IT-Server-Admins	Full Control
\\NEH01\NEH-IT-Network	GRP-IT-Network	Full Control
\\NEH01\NEH-HR	GRP-HR-Manager, GRP-HR-Staff	Full Control / Read
\\NEH01\NEH-Billing	GRP-Billing-Manager, GRP-Billing-Staff	Full Control / Read

Helpdesk has Read-only access to IT-Support share — they can view procedures and documentation but cannot modify files. This aligns with least privilege principle.
 
Hybrid Identity — Entra Connect
Overview
Microsoft Entra Connect synchronizes on-premises Active Directory objects to Entra ID (Azure AD). This creates a hybrid identity environment where users have one identity that works for both on-prem resources and cloud applications.

Sync Direction	Details
On-prem to Cloud	Users, Groups synced from NEH.local to Entra ID
Sync Method	Password Hash Synchronization
Sync Frequency	Every 30 minutes (default)
Hybrid Join	Windows 11 workstation joined to both NEH.local and Entra ID

What Gets Synced
•	All user accounts from NEH-Users OU
•	All security groups and their memberships
•	User attributes — name, title, department, email
•	Account status — enabled/disabled
•	Password hashes for cloud authentication
 
Conditional Access Policies
Overview
Conditional Access enforces Zero Trust principles by evaluating every sign-in against defined conditions before granting access. All policies are configured in Microsoft Entra ID and apply to cloud application access.

Policy Name	Purpose
NEH-MFA-All-Users	Require MFA for all hospital staff on all cloud apps
NEH-Block-Legacy-Auth	Block legacy authentication protocols that bypass MFA
NEH-MFA-IT-Admins	Require MFA + 4-hour session limit for IT Admins
NEH-Block-Outside-Network	Block access from outside hospital network IP range
NEH-External-Restrictions	MFA + 1-hour session for contractors and vendors
NEH-User-Risk-Policy	High risk users must MFA and change password
NEH-Sign-In-Risk-Policy	Medium/High risk sign-ins require MFA

Policy Details
Policy	Conditions	Controls
NEH-MFA-All-Users	All users, all cloud apps	Require MFA
NEH-Block-Legacy-Auth	All users, Exchange ActiveSync + Other clients	Block access
NEH-MFA-IT-Admins	GRP-IT-Admins, all cloud apps	MFA + 4hr sign-in frequency
NEH-Block-Outside-Network	All users, outside 192.168.1.0/24	Block access
NEH-External-Restrictions	Contractor/Vendor groups, medium/high risk	MFA + 1hr sign-in frequency
NEH-User-Risk-Policy	All users, user risk = High	MFA + password change required
NEH-Sign-In-Risk-Policy	All users, sign-in risk = Medium/High	MFA required
 
Privileged Identity Management (PIM)
Overview
PIM eliminates standing privileged access by making admin roles eligible rather than permanently assigned. IT Admins must request and justify elevated access which is automatically revoked after a set time period. This directly addresses the HIPAA requirement for minimum necessary access on privileged accounts.

Eligible Role Assignments
User	Role	Assignment Type
Michael Torres	User Administrator	Eligible
Michael Torres	Global Reader	Eligible
James Carter	User Administrator	Eligible
Rachel Gomez	User Administrator	Eligible

How PIM Works in This Environment
•	No IT Admin has permanent Global Admin or User Admin rights
•	When elevated access is needed the user activates their eligible role in PIM
•	Activation requires a written business justification
•	Access is granted for a limited time period then automatically removed
•	All activations are logged for HIPAA audit purposes

PIM activation workflow: User opens Entra ID portal → My roles → Activate → Enter justification → Access granted for limited time → Auto-removed on expiry
 
Access Reviews
Overview
Access Reviews automate the HIPAA requirement for periodic access certification. Every 90 days managers are prompted to review and confirm who still needs access to each security group. Anyone not confirmed has their access automatically removed.

Configured Access Reviews
Review Name	Group Reviewed
NEH-Physicians-Quarterly-Access-Review	GRP-Physicians
NEH-Nursing-RN-Quarterly-Access-Review	GRP-Nursing-RN
NEH-Nursing-Charge-Quarterly-Access-Review	GRP-Nursing-Charge
NEH-IT-Admins-Quarterly-Access-Review	GRP-IT-Admins
NEH-HR-Manager-Quarterly-Access-Review	GRP-HR-Manager
NEH-Billing-Manager-Quarterly-Access-Review	GRP-Billing-Manager
NEH-Contractors-Clinical-Quarterly-Access-Review	GRP-Contractors-Clinical
NEH-Vendors-EHR-Quarterly-Access-Review	GRP-Vendors-EHR

Review Settings
Setting	Value
Recurrence	Quarterly (every 90 days)
Duration	14 days for reviewers to respond
Reviewer	Manager of each user
Auto-apply results	Enabled
If reviewer does not respond	Remove access
End date	Never (ongoing)

Quarterly access reviews directly satisfy HIPAA Security Rule 164.308(a)(3) — Workforce Authorization and Supervision — by providing documented evidence that access is regularly reviewed and certified.
 
Security Findings and Remediation
Finding 1 — Application Block Bypass via Batch Files
During lab testing it was discovered that the Don't run specified Windows applications GPO setting can be bypassed by wrapping blocked executables in a .bat batch file. The policy blocks executables by name but does not prevent indirect execution via scripting.

Detail	Description
Finding	PowerShell and CMD blocked by GPO name can be launched via .bat file
Severity	Medium
Affected Policies	NEH-IT-Helpdesk-Policy, NEH-Clinical-Baseline, NEH-Contractors-Policy, NEH-Vendors-Policy
Remediation	Implement AppLocker Script Rules to block .bat, .cmd, .vbs, .ps1 execution
Status	Identified — remediation pending

Remediation: Add AppLocker Script Rules via NEH-AppLocker-Policy linked to NEH-Users OU. Block *.bat, *.cmd, *.vbs, *.ps1 for all non-IT users. Exempt GRP-IT-Admins, GRP-IT-Server-Admins, GRP-IT-Network via security filtering.
 
Portfolio and Resume Talking Points
How to Describe This Lab
•	Designed and implemented a complete enterprise IAM environment for a simulated 500-bed hospital aligned to HIPAA Security Rule requirements
•	Built Active Directory OU structure and RBAC group model covering 5 departments and 30+ user accounts across clinical, administrative, and external staff personas
•	Configured 16 Group Policy Objects enforcing least privilege, application control, USB blocking, audit logging, and HIPAA logon warnings across all hospital departments
•	Implemented hybrid identity architecture using Microsoft Entra Connect to sync on-premises AD to Entra ID, enabling single identity for both on-prem and cloud resources
•	Configured 7 Conditional Access policies enforcing MFA, blocking legacy authentication, restricting access by network location, and applying risk-based access controls
•	Deployed Privileged Identity Management eliminating standing admin access — all privileged roles are eligible only and require time-limited activation with business justification
•	Implemented 8 quarterly Access Reviews automating HIPAA access certification for clinical, administrative, and privileged groups
•	Identified and documented application control bypass vulnerability via batch files during testing — remediation plan documented
•	Automated user provisioning using PowerShell scripts for joiner, mover, and leaver identity lifecycle processes

Key Technologies Demonstrated
Technology	What You Did With It
Active Directory	OU design, user/group management, GPO configuration, delegation
Windows Server 2016	DC promotion, AD DS, DNS, file shares, drive mapping
Microsoft Entra ID P2	Conditional Access, PIM, Identity Protection, Access Reviews
Entra Connect	Hybrid identity sync, password hash synchronization
PowerShell	User provisioning, group management, AD automation
Group Policy	Application control, USB blocking, audit logging, drive mapping
RBAC	Security group design, least privilege enforcement, delegation
Zero Trust	No standing access, always verify, assume breach monitoring
HIPAA	Minimum necessary access, audit controls, access certification

Built as part of NEH Hospital IAM Lab — Windows Server 2016 + Microsoft Entra ID P2
