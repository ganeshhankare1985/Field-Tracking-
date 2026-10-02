[README.md](https://github.com/user-attachments/files/32975128/README.md)
# Customer Master Upload & Staff Assignment

A working prototype of the field-collection module: upload customers, assign them to field staff, create visit tasks, and record visits on the phone.

**Status: prototype.** Everything runs on sample data inside the browser or on the phone. There is no server, no database and no sign-in yet, so the dashboard and the phone app do not share data.

## What is in this repository

| Path | What it is |
|---|---|
| `index.html` | Admin dashboard. Upload, validation, assignment, tasks, rules, history. |
| `staff/index.html` | Staff app for phones. Works in any iPhone or Android browser. |
| `android/StaffVisits-1.0.apk` | Installable Android app (the staff app, offline, with GPS at check-in). |
| `android/` (other files) | Source of the Android app: two Java files, manifest, icon, bundled page. |

Each page is a single file with no build step and no libraries to install.

## Open it

**On your computer:** download the repository and double-click `index.html`.

**As a website (GitHub Pages):**

1. Repository → **Settings** → **Pages**.
2. Source: **Deploy from a branch**. Branch: `main`, folder: `/ (root)`. Save.
3. After a minute or two the site is live at:
   - Dashboard: `https://<your-username>.github.io/<repository-name>/`
   - Staff app: `https://<your-username>.github.io/<repository-name>/staff/`

A GitHub Pages site is public. Anyone with the link can open it.

## Install the Android app

Needs Android 7 or newer.

1. Copy `android/StaffVisits-1.0.apk` to the phone and tap it.
2. Allow installing from that source when asked ("Install unknown apps").
3. If Play Protect offers to scan the app, let it, then tap **Install**.
4. Open **Staff Visits** and allow location.

The APK was built and its signature verified, but it has not been tested on a physical phone. Try it on one phone before giving it to staff.

There is no iPhone file like an APK. An iPhone app has to go through Apple with a developer account. Until then, iPhone users can open the staff app page in Safari.

## What the dashboard does

- **Upload customers:** Excel (.xlsx) or CSV with the standard 32 columns, a downloadable template and sample file, manual entry, and a simulated LMS sync.
- **Validation before saving:** total, valid, invalid, duplicate, missing GPS, missing staff code, with row-by-row errors and a downloadable error file.
- **Duplicate policy:** update existing customer, reject duplicate, or update loan values only.
- **Daily portfolio upload:** refreshes outstanding, overdue, DPD, bucket, NPA status and due dates without touching customer master data or history.
- **Assignment:** bulk assign and reassign with a reason, auto assign with a review step, drag-and-drop board, and map assignment by radius or village cluster.
- **Rules and masters:** DPD rule engine, territory master, staff capacity.
- **Visit tasks:** create by branch, DPD band, visit type and date; reassign; hand over an absent employee's pending tasks.
- **History:** every assignment is closed with an end date and never deleted. Upload batches are kept. An API log shows which endpoint each action would call.
- **Start empty / Load sample data:** buttons in the header.

## What the staff app does

- Today's visits with counters, sort (nearest, highest DPD, highest overdue) and filters.
- Customer card with **Navigate** (opens Google Maps directions), **Call** and **Check-in**.
- Check-in records the time and, where the phone allows, GPS position, accuracy and distance from the customer's recorded location.
- Visit form by task type: collection outcome (payment, promise to pay, not available, refused, shifted), LUC check, customer audit. Remarks and photo.
- Follow-up tasks are created automatically. Refusals, shifted customers and adverse LUC or audit findings are flagged to the supervisor.
- My customers, My day (collections, PTPs, visit log) and Profile.

## Sample data

- 480 made-up customers in 12 villages across three branches: Kalyan (`KLN`), Nashik (`NSK`), Bhiwandi (`BWD`).
- 23 made-up staff, `EMP101` onward, in six roles.
- Names, mobile numbers and loan figures are invented. None of it is real.

An uploaded file only validates if it uses these branch and employee codes. Real branch and staff masters are not loaded yet.

## Data safety

- **Do not commit customer data.** No customer files, loan data or exports belong in this repository, public or private. The `.gitignore` blocks `.xlsx`, `.xls` and `.csv` for that reason.
- **Do not commit the signing key.** The `.jks` file and its password are kept outside the repository. Any later version of the Android app must be signed with the same key, or phones will refuse to update it.
- Data entered in the pages stays in that browser or phone. Nothing is sent to a server.

## Assumptions made where the specification was silent

1. Missing GPS and missing staff code are warnings; the row still imports. Invalid rows and duplicates inside the file are rejected.
2. Duplicates are matched on loan account number, then customer ID within the same partner.
3. Required columns: `customer_id`, `loan_account_number`, `customer_name`, `mobile_number`, `branch_code`.
4. The primary owner is always a Collection Officer. Auto assign tries the territory master first (village, then pincode), then the least-loaded officer in the branch, and stops at the officer's maximum customers.
5. No visit task is created for a customer with no owner. They are counted and reported as skipped.
6. Collection visits for DPD 0 stay with the primary owner. Other DPD bands go to the role set in the rule engine.
7. A collection recorded on the phone does not change DPD or overdue. Those change only with the daily portfolio upload.

## Not built yet

- Server and database, staff sign-in and roles.
- Live link between dashboard and phones: tasks down, visit outcomes up.
- Real branch, staff and territory masters.
- Real LMS or banking API connection.
- Background speed and route tracking, push notifications, direct camera capture.
- iPhone app.
- Road or satellite base map on the dashboard.

## Rebuilding the Android app

The app is a small wrapper that shows `android/assets/index.html` full screen and gives it GPS, the file picker and the back button.

- Package `com.hmpl.staffvisits`, minimum Android 7 (API 24), target API 29.
- Permissions: fine and coarse location. No internet permission.
- Version 1.0 was built with `javac`, `dx`, `aapt2` and `jarsigner` (v1 signature).
- A developer can place these files in a new Android Studio project and build there. For the Play Store, or for a target of API 30 and above, sign with `apksigner` (v2/v3) instead.

To update the app after changing the staff page, copy the new `staff/index.html` content into `android/assets/index.html`, raise `versionCode` in the manifest, rebuild and sign with the same key.
