# SRAIS Tool

Scaling Responsible AI in Startups - a digital self-assessment tool built for ACAII-ACTS AI Institute.

## What This Is

The SRAIS Tool is a single self-contained HTML file that turns the SRAIS questionnaire into a guided, multi-stage digital assessment. It walks an innovator through five stages of AI solution development and scores each stage on two independent dimensions:

- RAI (Responsible AI) - how well the solution follows responsible AI principles at that stage
- SAI (Scalability) - how ready the solution is to scale at that stage

At the end of every stage, the tool shows a subtotal for that stage and a cumulative percentage of the grand total so far. The percentage is expected to be low early on and rise stage by stage as more of the questionnaire is completed.

The five stages, in order:

1. Problem Definition, Objectives, KPIs and Predictors (Features)
2. Data Collection
3. Model Development
4. Solution Deployment
5. Solution Scaling

Grand totals used for the percentage calculation: RAI = 540 points, SAI = 565 points. These are the totals stated in the original SRAIS Tool questionnaire document.

## Responsibility/Scalability Rubric

Each stage summary, the Final Scorecard, and the "Your Submission" screen include a real 3D scatter chart (via Plotly.js, loaded from a CDN only when a rubric is actually shown - it never slows down the initial page load): X = Scalability %, Y = Responsibility %, Z = stage in the lifecycle, with a point plotted for every completed stage at its own (cumulative Scalability %, cumulative Responsibility %). The chart can be rotated by dragging with the mouse. On the stage summary, the rubric builds up one point at a time as the innovator completes each stage - the same numbers already shown in the Cumulative RAI %/SAI % tiles, just visualized as a 3D trajectory. The Final Scorecard shows the complete 5-point trajectory from Problem Definition through Solution Scaling. It is on-screen only - not included in the Excel export or the submission email, both of which already contain the same underlying numbers in table form.

An earlier version of this hand-drew the 3D effect with plain SVG (isometric projection math computed manually). That approach was dropped in favour of Plotly's `scatter3d` because hand-computing 3D projection is fragile - small arithmetic errors repeatedly pushed elements off the visible canvas during development. A real charting library owns that math instead, which is both more reliable and gives genuine mouse-drag rotation for free.

This works for submissions made before this feature existed too, not just new ones - the rubric on "Your Submission" is reconstructed from each response row's recorded Stage, Type, and Score, which have been captured since the very first version of the submission endpoint. Nothing needs to be re-submitted or backfilled for old applications to get the chart.

## Question Layout: True RAI/SAI Pairing

The original questionnaire pairs most RAI and SAI questions side by side in the same row, grouped under a shared principle heading (e.g. "Proportionality and Do No Harm"). This was checked directly against the source document's table structure: of 115 RAI questions and 109 SAI questions, 105 pairs are genuine matched pairs sitting on the same row, while 10 RAI questions and 4 SAI questions have no counterpart at all.

The tool reflects this exactly rather than showing two independently-scrolling lists:

- Each stage renders as a two-column grid with a principle sub-heading spanning both columns whenever the principle changes
- A matched RAI/SAI pair always sits on the same row, side by side, never overlapping
- Where a question has no counterpart, that side shows a plain "No corresponding RAI/SAI question" placeholder instead of being skipped or misaligned - so the two columns never drift out of sync
- This applies identically to the respondent-facing assessment and to Admin > Preview Questions

Scores for individual questions are still hidden from respondents (see below) regardless of which side of a pair they're answering.

## Files

- ACAII_SRAIS_Tool.html - the tool itself. This is the only file needed to run the assessment.
- ACAII_SRAIS_AppsScript.js - backend script that receives submissions and writes them to a Google Sheet.

## Data note

The source questionnaire (SRAIS Tool.docx) had some inconsistencies in a few scoring cells (a handful of blank scores, and one multi-select question whose sub-items were reconstructed from the question text). The stated grand totals of 540 (RAI) and 565 (SAI) are used as the fixed denominators throughout, as given in the source document.

