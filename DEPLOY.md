# Deploying the holding page to GitHub Pages

Everything here is static. No build step, no dependencies. Push it and it
is live.

## Before you push - three placeholders to replace

Search the folder for these and swap them:

| Placeholder | Replace with | Appears in |
|---|---|---|
| `REPLACE-DOMAIN` | the bare domain, e.g. `playingfortheblue.nz` | `index.html`, `robots.txt` |
| `REPLACE-EMAIL` | the enquiries email address | `index.html` (3 times) |

Also create a `CNAME` file in this folder containing nothing but the domain:

```
playingfortheblue.nz
```

No `https://`, no trailing slash. GitHub reads this file to attach the
custom domain.

## Steps

1. **Create the repo** on github.com. Public. Name it after the project.
   Do not add a README or .gitignore - the repo needs this folder's files
   at its root.

2. **Push this folder as the repo root:**

   ```bash
   cd "clients/playing-for-the-blue/site/v1"
   git init -b main
   git add .
   git commit -m "Holding page"
   git remote add origin https://github.com/USERNAME/REPO.git
   git push -u origin main
   ```

3. **Turn on Pages:** repo Settings > Pages > Source: "Deploy from a
   branch" > Branch: `main` > Folder: `/ (root)` > Save.

4. **Set the custom domain:** same Settings > Pages screen, enter the
   domain, Save. Tick "Enforce HTTPS" once it becomes available (it is
   greyed out until the certificate is issued, usually 10-30 minutes).

5. **Point the DNS at GitHub.** At the registrar where the domain was
   bought:

   For an apex domain (`playingfortheblue.nz`), four A records:

   ```
   A    @    185.199.108.153
   A    @    185.199.109.153
   A    @    185.199.110.153
   A    @    185.199.111.153
   ```

   And one CNAME so the www version works too:

   ```
   CNAME    www    USERNAME.github.io
   ```

   If you would rather run it on `www` only, skip the A records and just
   set the CNAME, then enter `www.playingfortheblue.nz` as the custom
   domain in GitHub.

6. **Wait.** DNS takes anywhere from 10 minutes to a few hours. GitHub
   shows a green tick on the Pages settings screen when the certificate
   is issued.

## Checks once it is live

- Loads over `https://` with no certificate warning
- `www` and non-`www` both resolve
- Both mailto buttons open a mail client with the right subject line
- Renders correctly at 375px wide
- View source and confirm no `REPLACE-` placeholders survived

## Notes

- **Ownership:** the repo should sit under the client's GitHub account, or
  be transferred to them once one exists. Do not leave a police charity's
  web presence permanently attached to a personal account.
- **No analytics on purpose.** Adding a tracker means collecting data on
  police staff without a privacy policy. Leave it off until there is an
  entity and a policy.
- **The mailto buttons are deliberate.** A signup form needs a backend and
  somewhere to store police email addresses. Not worth the exposure for a
  holding page. If a real list is needed later, the cheapest safe option is
  a hosted form service with the list owned by the charity, not by us.
