# Blast Radius Test: Bulk Offboarding With No Checks

**Date:** 12 August 2026
**Run by:** Diego Morales-Sanchez
**Environment:** Simulator only. No real or lab accounts were touched.
**Source file:** NMG_Separations_Q2Q3.csv, 40 rows, as received

## What was tested
A 4-line script that reads the separations list, disables each account,
and removes its groups. It does no checking, and it does not use
Offboard-NMGUser.ps1.

## Result
It finished in 4.12 seconds and said "40 processed, 0 failures."
That was true, but it only tells you what the script did, not whether
it should have done it.

| Measure | Result |
|---|---|
| Accounts changed | 32 |
| Current employees disabled | 1 |
| Service accounts disabled | 2 |
| Old offboarding records overwritten | 4 |
| Errors the script ignored | 8 |
| Evidence files saved | 0 |
| Log entries written | 0 |
| Ticket numbers recorded | 0 |

## The worst part
Nothing was saved before group memberships were removed. Active Directory
does not keep a history of removed groups, so there is no record anywhere
of what those 32 accounts had access to. Everything else here can be fixed.
This cannot.

## Why my existing safety checks didn't help
All my checks live inside Offboard-NMGUser.ps1. This script never called
it, so the checks were never reached. They weren't broken, just skipped.

## Same file, with checks
| Measure | No checks | With checks |
|---|---|---|
| Accounts changed | 32 | 26 |
| Current employees hit | 1 | 0 |
| Service accounts hit | 2 | 0 |
| Records overwritten | 4 | 0 |
| Evidence files | 0 | 52 |
| Held for a person to review | 0 | 14 |

Extra time: 0.19 seconds.

## Recommendation
Bulk offboarding runs in 3 separate steps:
1. Clean the list. Changes nothing.
2. Check every row against the directory and write a report. Changes nothing.
3. A person approves the report. Only the approved list gets acted on,
   never the original file.

If checking feeds straight into action, nobody gets a chance to review it.

---
Written during the TotalThreat 30-Day Challenge in a simulated healthcare
environment. Northstar Medical Group is fictional.