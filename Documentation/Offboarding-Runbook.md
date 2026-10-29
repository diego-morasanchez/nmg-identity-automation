# User Account Offboarding

**Document:** SOP-IAM-001
**Author:** Diego Morales-Sanchez
**Raised under:** Ticket NMG-0203
**Approved by:** R. Ito, Privacy Office

## Purpose

Defines the complete sequence for offboarding a user account at
Northstar Medical Group. Before this document, no written
offboarding standard existed.

## Before you begin

- There is a ticket from a named requester.
- Username, display name and department all match.
- No legal hold prevents action on this account.
- The account belongs to a person, not a service.

## Procedure

### 1. Document the current state
Export every attribute and group membership to a timestamped file.
Removed memberships cannot be recovered, so the record is taken
before anything changes.

### 2. Disable the account
Blocks authentication. This is the step that reduces risk, so it
happens as early as the record allows. Stamp the description with
the ticket number.

### 3. Reset the password
A random value nobody holds. If the account is ever re-enabled by
mistake, the old credential must not still work.

### 4. Remove group memberships
Disabling removes no access. Stripping the groups means a
re-enabled account can reach nothing. Domain Users is the primary
group and is skipped.

### 5. Move to the Disabled Users OU
Quarantine, so the account is never mistaken for active staff.
Last, because moving changes the object path and invalidates
earlier references.

## Tooling

All five steps of this procedure are implemented in
`Scripts/Offboard-NMGUser.ps1`.

    .\Scripts\Offboard-NMGUser.ps1 -Username "jdoe" -Ticket "NMG-0214"

Both parameters are mandatory. The script will not run without
an authorising ticket number.

Operating instructions, including every refusal the script can
produce and what to do about each one, are in the repository
README rather than here. This document describes what should
happen. The README describes how to make it happen.

### Why step 4 has a gate in front of it

Removing group memberships is the only step in this procedure
that cannot be reversed. The CSV written moments earlier is the
only record that will ever exist of what the account could
reach, so the script reads that file back from disk and counts
the rows before the removal is reachable.

### Why step 5 goes last

Moving an object changes its distinguished name. Every earlier
step refers to the account at its original location, so a move
performed first would cause the remaining steps to fail against
a path that no longer exists.

## Reporting

`Scripts/Get-NMGOffboardingStatus.ps1` reports how many accounts
have been offboarded, how many are offboarded but not yet moved,
and how many remain. It takes no parameters and makes no changes
of any kind. It is safe for anybody to run at any time.

## Known exceptions

Two accounts, hgrady and rpace, were offboarded before step 5
was implemented and were moved into the Disabled Users OU
manually afterwards. Their evidence files and logs therefore do
not record the move.

## History

This procedure was tooled between Day 7 and Day 10. Earlier
commits refer to the script as Disable-NMGUser.ps1, which was
accurate when it performed two steps. It was renamed once it
performed all five.


## Reporting

`Scripts/Get-NMGOffboardingStatus.ps1` reports how many accounts
have been offboarded, how many are offboarded but not yet moved,
and how many remain. It takes no parameters and makes no changes
of any kind. It is safe for anybody to run at any time.

## Known exceptions

Two accounts, hgrady and rpace, were offboarded before step 5
was implemented and were moved into the Disabled Users OU
manually afterwards. Their evidence files and logs therefore do
not record the move.


## Bulk requests

This procedure describes offboarding a single account against a
single authorising ticket. Requests sometimes arrive as a list
instead, usually as a spreadsheet from Human Resources.

A list is a request, not an authorisation. It must be validated
before any account on it is actioned.

### The file you were sent is evidence

Store the original unmodified, alongside the offboarding evidence
it produced. Cleaning is performed on a copy. The original proves
what was asked for. The cleaned copy proves what was decided.

### Every row is checked before any row is actioned

For each row, confirm:

- The username exists in the directory.
- The account is not already disabled.
- The account belongs to a person, not a service.
- The person is not a current employee.

That last check is the one a spreadsheet cannot help with, and it
is the one that matters most. A row naming a current employee is
indistinguishable, on the page, from a correct row.

### A person reviews the result

Validation produces a report. The report is read by a person, and
that person decides whether to proceed. Validation that feeds
straight into action is not validation, it is a delay.

### How bulk requests are processed
Bulk offboarding runs in three separate phases. Keeping them separate is
the safety control. Merging any two removes it.

**Phase 1, clean.** Trim every field, skip blank usernames, reject anything
not shaped like a username, remove duplicates. Uses the file only.
Changes nothing.

**Phase 2, check and report.** Look up every row in the directory and
record: does it exist, is it already disabled, is it a service account,
does the person still appear to work here. Produces a report. Changes nothing.

**A person reviews and approves the report.** Required. Deciding that a
named person should NOT be offboarded is a judgment call, and only a
person can make it.

**Phase 3, act.** Offboard only the accounts on the approved list. Reads
the approved output of Phase 2, never the original file.

### Why the phases stay separate
A check inside the action loop runs at full speed with nobody watching.
It catches rules (service account, already disabled) but not a current
employee, because that row looks exactly like a valid one.

### Tested
Simulated on 12 August 2026. The unchecked run reported full success
while disabling a current employee and two service accounts, overwriting
four existing records, and saving no evidence for any of the 32 accounts.
See `Documentation/Blast-Radius-Findings.md`.


## Retention and disposal

Retained in the Disabled Users OU for the period the retention
policy defines, then deleted on a documented schedule. Where a
legal hold is in place the clock stops and nothing is deleted
until Counsel releases it in writing.

Disabled accounts are evidence. You disable a leaver.
You do not delete them.

---

Built during the TotalThreat 30-Day Challenge in a simulated
healthcare environment. Northstar Medical Group is fictional.