Four questions were also found with broken response options during real-world testing - two with a spurious duplicate "Yes" and no way to select "No", and two more with only a "Yes" option and no "No" at all - and have since been corrected to a normal Yes/No pair:

- Problem Definition SAI: "Do you use a technique for measuring the AI RoI?"
- Model Development RAI: "Do your models demonstrate accuracy, precision, and recall?"
- Model Development SAI: "Do you consider infrastructure, platforms and open-source libraries that support easy development and deployment of the AI system..."
- Solution Deployment SAI: "Do you first build and deploy a simple, working MVP that is easy to use to evaluate initial user responses?"

A full scan of the question bank confirms these were the only four instances of this issue. A manual review of the underlying question bank against the original document is still recommended before relying on this for scoring decisions.

## How to Use the Tool

1. Open ACAII_SRAIS_Tool.html in a browser (works locally, no installation needed)
2. Enter your email address and click "Email My Access Code" - a code is generated, emailed to you, and shown on screen so you can carry on right away
3. Enter that code on the next screen to log in (returning users who already have a code can skip straight to this step via "Already have a code? Enter it")
4. Fill in the innovator name, solution name, and respondent details
5. Answer every question in a stage - all questions are mandatory, and the Next button stays disabled until the stage is complete. Response options do not show their score, so answers are not influenced by which option is worth more. RAI and SAI questions are shown side by side in two columns per stage, matching the original questionnaire layout (they stack on narrow/mobile screens).
6. Review the stage subtotal and cumulative percentage shown at the bottom of each stage, and the overall progress bar at the top of the assessment. You can click any completed segment in the stage stepper to jump back and review or change an earlier stage.
7. After the final stage, review the full scorecard
8. Either download the responses as an Excel file, or submit them directly if the backend has been connected (see below)

## Logging In, Logging Out, and Resuming

Every answer is saved automatically to the browser's local storage as soon as it is selected - not just at the end. This means an innovator can safely close the tab, lose their connection, or get logged out partway through, and pick up exactly where they left off.

To log out deliberately, click "Log Out" in the top right of the header (visible once logged in). This returns to the email/access screen without losing anything - the saved progress stays tied to that access code.

To resume, log back in with the same access code. If there is saved progress for that code, the tool skips straight past the "Before you start" screen and reopens on the exact stage that was left, with every previous answer already selected. If a different, never-used code is entered, the assessment starts fresh with a blank slate - answers are never carried over between different codes.

This resume behavior is per browser/device, since it uses the browser's local storage rather than the server - the same code entered on a different device or after clearing browser data will start over. Once an assessment is fully submitted, its saved local progress is cleared automatically.

## One Email, Multiple Solutions

An innovator/organisation can have more than one AI solution, each assessed separately - so one email is allowed to have more than one access code. Entering an email on the "Get Started" screen now checks for existing codes first:

