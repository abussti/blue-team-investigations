# Cloud Incident Response (AWS CloudTrail & Google Cloud)

## Overview

This case study documents two cloud security incidents investigated in a SOC simulation environment. The investigations focused on identifying unauthorized access, IAM abuse, and suspicious API activity using audit logs from Amazon Web Services CloudTrail and Google Cloud. The objective was to perform alert triage, reconstruct attacker timelines, assess impact, and document containment and remediation actions following SOC workflows.

# Case 1: AWS CloudTrail 

### Scenario Summary

A threat actor (identified as “agentdarius”) used compromised credentials to perform unauthorized actions in an AWS environment, including enumeration of identities and S3 buckets, creation of backdoor accounts with admin access, and making sensitive data publicly accessible.

## Investigation Findings

### IAM User Compromised:

Identified IAM user: agentdarius by searching CloudTrail logs.

### Source IP:

The actor authenticated from IP 185[.]202[.]237[.]209.

### Reconnaissance Activities:

The actor enumerated identities and policies using the following API calls:

- GetCallerIdentity

- ListUserPolicies

- ListAttachedUserPolicies

- GetPolicy

### Privilege Escalation:

- Created a new user named backdoor.

- Attached the AdministratorAccess managed policy to this user.

### Resource Enumeration:

Enumerated S3 buckets using both ListBuckets and ListObjects.

### Data Exposure:

Identified the bucket siasecrets (ARN: arn:aws:s3:::siasecrets) had its public access block removed and ACL set to public-read, likely exposing sensitive data.

## MITRE ATT&CK Mapping

| Phase  | Technique |
|--------|-----------|
| Initial Access | Valid Accounts |
| Discovery    | Cloud Service Discovery        | 
| Persistence    | Create User / Credential Access        | 
| Privilege Escalation    | Administrative Privilege       | 
| Exfiltration    | Data from Cloud Storage     | 

## Root Cause & Impact

### Root Cause:

- Compromised IAM credentials without enforced multi-factor authentication (MFA).

### Impact:

- Backdoor account with full admin access

- Public exposure of sensitive bucket data

- Potential for further data loss or resource misuse

## Containment & Remediation

- Disable the compromised IAM user (agentdarius) and the backdoor user

- Rotate all access keys

- Restrict policies to start from least-privilege

- Re-enable MFA for all privileged accounts

- Reinstate default S3 bucket public access settings

- Monitor for further suspicious API activity

# Case 2 — Google Cloud Compromise

### Scenario Summary

A developer impersonated another identity in a Google Cloud environment and attempted to exfiltrate a file from a Google Cloud Storage bucket by abusing unauthorized access.

## Investigation Findings

### Compromised Project:

Identified project: cryptostartup

### Compromised Google Cloud Identity:

The compromised google cloud identify found: cloud-storage-helper[@]crytpostartup[.]iam[.]gserviceaccount[.]com

### Source IP & Geolocation:

Source IP: 178[.]132[.]108[.]38

IP orginated from: 🇷🇴 Romania

### Device Used:

Macintosh

### First Failed API Call:

google[.]api[.]serviceusage[.]v1[.]ServiceUsage[.]EnableService

### Bucket Enumerated:

"importantbucket"

### API Call Used for Exfiltration:

storage[.]objects[.]get

### Tool Used:

"gsutil"

### Exfiltrated File:

"secretcode[.]java"

## MITRE ATT&CK Mapping

| Phase  | Technique |
|--------|-----------|
| Initial Access | Valid Accounts |
| Discovery    | Cloud Service Discovery        | 
| Exfiltration    | Data from Cloud Storage     | 
| Credential Access    | Impersonation / Abuse of Identity        | 

## Root Cause & Impact

### Root Cause:

- Compromised identity used to access cloud storage without authorization.

### Impact:

- Unauthorized access to bucket contents

- Successful exfiltration attempt

## Containment & Remediation

- Revoke compromised identity credentials

- Restrict access permissions on storage buckets

- Implement conditional access / MFA where possible

- Add monitoring and alerting on high-risk API calls

## Skills Demonstrated

- Cloud log triage and analysis (AWS CloudTrail & Google Cloud)

- IAM abuse detection and root cause analysis

- Data exposure identification and remediation planning

- MITRE ATT&CK cloud mapping

- Incident response playbook creation
