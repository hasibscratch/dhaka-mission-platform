# Dhaka Mission Visitor Management Platform

**World Bank Bangladesh Country Office**
A mission visitor registration and capacity management system built on Microsoft Power Platform.

---

## Live Demo

> **[View the Interactive Demo →](https://YOUR-USERNAME.github.io/dhaka-mission-platform/)**

The demo is a static HTML prototype hosted on GitHub Pages. It shows the full UI including the capacity calendar, registration form, and admin panel using mock data. No Microsoft account is required to view it.

| Screen | Demo Link |
|---|---|
| Capacity Calendar & Registration | `index.html` |
| Admin Dashboard | `admin.html` (password: `admin123`) |

---

## Repository Structure

```
dhaka-mission-platform/
│
├── README.md                          ← This file
│
├── demo/                              ← Static HTML demo (GitHub Pages)
│   ├── index.html                     ← Calendar + Registration form
│   ├── admin.html                     ← Admin dashboard panel
│   └── assets/
│       └── favicon.ico                ← Optional WB favicon
│
├── sharepoint-schema/                 ← SharePoint list definitions
│   ├── Mission_Registrations.json
│   ├── Daily_Cap_Settings.json
│   ├── Admin_Parameters.json
│   ├── Email_Templates.json
│   ├── Hotel_List.json
│   ├── Admin_Users.json
│   └── Waitlist_Queue.json
│
├── email-templates/                   ← Initial HTML email bodies
│   ├── REGISTRATION_ACK.html
│   ├── ADMIN_ALERT.html
│   ├── APPROVED.html
│   ├── REJECTED.html
│   └── DAILY_DIGEST.html
│
├── solutions/                         ← Power Platform exported packages
│   ├── DhakaMissionApp.msapp          ← Power Apps canvas export
│   └── DhakaMissionFlows.zip          ← Power Automate solution export
│
├── docs/
│   └── WB_Bangladesh_Mission_Platform_Complete_Guide.md
│
└── .github/
    └── workflows/
        └── deploy.yml                 ← Optional: CI/CD for Power Platform
```

---

## Technology Stack

| Layer | Tool | License Required |
|---|---|---|
| Frontend App | Microsoft Power Apps | Microsoft 365 E3/E5 (standard connectors only) |
| Automation & Email | Microsoft Power Automate | Microsoft 365 E3/E5 |
| Database | SharePoint Online Lists | Microsoft 365 E3/E5 |
| Demo / Prototype | Static HTML + GitHub Pages | Free |

---

## Prerequisites Before Building

Before starting the Power Platform build, confirm:

- [ ] Microsoft 365 E3 or E5 license on all admin accounts
- [ ] Power Apps and Power Automate not blocked by WBG tenant policy (check with ITS)
- [ ] SharePoint site creation rights (or request from ITS)
- [ ] Exchange Online enabled for outbound email via Power Automate
- [ ] Admin group email address created (e.g., `bd-mission-admin@worldbank.org`)

---

## Deployment Guide

### Step 1 — Enable GitHub Pages

1. Fork or clone this repository to your GitHub account
2. Go to **Settings → Pages**
3. Source: **Deploy from a branch**
4. Branch: `main` / Folder: `/demo`
5. Click **Save**
6. Your demo will be live at `https://YOUR-USERNAME.github.io/dhaka-mission-platform/`

### Step 2 — Create SharePoint Lists

Use the JSON schema files in `/sharepoint-schema/` as your reference. Create each list in order:

1. `Mission_Registrations` — main registration data
2. `Daily_Cap_Settings` — per-date capacity control
3. `Admin_Parameters` — global settings (MissionCap, MaleBodyCount)
4. `Email_Templates` — editable email content
5. `Hotel_List` — accommodation dropdown options
6. `Admin_Users` — admin access control
7. `Waitlist_Queue` — overflow queue management

Full column-by-column instructions are in `/docs/WB_Bangladesh_Mission_Platform_Complete_Guide.md`.

### Step 3 — Import Power Apps Canvas App

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Click **Apps → Import canvas app**
3. Upload `solutions/DhakaMissionApp.msapp`
4. Update SharePoint data connections to point to your site URL
5. Publish and share

### Step 4 — Import Power Automate Flows

1. Go to [make.powerautomate.com](https://make.powerautomate.com)
2. Click **My flows → Import → Import Package (Legacy)**
3. Upload `solutions/DhakaMissionFlows.zip`
4. Update SharePoint and Outlook connections
5. Turn all flows ON

### Step 5 — Seed Email Templates

1. Go to your SharePoint `Email_Templates` list
2. For each of the 5 rows, paste the corresponding HTML from `/email-templates/`
3. The admin can then edit these at any time from inside the Power Apps admin panel

---

## SharePoint Lists — Quick Reference

| List Name | Purpose | Key Columns |
|---|---|---|
| `Mission_Registrations` | All registration submissions | UPI, FullName, Email, ArrivalDate, Status |
| `Daily_Cap_Settings` | Per-date capacity limits | CapDate, MissionCap, ConfirmedCount, IsBlocked |
| `Admin_Parameters` | Global editable parameters | MaleBodyCount (default 30), MissionCap (default 60) |
| `Email_Templates` | Editable email content | TemplateKey, Subject, Body |
| `Hotel_List` | Accommodation dropdown | HotelName, IsActive |
| `Admin_Users` | App admin access control | AdminEmail, IsActive |
| `Waitlist_Queue` | Overflow management | RegistrationID, QueuePosition |

---

## Email Templates — Token Reference

Use these `{{tokens}}` inside any email Subject or Body in the `Email_Templates` list:

| Token | Value |
|---|---|
| `{{FullName}}` | Registrant full name |
| `{{UPI}}` | UPI number |
| `{{ReferenceNumber}}` | WB-BD-[ID] |
| `{{ArrivalDate}}` | Formatted arrival date |
| `{{DepartureDate}}` | Formatted departure date |
| `{{Accommodation}}` | Hotel name |
| `{{SecurityLearning}}` | Yes or No |
| `{{Purpose}}` | Mission purpose text |
| `{{Status}}` | Current registration status |
| `{{RejectionReason}}` | Admin rejection reason |
| `{{AdminPanelLink}}` | Link to Power Apps admin panel |
| `{{Today}}` | Today's date |
| `{{MissionCap}}` | Daily visitor cap number |
| `{{ConfirmedCount}}` | Confirmed count for today |
| `{{VisitorTable}}` | HTML table of today's visitors (digest only) |

---

## Power Automate Flows

| Flow Name | Trigger | Purpose |
|---|---|---|
| `Dhaka Mission - New Registration Handler` | Item created in Mission_Registrations | Sends acknowledgement to registrant + alert to admin |
| `Dhaka Mission - Status Change Notifier` | Item modified in Mission_Registrations | Sends approval or rejection email to registrant |
| `Dhaka Mission - Daily Admin Digest` | Scheduled 7:00 AM BST daily | Sends morning summary to admin group |
| `Dhaka Mission - Cap Change Handler` | Item modified in Daily_Cap_Settings | Re-evaluates waitlist when cap increases |
| `Dhaka Mission - Parameter Sync` | Item modified in Admin_Parameters | Syncs global cap changes to today's date row |

---

## Admin Panel Access

The Power Apps admin panel is protected by Azure AD. The admin's WBG email must be listed in the `Admin_Users` SharePoint list with `IsActive = Yes`.

For the **GitHub Pages demo**, use password: `admin123`

---

## Contributing / Updating

To update the platform after go-live:

1. Make changes in Power Apps Studio or Power Automate
2. Export the updated `.msapp` or solution `.zip`
3. Replace the files in `/solutions/`
4. Commit and push — the repo always reflects the latest deployed version

---

## Support

For platform issues contact the WB Bangladesh Office admin team.
For Microsoft licensing questions contact WBG ITS: [itsai@worldbankgroup.org](mailto:itsai@worldbankgroup.org)

---

*This platform was designed and documented with the assistance of mAI — World Bank Group's AI research assistant.*
