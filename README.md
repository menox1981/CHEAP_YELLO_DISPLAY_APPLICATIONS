# Rolling Clock — Web Flasher

A static, browser-based flashing tool for the Rolling Clock firmware (ESP32 Cheap Yellow
Display). Built on [ESP Web Tools](https://esphome.github.io/esp-web-tools/) — visitors click
Install, pick their device's serial port, and the firmware flashes straight from the page. No
PlatformIO, no Arduino IDE, no drivers beyond what Chrome/Edge already have.

## Using it

Open the hosted page (see "Publishing" below) in **Chrome, Edge, or Opera on desktop** — Web
Serial isn't supported in Firefox, Safari, or any mobile browser. Plug the device in over USB,
click Install on the card for your firmware variant, and pick the serial port when prompted.

Three variants are offered:

| Variant | Needs touchscreen? | Notes |
|---|---|---|
| Premium Version | Yes | Original release: multi-provider weather, 3 on-device screens, branding |
| Premium v2 | Yes | Adds access-code lock, on-device keyboard, OpenAI insights, forecast chart, provider auto-recovery |
| Premium v3 | No | Same as v2, built for boards with no touch controller — configured via serial + web dashboard |

## Publishing (GitHub Pages)

1. Push this folder to a GitHub repo.
2. In the repo's **Settings → Pages**, set the source to the branch/folder this lives in (e.g.
   `main` / `/ (root)`).
3. GitHub Pages serves over HTTPS automatically, which Web Serial requires — no extra config
   needed.
4. The page will be live at `https://<username>.github.io/<repo>/`.

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
index.html              the flasher page (3 cards, one per variant)
manifests/
  premium-version.json  ESP Web Tools manifest for the Premium Version build
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
