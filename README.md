# NMG Identity Automation With Powershell

PowerShell tooling for identity lifecycle management, built for a fictional company called
Northstar Medical Group.

## The Problem

Northstar's offboarding depended on one person emailing IT whenever
someone left. When she retired, those emails stopped, and no one
caught it for 102 days.

The manual cleanup took 11 hours and found 23 stale accounts, but it
only worked off payroll records. It missed contractors, service
accounts, and anyone whose departure was never logged.

## The Approach

Rather than comparing directory records against payroll records, these
tools query the domain controller directly for the last authentication
date of every account. That value does not depend on paperwork being
filed correctly or names matching between systems.

## Tools

### Find-StaleAccounts.ps1

Identifies enabled accounts that have not authenticated within a given
number of days, including accounts that have never authenticated.
Exports a timestamped CSV plus a summary recording the exact query used.

    .\Find-StaleAccounts.ps1
    .\Find-StaleAccounts.ps1 -Days 30
    .\Find-StaleAccounts.ps1 -Days 180 -IncludeDisabled

| Parameter | Type | Default | Description |
|---|---|---|---|
| `-Days` | int | 90 | Days without authentication before an account is considered stale |
| `-ReportPath` | string | C:\Reports | Where reports are written |
| `-IncludeDisabled` | switch | off | Include disabled accounts in results |

**Output:** a timestamped CSV of findings, and a summary file recording
the question that produced them so the report can be reproduced.

## Repository Structure

    Scripts/        PowerShell tools
    Documentation/  Runbooks and process documentation
    Evidence/       Sample output and verification screenshots
    Logs/           Execution logs

## Environment

Windows Server with Active Directory Domain Services.
Requires the ActiveDirectory PowerShell module.

## About

Built during the TotalThreat 30-Day Challenge in a simulated
healthcare environment. Northstar Medical Group is fictional.

Author: Diego Morales-Sanchez

