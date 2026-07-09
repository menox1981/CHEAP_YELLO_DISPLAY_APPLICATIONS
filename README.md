# CYD Applications — Web Flasher

A static, browser-based flashing hub for ESP32 Cheap Yellow Display applications. Built on
[ESP Web Tools](https://esphome.github.io/esp-web-tools/) — visitors click Install, pick their
device's serial port, and the firmware flashes straight from the page. No PlatformIO, no
Arduino IDE, no drivers beyond what Chrome/Edge already have.

Live at: `https://mwalatimothy.github.io/CHEAP_YELLO_DISPLAY_APPLICATIONS/` (once GitHub Pages
is enabled — see "Publishing" below).

Built and maintained by [Timothy Mwala](https://mwalatimothy.github.io/Portfolio-/), embedded
systems engineer. Contact/support details are on the page itself (`#support`).

## Using it

Open the hosted page in **Chrome, Edge, or Opera on desktop** — Web Serial isn't supported in
Firefox, Safari, or any mobile browser. Plug the device in over USB, click Install on the card
for the application/variant you want, and pick the serial port when prompted.

## What's here today: Rolling Clock

Modular clock/weather firmware, offered as three variants:

| Variant | Needs touchscreen? | Notes |
|---|---|---|
| Premium Version | Yes | Original release: multi-provider weather, 3 on-device screens, branding |
| Premium v2 | Yes | Adds access-code lock, on-device keyboard, OpenAI insights, forecast chart, provider auto-recovery |
| Premium v3 | No | Same as v2, built for boards with no touch controller — configured via serial + web dashboard |

Premium v2/v3 are locked behind a one-time access code, purchased manually via PayPal — see the
page's "Premium access" section for the exact flow. There's no payment gateway integration here,
it's a direct pay-then-get-a-code process (details on the page, not repeated here since the
contact/payment info is the kind of thing that changes without a code change).

## Adding future applications

This repo is meant to grow beyond Rolling Clock. To add a new CYD application:

1. Add a new `<section>` to `index.html` (copy the `#rolling-clock` section as a template) with
   its own card grid.
2. Add a manifest per new variant under `manifests/`.
3. Add the corresponding `firmware.merged.bin` under `firmware/<app-name>/<variant>/`.
4. Link the new section from the top nav.

## Publishing (GitHub Pages)

1. Push this repo to `https://github.com/MwalaTimothy/CHEAP_YELLO_DISPLAY_APPLICATIONS`.
2. In the repo's **Settings → Pages**, set the source to `main` / `/ (root)`.
3. GitHub Pages serves over HTTPS automatically, which Web Serial requires — no extra config
   needed.

## Updating firmware

Each variant's PlatformIO project already has a `merge_bin.py` post-build script that produces
`firmware.merged.bin` (bootloader + partitions + app combined at their correct flash offsets) —
that's the exact file format ESP Web Tools expects, pointed at from `offset: 0` in each manifest.

To ship a new build:

1. Rebuild the variant's PlatformIO project (`pio run` — `firmware.merged.bin` appears in that
   project's root automatically).
2. Copy it over the matching file here:
   - `firmware/premium-version/firmware.merged.bin`
   - `firmware/v2/firmware.merged.bin`
   - `firmware/v3/firmware.merged.bin`
3. Bump the `version` field in the matching `manifests/*.json` file (cosmetic — shown in the
   install dialog, not used for compatibility checks).
4. Commit and push.

## Repo layout

```
index.html              the flasher hub page (Rolling Clock section today, more to follow)
manifests/
  premium-version.json  ESP Web Tools manifest for the Rolling Clock Premium Version build
  v2.json                ...for Premium v2
  v3.json                ...for Premium v3
firmware/
  premium-version/firmware.merged.bin
  v2/firmware.merged.bin
  v3/firmware.merged.bin
```

## Caveats

- The access-code gate on v2/v3 is a basic deterrent (a single string built into the firmware),
  not real DRM — see each project's own README for details.
- `new_install_prompt_erase: true` is set in every manifest, so users get an "erase device
  first?" prompt — recommended when someone might be switching between variants, since NVS
  layouts can drift between them over time.
- Payment is a manual PayPal-then-contact flow, not an automated checkout — there's no order
  system or receipt verification built here.