- First time this email has been seen: a new code is created immediately, same as before.
- Email already has one or more codes: instead of creating another one, the tool shows "Your Solutions" - a list of every code tied to that email, each labelled by its solution name (or "solution name not set yet" if that step hasn't been reached) with a status (Submitted / In progress / Inactive) and a "Log In" button. Inactive codes can't be logged into.
- To assess an additional, different solution under the same email, click "+ Add a New Solution" - this mints a genuinely new code and walks through the same first-time flow.

This replaces the earlier behaviour where every email submission silently created a brand new code with no way to find previous ones - which was the direct cause of "I logged in but my previous answers aren't there": the login was landing on a different, newly-created code rather than the one that actually had the submission.

## Logging In After Already Submitting

Unlike the in-progress resume above (which is local to one browser), a fully submitted assessment is checked against the server itself, so it works from any device. If an innovator logs in with a code that has already been used to submit a complete assessment, they see a read-only "Your Submission" screen instead of being taken back into the questionnaire, showing:

- Innovator/solution/respondent details, submission date, and final RAI%/SAI% scores
- Every question-by-question answer, grouped into the same collapsible per-stage sections used in the Admin > Preview Questions tab (click a stage to expand or collapse it, rather than one long scrolling table)
- A "Download as Excel" button, so the innovator can keep their own copy

There is currently no option to resubmit or edit answers from this screen; if a genuine correction is needed, that has to be handled directly in the spreadsheet or by issuing a new code.

A copy of the submission is also emailed automatically the moment it's submitted, sent to whatever email address is on file for that access code (looked up server-side, not sent by the browser). The email includes the final scores and a per-stage summary, plus a reminder that logging back in with the same code shows the full detail. If no email is on file for that code (e.g. an admin-added code with no email recorded), this step is silently skipped - it never blocks the submission itself.

## Performance Notes

Several things were slowing the tool down and have been fixed:

- The Excel-export library (SheetJS) and the 3D rubric charting library (Plotly.js) used to load eagerly, even though they're large third-party scripts only needed when someone actually clicks "Download as Excel" or views a rubric. Both now load on demand, the first time they're actually needed - so the initial page load never waits on them.
- Logging in used to make two separate requests to the Apps Script backend one after another (check the code, then check for a submission). Google Apps Script Web Apps are inherently slow per request (often 1-3 seconds each), so two sequential calls meant a noticeably slow login. These are now combined into a single `loginCheck` request.
- In the Admin dashboard, switching between the Access Codes, Access Log, and Analytics tabs used to re-fetch that tab's data from Apps Script every single time, even if nothing had changed - so clicking back and forth between tabs meant repeated 1-3 second waits. Each tab's data is now cached in the browser after its first load and reused on later visits. The cache refreshes itself automatically whenever something changes it (approving/deactivating a code, adding a code, deleting a submission), and each tab also has a manual "Refresh" link for forcing a reload.

If pages still feel slow after this, the most likely remaining cause is Apps Script's own latency (which is outside this file's control, and can be worse right after the script has been idle for a while - a "cold start").

## Editing Your Profile

Once logged in - whether mid-assessment, on the "already submitted" screen, or anywhere else in the tool - an "Edit Profile" link is available in the header. It opens a small form to update the Innovator/Organisation Name and AI Solution Name tied to that access code. Saving updates the Access Codes sheet immediately (the same update used when these details are first entered - see the note on the Solution column below) and updates whatever screen you return to. This does not require the admin password; the access code itself is the credential, same as everywhere else in the tool.

## Access Codes

Innovators get their own access code by entering their email on the first screen - no separate signup form, no innovator/solution name needed up front. A code is generated immediately, emailed to that address, and also shown on screen as a fallback, and it is active immediately with no approval step. The innovator and solution name are collected afterwards, once they are logged in, on the "Before you start" screen.

An admin can also add a code directly (Admin > Access Codes > Add a Code Directly), which is useful for handing a code to an innovator in person, and can deactivate any code at any time (self-requested or admin-added) from the Access Codes tab if it needs to be revoked.

Access codes require the Apps Script backend to be deployed and connected (see below) - until then, the email-request screen tells the user the backend is not active yet. The email step uses Apps Script's built-in MailApp, sending from the Google account that owns the script, so no separate email service needs to be configured - it works as soon as the backend is deployed. If an email fails to send (e.g. a daily sending quota is reached), the code is still valid and still shown on screen.

## Admin Dashboard

Click "Admin" on the access screen and enter the admin password. The dashboard has four tabs:

