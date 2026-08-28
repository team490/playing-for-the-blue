# Deploying playingfortheblue.nz

Static HTML on GitHub Pages. No build step, no dependencies.
Domain and repo details are already baked in. One placeholder is left.

## Current state

| Item | Value |
|---|---|
| Domain | `playingfortheblue.nz` (CNAME file committed) |
| GitHub account | `team490` |
| Repo | https://github.com/team490/playing-for-the-blue (public, `main`) |
| Pages | enabled, built OK, custom domain registered |
| Remaining | Cloudflare DNS records - nothing else |
| GHL form | LD4dK2lxO7FFQZniYveC on links.genesiscrm.co.nz - wired in and tested |

## The form

Wired in and tested at 1440px and 375px. The iframe and `form_embed.js`
inject on click only, so nothing third-party touches the page until someone
presses "Keep me posted". The script resizes the iframe to the form's real
height via postMessage, which is why the data attributes on the iframe
matter - do not strip them.

Cloudflare Turnstile inside the GHL iframe throws console errors on
localhost. That is expected and clears once it runs on the real domain.

### Two things to change inside the GHL builder

1. **Phone is currently a required field.** Make it optional or remove it.
   Mandatory phone numbers for a holding-page mailing list is more personal
   data than is needed, it suppresses signups, and for an audience of police
   staff it is the wrong default. Email alone is enough at this stage.
2. **The submit button is default GHL blue** (`#2563EB`), which fights the
   palette. Restyle it to amber `#F2A33C` with dark text `#2A1806` to match
   the page CTA. Cross-origin iframe, so it cannot be done from our CSS.

**Where the data lands.** The form sits in the Big Ugly Results GHL account,
so enquiries from police staff land in the agency CRM. That is fine for a
holding page, but before this gets promoted anywhere, move it to a dedicated
GHL sub-account for the charity. It keeps the list separate from agency
leads and means it can be handed over cleanly when the entity exists.

## Steps already done (2026-08-28)

1. `gh` CLI installed and authenticated as `team490`.
2. Repo created and pushed:
   `gh repo create team490/playing-for-the-blue --public --source=. --remote=origin --push`
3. Pages enabled from `main` / root:
   `gh api --method POST repos/team490/playing-for-the-blue/pages -f "source[branch]=main" -f "source[path]=/"`
4. Build succeeded. `team490.github.io/playing-for-the-blue` now 301s to
   `playingfortheblue.nz`, confirming the custom domain is registered.

## The only remaining step: DNS

The domain sits on Cloudflare nameservers (`ajay` / `yolanda.ns.cloudflare.com`).
Add five records in the Cloudflare dashboard:

```
A        @      185.199.108.153
A        @      185.199.109.153
A        @      185.199.110.153
A        @      185.199.111.153
CNAME    www    team490.github.io
```

**Set every one of them to "DNS only" (grey cloud), not proxied.** A proxied
record stops GitHub validating the domain, so the certificate never issues
and "Enforce HTTPS" stays greyed out permanently. Once the cert is issued you
can turn the proxy back on if you want, with SSL mode set to Full.

Delete any parking or placeholder record Cloudflare added when the domain was
registered, or it will fight the A records.

Then tick **Enforce HTTPS** on the repo's Settings > Pages screen once it
stops being greyed out (usually 10-30 minutes after DNS resolves).

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
