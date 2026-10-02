JT RELIABLE SOLUTIONS WEBSITE  -  publish it, then get Intuit production keys
==========================================================================

Pages in this folder
  index.html        Home page (services, contact)                 -> Intuit "Host domain"
  privacy.html      Privacy Policy                                -> Intuit "Privacy policy URL"
  eula.html         End-User License Agreement (JT Books)         -> Intuit "EULA URL"
  connect.html      How connecting works                          -> Intuit "Launch URL"
  disconnect.html   Shown after a client disconnects in QBO       -> Intuit "Disconnect URL"
  callback.html     Finishes the QuickBooks sign-in (copy link)   -> Intuit production "Redirect URI"
  style.css         Shared styling

Before publishing
  1. Read privacy.html and eula.html in full. Change anything that is not true for how you work
     (e.g., if you use a different retention period than your WISP, or another payment processor).
  2. Confirm jean.tra@jtreliablesolutions.com receives mail. If not, replace it in all 6 pages.
  3. Have the Georgia attorney who reviews the engagement letter look at both pages in the same visit.

STEP 1 - Publish (free, about 15 minutes). Pick ONE option.

  Option A: Netlify Drop (easiest)
    a. Go to https://app.netlify.com/drop and create a free account.
    b. Drag this whole "website" folder onto the page.
    c. Netlify gives an https address like https://jt-reliable.netlify.app
       (Site settings > Change site name to choose the first part).

  Option B: GitHub Pages
    a. Create a free GitHub account; new public repository named  jtreliablesolutions.github.io
       (use your GitHub username in place of jtreliablesolutions if it differs).
    b. "Add file > Upload files", drag in every file from this folder, Commit.
    c. Settings > Pages: Source = main branch. Address: https://<username>.github.io

  Own domain (optional, ~$12/yr): if you own jtreliablesolutions.com, point it at the site
  (Netlify: Domain settings; GitHub: Settings > Pages > Custom domain). HTTPS is automatic.

  Check: open <your address>/privacy.html on your phone. It must load without signing in.

STEP 2 - Point JT Books at the callback page (1 minute)
  Double-click JT_BOOKKEEPING\automation\set_site_address.bat and paste your site address
  (e.g. https://jt-reliable.netlify.app). It checks all 6 pages load, sets REDIRECT_URI_PRODUCTION in
  config.py, and writes JT_BOOKKEEPING\website_portal_values.txt with the exact Step 3 values to paste.
  If any page fails to load it changes nothing. ENVIRONMENT stays "sandbox" until production keys arrive.
  Why: Intuit does not accept localhost redirect addresses for production keys. With production keys,
  Intuit sends the browser to callback.html, which shows a one-time link; JT Books pops up a box,
  you paste the link, and the connection finishes as before.

STEP 3 - Intuit developer portal (developer.intuit.com > your app)
  Settings / App URLs (replace the address with yours):
     Host domain ............ jt-reliable.netlify.app
     Launch URL ............. https://jt-reliable.netlify.app/connect.html
     Disconnect URL ......... https://jt-reliable.netlify.app/disconnect.html
     EULA URL ............... https://jt-reliable.netlify.app/eula.html
     Privacy policy URL ..... https://jt-reliable.netlify.app/privacy.html
  Keys & credentials (Production) > Redirect URIs:
     https://jt-reliable.netlify.app/callback.html

  App assessment questionnaire - honest answers that match how JT Books works:
     App type / listing ...... Private app, not listed on the App Store; used by JT for its own bookkeeping clients.
     Hosting ................. Runs on JT's own (encrypted) Windows computer; no server; website is static.
     Scope ................... Accounting only (com.intuit.quickbooks.accounting); read-only use.
     APIs .................... Query (Account, Invoice, Attachable); Reports (TransactionList, ProfitAndLoss).
     Frequency ............... Monthly per client (a few hundred calls per client per month at most); no webhooks, no CDC.
     Editions ................ QuickBooks Online (US).
     Tested in sandbox ....... Yes: connect, run, and reconnect (you ran run_monthly.bat 2026-08 sandbox).
                               Before submitting, also test Disconnect from the sandbox company's Apps page
                               and reconnect with connect.bat sandbox, so you can truthfully answer Yes.
     Token refresh ........... Access token refreshed automatically when expired (before each run); refresh token
                               saved after each refresh; reconnect needed if not used for 100 days.
     Errors .................. Rate limits (429) are retried with a wait; other failed calls stop the run and print
                               the HTTP status and response. A failed token refresh (e.g., invalid_grant) stops the
                               run and the client is reconnected. Every call's intuit_tid, HTTP status, time and client are logged to
                               automation\logs\qbo_api.log (no tokens or financial data), and errors show the intuit_tid.
     CSRF .................... Random state value checked on every connection.
     Secrets ................. Client ID/secret stored in a local .env file, not in the code.
     MFA ..................... Yes on the Intuit developer account (turn it on if not already).
     Data shown to others .... Only to the client it belongs to (their monthly report) and their CPA if they ask.
     Security incidents ...... None.

  Intuit may take a few days to review and may ask follow-up questions by email.

STEP 4 - After approval
  Copy the PRODUCTION Client ID/secret into automation\.env (you do this; never send them to anyone),
  set ENVIRONMENT = "production" in config.py, then connect.bat <client_key> with the client on a screen-share.
