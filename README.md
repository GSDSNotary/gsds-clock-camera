#  Clock In/Out — GitHub camera module

## Files
- `Code.gs` — Apps Script backend. Existing spreadsheet, face-profile, face-verification, GPS, branch, duplicate-check and attendance-write functions are retained.
- `index.html` — Apps Script attendance interface. It opens the camera module in a separate top-level GitHub Pages window and receives face descriptors using a nonce-protected `postMessage` bridge.
- `camera-index.html` — rename this file to `index.html` in the GitHub Pages repository. It owns the camera stream and face-api.js models.

## Configure before deployment
1. In `Code.gs`, replace `https://YOUR-USERNAME.github.io/gsds-clock-camera/` with the actual GitHub Pages URL (must end with `/`).
2. Verify `SPREADSHEET_ID`. The uploaded source currently contains your `SPREADSHEED iD'. Do not change this blindly; confirm which spreadsheet contains `Log Name`, `timesheet`, and `time log` before deployment.
3. In the GitHub repository, create a public repository named `gsds-clock-camera` (or use another name and match its URL). Upload `camera-index.html` renamed to `index.html` at the repository root.
4. Open repository Settings → Pages. Publish from the `main` branch and `/ (root)`. Wait for the Pages URL to become available. Enable Enforce HTTPS.
5. Copy the exact Pages URL into `CAMERA_PAGE_URL` in `Code.gs` and save.
6. In Apps Script, replace `Code.gs` and `index.html` with the supplied revised files. Run `setupFaceRecognitionSheets()` once if the face sheets have not yet been initialized; approve the requested permissions.
7. Deploy → Manage deployments → Edit → New version → Deploy. Open the `/exec` URL.
8. Click **OPEN SECURE CAMERA**. A popup should open. In that popup click **ALLOW CAMERA ACCESS** and approve the browser prompt. Wait until it says camera and facial recognition are ready. Return to the Apps Script page and test face registration / clock actions.

## Important operation notes
- Both the Apps Script page and GitHub Pages camera popup must remain open during registration and attendance.
- Allow pop-ups for the Apps Script web app. Allow camera access for the GitHub Pages site.
- Test the GitHub Pages URL directly in a browser first. It must be HTTPS.
- The GitHub repository contains public client-side code only. Never put spreadsheet IDs with credentials, API keys, employee face descriptors, or private employee data in the repository. Face descriptors are sent only to the Apps Script page via `postMessage`; the page then calls the existing Apps Script server functions.
- The popup bridge checks the exact configured GitHub Pages origin, the popup window reference, and a random per-window nonce. It is not a substitute for server-side authentication.
- The existing `getResolvedEmail_()` fallback trusts a client-provided email when `Session.getActiveUser().getEmail()` is blank. For a biometric attendance system, configure deployment so the authenticated employee identity is reliably available, or replace that fallback with a verified sign-in method before production use. Otherwise a client can claim another email and attempt to match that employee's face.
- Face-api descriptor matching is not liveness detection; a printed photo or screen replay may be able to spoof it. Do not treat it as high-assurance identity proof without liveness/anti-spoof controls.
## Test checklist
- [ ] GitHub Pages camera page opens directly over HTTPS.
- [ ] Browser asks for camera permission and video preview appears.
- [ ] Face-api models load; status changes to ready.
- [ ] Switch front/rear camera works on mobile where supported.
- [ ] Apps Script page receives camera-ready status.
- [ ] Admin can register an employee with consent.
- [ ] Clock IN and OUT request a fresh descriptor, then call server-side `verifyFaceForClock()`.
- [ ] GPS and branch validation still run in `clock()`.
- [ ] Test duplicate attendance and confirm existing sheet columns are unchanged.