- Responses - every submission (innovator, solution, respondent, date, final RAI % and SAI %), with a View button that expands that submission's full question-by-question answers directly beneath its own row, labelled with the innovator, solution, respondent, and submission date so it's always clear whose responses are showing, and grouped into collapsible per-stage sections (same pattern as Preview Questions and the innovator's own "Your Submission" screen, so a long submission doesn't turn into one giant scrolling table). Clicking View again (now labelled "Hide") collapses it. A Delete button permanently removes a submission.
- Access Codes - every code that has been requested or added, with a Deactivate / Approve button per row (self-signup codes start active, so this is mainly for revoking access later). This is also where new codes are added directly.
- Access Log - every access-code attempt, valid and invalid, most recent first, with the code used and the timestamp. This is the record of who tried to log in and when.
- Analytics - total responses, average/lowest/highest RAI % across all submissions, and a per-innovator bar breakdown of RAI % and SAI %.
- Preview Questions - every stage as a collapsible section, showing the RAI and SAI questions side by side exactly as respondents see them, except scores are visible here (they're hidden from respondents). Click a stage header to expand or collapse it, or use "Expand All" / "Collapse All". This is for reviewing the full question bank without going through the access-code flow or answering anything.

The admin password is set once as a Script Property (see setup steps below) and is not stored anywhere in the HTML file. This is a lightweight access gate suitable for keeping out casual or unauthorised viewing - it is not a substitute for real authentication if the responses are highly sensitive, since the password is sent as a plain request parameter. The same caveat applies to deleting a submission: there is no undo, so use it carefully.

### Editing questions from the Preview tab

Question text, option labels, and scores in the Preview tab are directly editable (click into any of them). This is meant for review and drafting, not live editing: the question bank is embedded as data inside the HTML file itself, not stored in the spreadsheet, so there's nothing on the server for these edits to save to. Clicking "Download Edited Question Bank (JSON)" exports everything currently shown - including any edits made - as a JSON file matching the internal data structure. Sending that file back is how those edits actually get baked into a new version of the tool.

## How to Deploy on GitHub Pages

1. Push this file (and the rest of the ACAII folder, or just this file on its own) to a GitHub repository
2. In the repository settings, go to Pages and set the source to the main branch, root folder
3. GitHub will publish the tool at `https://your-username.github.io/repository-name/ACAII_SRAIS_Tool.html`
4. Share that link with respondents

No build step and no server are required - it is a static HTML file.

## How to Connect Response Storage (Excel via Google Sheets)

GitHub Pages only serves static files, so it cannot write to a spreadsheet directly. The tool instead posts each submission to a small Google Apps Script Web App, which writes the response into a Google Sheet. A Google Sheet can be downloaded as a .xlsx file at any time (File > Download > Microsoft Excel), so this gives you a live, centrally stored, Excel-compatible record of every submission.

Setup steps:

1. Go to sheets.google.com and create a new spreadsheet, e.g. "ACAII SRAIS Responses"
2. Open Extensions > Apps Script
3. Paste in the contents of ACAII_SRAIS_AppsScript.js and save
4. Run the `setupSpreadsheet` function once (creates the Submissions, Response Detail, Access Codes, and Access Log sheets)
5. Set the admin password: Project Settings > Script properties > add a property with key `ADMIN_KEY` and your chosen password as the value
6. Deploy the script: Deploy > New deployment > Web app, execute as yourself, access set to Anyone
7. Copy the deployment URL
8. Open ACAII_SRAIS_Tool.html, find the line `const APPS_SCRIPT_URL = "";` near the end of the file, and paste the URL between the quotes
9. Re-publish the updated HTML file to GitHub Pages
10. From here, innovators can request their own codes and admins approve them from the dashboard - no need to add codes manually in the sheet unless you want to hand one out directly

Until this is configured, the "Submit Responses" button will tell the user to use "Download as Excel" instead, so the tool is usable even before the backend is set up.

## Editing the Question Bank

The full question bank is embedded in ACAII_SRAIS_Tool.html as a single JSON object (`SRAIS_DATA`) near the start of the script section. Each stage lists its RAI questions and SAI questions separately, each with a principle, question text, a type (single-select or multi-select), and a list of response options with their scores. Editing a question means editing this JSON directly in the HTML file.
