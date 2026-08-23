# Casa capture

A Firefox and Chrome extension that reads the property pages **you** open and posts what they
already publish to **your own** [Casa](https://github.com/mathieu-fortin/casa), on your own network.

**It does nothing on its own.** Casa is self-hosted software, and this is its companion — without a
Casa instance you can reach and a token from it, this extension has nowhere to send anything and
will sit there doing nothing. That is not a limitation to be worked around; it is the whole design.

- **[Privacy policy](https://mathieu-fortin.github.io/casa-capture/privacy.html)** — what leaves your
  browser, and where it goes.
- **[Releases](https://github.com/mathieu-fortin/casa-capture/releases)** — the signed Firefox add-on
  and the Chrome package.

## What it does, and what it will not do

**It makes no requests of its own to any property site.** There is exactly one `fetch()` in the
whole extension, and its address is the one you type into the options page. The script that runs on
the sites contains no `fetch`, no `XMLHttpRequest`, no `sendBeacon` and no dynamic `import` — a test
in Casa's own suite asserts exactly that on every commit, rather than promising it here.

The four sites this covers all sit behind one anti-bot tenancy and all four refuse an automated
fetch. A served block is a stop signal, so nothing here tries to get past it: the page in front of
you was fetched by you, browsing, as you were going to anyway.

**It extracts nothing clever.** It collects the page's own machine-readable evidence — the
`application/ld+json` blocks and the JSON state blob a framework left in the DOM — and sends it
verbatim. All the understanding lives in Casa, in Python, so when a site redesigns, the fix is a
parser edit and a replay over everything ever captured. You do not reinstall anything and you do not
re-browse a fortnight of listings.

**It shows you a queue. It never works one.** The options page lists pages your Casa says would tell
it the most, best first, with the reason attached. **Every one of them opens because you clicked
it.** The code contains no way to open one — no `tabs.create`, no `window.open`, no assignment to
`location` — and a test greps every script for each of those and fails if one appears.

## Install

### Firefox — desktop and Android

Download `casa-capture-firefox-<version>.xpi` from the
[latest release](https://github.com/mathieu-fortin/casa-capture/releases/latest). It is signed by
Mozilla, so it installs into ordinary release Firefox and **updates itself** from then on.

- **Desktop** — open the `.xpi` link in Firefox and accept the prompt.
- **Android** — there is no web install. Save the file, then
  **Settings → Add-ons → Install add-on from file**. Firefox for Android has supported general
  add-ons since Firefox 120, which is the minimum this declares.

Then, and this catches everybody: **Firefox treats host permissions as opt-in.** Open
**Add-ons → Casa capture → Permissions** and allow access to the sites. Until you do, it sits there
doing nothing, which looks exactly like it being broken.

### Chrome, Edge or Brave — desktop only

Chrome blocks extensions from every source but its own Web Store, so there is no one-click install
and there cannot be one until this is published there. What is left:

1. Download `casa-capture-chrome-<version>.zip` from the
   [latest release](https://github.com/mathieu-fortin/casa-capture/releases/latest) and unzip it
   **somewhere permanent** — Chrome loads it from that path at every start, so it cannot be a
   Downloads folder you tidy up.
2. Open `chrome://extensions` and turn on **Developer mode**.
3. **Load unpacked**, and choose that folder.

Two consequences are permanent rather than teething problems: Chrome shows a *"Disable developer
mode extensions"* warning at every start, and **nothing auto-updates** — a new version means
downloading and unzipping again.

**Chrome on Android cannot run this, or any extension.** It supports none at all. Use Firefox.

## Set it up

Open the options page, enter your Casa's address and a token, and press **Save and test**.

The address is where your Casa's web UI answers — something like `http://192.168.1.20:8000`. It
cannot be written into the extension's manifest, because it is your LAN address and nobody else's,
which is why the extension ships holding no permission for it and asks for exactly that origin when
you press Save.

### About the token

The token is **not a login and not a per-user credential.** It grants nothing that another token
does not: every configured token can post any capture and read the whole queue, and nothing in Casa
is scoped or partitioned by it.

What it buys is **attribution**. Casa records which installation a capture came from, so its logs
can say which one discarded a block or sent something malformed. That is why the label is a device
name rather than a person — `phone`, `mathieu-laptop` — and why each installation should have its
own:

```
CASA_CAPTURE_TOKENS=desktop:<token>,phone:<token>
```

Sharing one token between two devices is not a security downgrade. It costs you the ability to tell
their captures apart, and nothing else.

Casa has no user model at all today, so "two people" and "two browsers" are the same thing to it.

## Licence

MIT. See [LICENSE](LICENSE).
