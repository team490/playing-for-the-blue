# Deploying playingfortheblue.nz

Static HTML on GitHub Pages. No build step, no dependencies.
Domain and repo details are already baked in. One placeholder is left.

## Current state

| Item | Value |
|---|---|
| Domain | `playingfortheblue.nz` (CNAME file committed) |
| GitHub account | `team490` |
| Local repo | committed on `main`, no remote yet |
| Outstanding | GHL form ID - see below |

## The one remaining placeholder

`index.html` contains:

```
https://links.biguglyresults.com/widget/form/REPLACE-GHL-FORM-ID
```

Create the form in GoHighLevel, copy the form ID out of its embed code,
and swap it in. Until then the modal opens to an empty white panel because
the iframe 404s.

The form loads on click only. Nothing from leadconnectorhq touches the page
until someone presses "Keep me posted", so the holding page stays fast and
does not phone home for visitors who never interact.

**Where the data lands.** The form sits in the Big Ugly Results GHL account,
so enquiries from police staff land in the agency CRM. That is fine for a
holding page, but before this gets promoted anywhere, move it to a dedicated
GHL sub-account for the charity. It keeps the list separate from agency
leads and means it can be handed over cleanly when the entity exists.

## Steps to go live

1. **Create the repo** at github.com/new under `team490`. Public. Name it
   `playing-for-the-blue`. Do NOT tick "Add a README" - the repo needs this
   folder's files at its root.

2. **Push:**

   ```bash
   cd "clients/playing-for-the-blue/site/v1"
   git remote add origin https://github.com/team490/playing-for-the-blue.git
   git push -u origin main
   ```

3. **Turn on Pages:** Settings > Pages > Source "Deploy from a branch" >
   Branch `main` > Folder `/ (root)` > Save.

4. **Custom domain:** same screen, enter `playingfortheblue.nz`, Save.
   The committed CNAME file usually fills this in automatically. Tick
   "Enforce HTTPS" once it stops being greyed out (10-30 min while the
   certificate is issued).

5. **DNS at the registrar.** Four A records for the apex, one CNAME for www:

   ```
   A        @      185.199.108.153
   A        @      185.199.109.153
   A        @      185.199.110.153
   A        @      185.199.111.153
   CNAME    www    team490.github.io
   ```

   Do not add an A record pointing at anything else, and remove any parking
   or redirect records the registrar added when the domain was bought.

6. **Wait.** DNS takes 10 minutes to a few hours. GitHub shows a green tick
   on the Pages settings screen when the certificate is issued.

## Checks once it is live

- Loads over `https://` with no certificate warning
- `www` and apex both resolve
- "Keep me posted" opens the modal and the GHL form renders inside it
- Form submission actually arrives in GHL
- Renders correctly at 375px wide
- View source: no `REPLACE-` placeholders survive

## Notes

- **Ownership.** The repo sits under Ash's personal account for now.
  Transfer it to the charity's account once the entity exists. Do not leave
  a police charity's web presence permanently attached to a personal login.
- **No analytics on purpose.** A tracker means collecting data on police
  staff without a privacy policy. Leave it off until there is an entity and
  a policy. The GHL form is the only third-party call on the page, and it
  only fires on click.
