---
description: Rebrand and reword the Windows crash dialog by editing two JSON files
---

# Crash Dialog Branding

The Windows crash dialog reads its appearance from `theme\theme.json` and its text from `theme\strings.en-US.json`, both at runtime. Nothing is compiled: edit a file, run the reporter, see the change. There is no Visual Studio project to open and no resource DLL to rebuild.

{% hint style="warning" %}
This replaces the old `BugSplatRc.dll` workflow. If you previously customized the dialog by editing `.rc` files and rebuilding a resource-only DLL, that DLL no longer exists and none of that work carries forward — your customizations need to be re-expressed in `theme.json`. See [How the Windows Crash Reporter Works](../../introduction/getting-started/integrations/desktop/cplusplus/how-the-windows-crash-reporter-works.md).
{% endhint %}

For macOS, see the [Crash Reporter Customization](../../introduction/getting-started/integrations/desktop/macos.md#crash-reporter-customization) section of the macOS guide.

### Where the files live 📁

The theme folder ships in the SDK's `bin` folder and belongs next to `BugSplatReporter.exe`, which is next to your executable:

```
YourApp\
  YourApp.exe
  BugSplat.dll
  BugSplatMonitor.exe
  BugSplatReporter.exe
  BugSplatWer.dll
  theme\
    theme.json            colours, type, layout, switches
    strings.en-US.json    every word the dialog shows
    logo.png              optional, referenced by brand.logo
```

{% hint style="info" %}
The whole folder is optional. `BugSplatReporter.exe` carries a built-in copy of both files, and the `theme.json` that ships in the SDK is an exact copy of those built-in values — so deleting it changes nothing, and you can always diff your file against the shipped one to see what you actually changed.
{% endhint %}

### Seeing your changes without crashing anything 👀

`BugSplatReporter.exe` has a preview mode. It renders the real dialog against a sample crash folder and **never uploads anything**, so you don't have to crash an application or pollute your crash database to check a colour.

```batch
BugSplatReporter.exe --preview
BugSplatReporter.exe --preview --theme "C:\work\my-theme"
BugSplatReporter.exe --preview --scale 200
```

`--theme` accepts either the folder or the `theme.json` file itself, and works in every mode, not just preview. `--scale` renders as though the display were at that percentage, so you can check high-DPI scaling on a 100% monitor.

### Editing `theme.json` 🎨

Only write the keys you are changing. Everything you leave out keeps its default. This is a complete, valid theme:

```json
{
  "palette": {
    "light": { "accent": "#7A3E9D", "accentHover": "#8E4FB3" },
    "dark":  { "accent": "#B180D8", "accentText": "#1A1A1A" }
  },
  "brand": { "logo": "acme-logo.png" }
}
```

#### Top level

| Key | Type | Default | Notes |
| --- | --- | --- | --- |
| `schemaVersion` | integer | `1` | The schema the file was written against. A higher number still loads. |
| `appearance` | `"system"` \| `"light"` \| `"dark"` | `"system"` | `"system"` follows the end user's Windows app-theme setting. The other two pin it. |

`appearance: "system"` is almost always the right answer. The crash dialog appears at the worst moment of someone's day; matching the desktop they're already looking at is one less surprise.

#### `brand`

| Key | Type | Default | Effect |
| --- | --- | --- | --- |
| `productName` | string | `"BugSplat"` | Available to string files as `{productName}`. Doesn't appear anywhere on its own. |
| `logo` | string | `""` | Image file in the theme folder, drawn in the banner. `""` means the built-in BugSplat logo. |
| `logoHeight` | integer, 0–240 | `0` | Height in DIPs to fit the logo into. `0` means "as tall as the banner allows". The logo is never upscaled past its natural size. |
| `logoAlignment` | `"start"` \| `"center"` \| `"end"` | `"start"` | Horizontal position in the banner. `"start"` is the left edge in a left-to-right language. |

#### `palette`

Two objects, `light` and `dark`, with the same fifteen keys. Colours are `#RGB`, `#RRGGBB` or `#RRGGBBAA` — names like `red` and `rgb(...)` are rejected. The alpha pair is accepted so a theme round-trips through a web colour picker, but it's ignored; the dialog paints opaque.

| Role | Light | Dark | Where it shows |
| --- | --- | --- | --- |
| `background` | `#F3F3F3` | `#202020` | The dialog face. Everything sits on this. |
| `surface` | `#FFFFFF` | `#2D2D2D` | Text field interiors, the file list body, secondary button fill. |
| `surfaceAlt` | `#F6F6F6` | `#383838` | A pressed or hovered secondary button, a disabled field, the progress bar's track. |
| `bannerBackground` | `#FFFFFF` | `#FFFFFF` | The strip the logo sits on. See the note below. |
| `textPrimary` | `#1A1A1A` | `#FFFFFF` | The headline, field text, list rows, secondary button captions. |
| `textSecondary` | `#606060` | `#B4B4B4` | Body copy, field labels, the progress dialog's status column. |
| `textDisabled` | `#A0A0A0` | `#767676` | A disabled button's caption. |
| `accent` | `#0F6CBD` | `#4C9EEB` | The primary button, the checked consent box, the focused field's outline, the progress bar. |
| `accentHover` | `#115EA3` | `#60ACF0` | Primary button under the pointer. |
| `accentPressed` | `#0C4B85` | `#4086C8` | Primary button while held down. |
| `accentText` | `#FFFFFF` | `#000000` | Text and the check mark drawn **on top of** `accent`. |
| `border` | `#D1D1D1` | `#424242` | Field outlines, secondary button outlines, horizontal rules. |
| `borderStrong` | `#8A8A8A` | `#6E6E6E` | A field or button under the pointer; the unchecked consent box's outline. |
| `focus` | `#1A1A1A` | `#FFFFFF` | The outer half of the keyboard focus ring. |
| `error` | `#C42B1C` | `#FF99A4` | A failed upload's progress bar. |

You don't have to supply both variants. Supplying only `dark` leaves light mode at the built-in colours.

{% hint style="info" %}
**`bannerBackground` defaults to white in both variants.** That's a workaround, not a design choice: the BugSplat logo that ships in the reporter has an opaque white background, so in dark mode the banner would otherwise read as a white strip clipped across the top. If you supply **your own logo with a transparent background**, set `bannerBackground` to whatever you like — the banner honours the image's alpha channel and composites correctly.
{% endhint %}

#### `type`

| Key | Type | Default | Range | Effect |
| --- | --- | --- | --- | --- |
| `family` | string | `"Segoe UI"` | ≤ 256 chars | Font family for every control. If it isn't installed, Windows substitutes and the dialog still lays out correctly, because it measures whatever font it actually got. |
| `baseSize` | integer | `9` | 6–24 | Point size for body text, labels, fields and buttons. |
| `headingSize` | integer | `13` | 6–48 | Point size for the headline. |
| `headingWeight` | integer | `600` | 100–900 | `400` normal, `600` semibold, `700` bold. |

Sizes are in **points**, converted to pixels at the window's real DPI, so 9 pt is 9 pt at 100%, 150% and 300%.

#### `layout`

Measurements are in DIPs (1 DIP = 1 px at 100% scaling) and are multiplied by the window's DPI scale at runtime.

| Key | Type | Default | Range | Effect |
| --- | --- | --- | --- | --- |
| `windowWidth` | integer | `440` | 320–1200 | Width of the dialog's client area. |
| `padding` | integer | `20` | 0–64 | Margin between the dialog edge and its content. |
| `spacing` | integer | `14` | 0–48 | Vertical gap between blocks. |
| `controlRadius` | integer | `4` | 0–24 | Corner radius of buttons, fields and the consent box. `0` is square. |
| `roundedWindow` | boolean | `true` | | Windows 11 rounded window corners. A no-op on Windows 10. |
| `mica` | boolean | `true` | | Windows 11 Mica backdrop. A no-op on Windows 10. |
| `banner` | boolean | `true` | | Whether there's a logo strip at all. |
| `bannerHeight` | integer | `64` | 0–240 | Height of the strip. `0` also hides it. |
| `descriptionLines` | integer | `5` | 1–20 | Visible lines in the description box. It scrolls beyond that; the field accepts 500 characters regardless. |

**Window height is not settable.** It's computed from the text, so a longer translation grows the window instead of being clipped. Per-control positions aren't settable either — the dialog is a single vertical stack (banner, headline, body, description, contact note, name/email, consent, buttons). You can turn blocks off with `features`, but you can't reorder them.

#### `features`

| Key | Type | Default | Effect |
| --- | --- | --- | --- |
| `showName` | boolean | `true` | Show the optional Name field. Hiding it widens Email to the full content width. |
| `showEmail` | boolean | `true` | Show the optional Email field. |
| `showConsent` | boolean | `false` | Show the consent check box. While it's shown and unticked, **Send Error Report** is disabled. |
| `showDetailsButton` | boolean | `true` | Show **View Report Details**, which lists the files in the report. Hiding it doesn't change what's uploaded. |
| `requireEmail` | boolean | `false` | **Send Error Report** stays disabled until Email contains something with an `@` that isn't at either end. |
| `autoCloseSeconds` | integer, 0–3600 | `0` | Send the report and close the dialog after this many seconds. `0` disables it. |

Hiding both `showName` and `showEmail` also hides the paragraph above them, since it exists only to explain those fields.

`autoCloseSeconds` is for machines with nobody sitting at them — kiosks, build agents, servers, test rigs. The countdown **sends** the report; it never discards it. Any keystroke, click, tick of the consent box or opening of the details window cancels the countdown permanently.

### Swapping the logo 🖼️

Put your image in the theme folder and name it in `brand.logo`:

```json
{ "brand": { "logo": "acme-logo.png", "logoHeight": 40, "logoAlignment": "center" } }
```

`logo` is the only key that names a file, so it's constrained tightly:

* A **plain file name** only — no `\`, no `/`, no `..`, no drive letter, no UNC path. The file must sit directly in the theme folder.
* The extension must be `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.tif` or `.tiff`.
* The file must be **4 MB or smaller**, and no more than **8192 pixels** on each side.
* Decoding must finish within 3 seconds.

Anything that fails a check falls back to the built-in logo and logs a line. Nothing about a bad image can delay or prevent a report being sent.

{% hint style="info" %}
**Recommended asset:** a PNG with a **transparent background**, roughly 3× the height you want so it stays sharp at 200% and 300% scaling — for example 1200 × 120 for a 40 DIP logo. A wide wordmark works better than a square mark, because the banner is a strip.
{% endhint %}

### Editing the text ✍️

Every word the dialog shows lives in `theme\strings.en-US.json`. To change the English wording, edit that file. To add a language, add a file.

```json
{
  "locale": "en-US",
  "direction": "ltr",
  "fontFamily": "",

  "strings": {
    "headline": "{appName} has stopped working.",
    "send": "&Send Report",
    "consent": "Allow Acme to store this information for up to one year."
  }
}
```

Save as **UTF-8**. Write the language directly — no escaping needed. Comments aren't accepted; `//` and `/* */` aren't JSON, and a file containing them is rejected whole.

The keys, and their English defaults:

**The crash dialog**

| Key | English |
| --- | --- |
| `title` | Crash Report |
| `headline` | A problem has caused your program to close. |
| `body` | Reporting this error will help us make our product more reliable… |
| `descriptionLabel` | Please describe the events just before this dialog appeared: |
| `contactNote` | Contact information below is optional… |
| `nameLabel` | Name (optional) |
| `emailLabel` | Email address (optional) |
| `consent` | Allow BugSplat to store this information for a period of up to one year. |
| `send` | `&Send Error Report` |
| `dontSend` | `&Don't Send` |
| `details` | `&View Report Details` |

**The progress dialog**

| Key | English |
| --- | --- |
| `progressTitle` | Sending error report |
| `progressContacting` | Contacting server |
| `progressPackaging` | Generating error report |
| `progressUploading` | Posting data |
| `progressDone` | Done |
| `progressFailed` | Failed |
| `thankYou` | Thank you for sending this error report. It has been received successfully. |
| `close` | `&Close` |

**The report details window**

| Key | English |
| --- | --- |
| `filesTitle` | Report Details |
| `filesBody` | A crash report has been generated for you, containing the files listed below… |
| `filesColumnFile` | File |
| `filesColumnPath` | Path |
| `ok` | OK |
| `cancel` | Cancel |

#### Ampersands and placeholders

`&` marks the **keyboard mnemonic** — `&Send Error Report` means Alt+S. It isn't drawn; it underlines the following letter when the user presses Alt. Pick a letter that isn't already taken by another control in the same dialog, and one that makes sense in your language rather than copying the English. To show a literal ampersand, write `&&`.

Three placeholders are available in any string:

| Placeholder | Value |
| --- | --- |
| `{appName}` | The crashing application's name, from the crash report. |
| `{appVersion}` | Its version, from the crash report. |
| `{productName}` | `brand.productName` from `theme.json`. |

A placeholder the reporter doesn't recognise is left in the text as written, so a typo shows up as `{appNmae}` rather than silently disappearing. `{appName}` and `{appVersion}` can legitimately be empty — don't build a sentence that reads wrong without them.

### Adding a language

Copy `strings.en-US.json` to `strings.<bcp47>.json`, translate the values, and leave the keys alone. Set `direction` to `"rtl"` for Arabic, Hebrew, Persian or Urdu — the whole dialog mirrors. Set `fontFamily` if your theme's font has no glyphs for the script (Segoe UI has no CJK coverage); the size and weight still come from `theme.json`, so the typographic scale stays consistent.

```json
{ "locale": "ja-JP", "fontFamily": "Yu Gothic UI", "strings": { } }
```

The reporter asks Windows which language to use — there's nothing to configure. This matters more for a crash dialog than for most software: **the person reading it is not the person who installed the SDK.** You can't know that a particular crash will be seen by someone whose Windows is in Portuguese. Ship the files and the right one appears.

Resolution follows RFC 4647 lookup. Each of the end user's preferred languages is tried whole, then with trailing subtags dropped, before moving to the next preference, with `en-US` as the final fallback. A user whose preferences are `pt-BR` then `fr-CA` resolves in this order:

```
strings.pt-BR.json      exact
strings.pt.json         region dropped
strings.fr-CA.json      next preference
strings.fr.json         region dropped
strings.en-US.json      the default
```

So a single `strings.pt.json` serves `pt-BR`, `pt-PT` and `pt-AO`.

Keys are resolved **per key**, layered over the English compiled into the reporter and then over `strings.en-US.json` from disk. A key your translation hasn't covered yet keeps the value from the layer underneath, so a partial translation shows partly in the user's language and partly in English — it never shows a blank label. Delete a key you haven't translated rather than leaving a copy of the English in place; it's easier to find later.

{% hint style="info" %}
**`en-US` is the only file that ships today.** Detection, the fallback chain, right-to-left layout, the font override and the measured layout that lets a longer language grow the window are all implemented and tested, so adding a language is adding a file.
{% endhint %}

### A worked example 💼

A dark, high-contrast theme for a product called Acme Rocket Sled: a custom logo centred on a banner that matches the dialog background, consent required before sending, no name field, and reworded copy.

`theme\theme.json`:

```json
{
  "schemaVersion": 1,
  "appearance": "dark",

  "brand": {
    "productName": "Acme Rocket Sled",
    "logo": "acme.png",
    "logoHeight": 40,
    "logoAlignment": "center"
  },

  "palette": {
    "dark": {
      "background": "#12131A",
      "surface": "#1D1F2B",
      "surfaceAlt": "#282B3A",
      "bannerBackground": "#12131A",
      "textPrimary": "#F2F3FF",
      "textSecondary": "#9AA0C0",
      "textDisabled": "#5A5F78",
      "accent": "#FF6B35",
      "accentHover": "#FF7F4F",
      "accentPressed": "#D9542A",
      "accentText": "#12131A",
      "border": "#333852",
      "borderStrong": "#6C7392",
      "focus": "#FFD166",
      "error": "#FF5D73"
    }
  },

  "type": { "family": "Cascadia Mono", "baseSize": 10, "headingSize": 16, "headingWeight": 700 },

  "layout": {
    "windowWidth": 620,
    "padding": 32,
    "spacing": 20,
    "controlRadius": 14,
    "mica": false,
    "bannerHeight": 96,
    "descriptionLines": 3
  },

  "features": {
    "showName": false,
    "showConsent": true,
    "requireEmail": true
  }
}
```

`bannerBackground` matches `background` because `acme.png` has a transparent background — the strip disappears into the dialog and only the mark is visible.

`theme\strings.en-US.json`:

```json
{
  "locale": "en-US",
  "strings": {
    "title": "Acme Rocket Sled — Problem Report",
    "headline": "{appName} {appVersion} has stopped working.",
    "body": "Sending this report helps us fix the problem. It contains a snapshot of the program at the moment it stopped, and nothing else.",
    "consent": "I agree that Acme may store this report for up to one year.",
    "emailLabel": "Email address (required)",
    "send": "&Send Report"
  }
}
```

Then look at it:

```batch
BugSplatReporter.exe --preview --theme "C:\work\acme-theme"
```

### Before you ship a theme ✅

1. **Both appearances.** Even if you pin `appearance`, flip your Windows theme and look — your `bannerBackground` may be the only white thing left.
2. **Contrast on the accent.** `accentText` sits on `accent`, not on `background`. A light accent needs dark `accentText`.
3. **The focus ring.** Tab through every control. `focus` has to be visible against `background`, and the ring's inner stroke has to be visible against `accent`.
4. **Your longest language.** German runs roughly 35% longer than English. The dialog grows to fit, but look at what "grown" means for your `windowWidth`.
5. **Display scaling.** Try 150% and 200%. Everything is measured, but a logo authored at 1× will look soft at 2×.
6. **Mnemonics.** Two controls sharing an `&` letter means one of them can't be reached from the keyboard.

### When a change doesn't take effect 🔧

**A theme file can change how the crash dialog looks. It can never stop a crash report being sent.** Everything about how the files are read follows from that:

| Situation | What happens |
| --- | --- |
| The file isn't there | Built-in theme. Not an error. |
| The file isn't JSON, is truncated, or is binary | Built-in theme, and a line in `BugSplat.log`. |
| The file is larger than 256 KB | Refused without being read. Built-in theme. |
| A key you wrote isn't in this document | Silently ignored. |
| `schemaVersion` is newer than the reporter | Loaded anyway; unknown keys ignored. |
| One value has the wrong type, or is out of range | **That one key** falls back to its default. Everything else in the file still applies. |
| A whole section has the wrong type | That section is ignored; the rest of the file applies. |
| `brand.logo` names a file that's missing, huge, or not an image | The built-in logo is used. |

Every fallback is written to the crash folder's `BugSplat.log`, prefixed `theme:` or `strings:`, naming the key and the reason. **If a colour you set isn't showing up, that log line is the first place to look.** Under `--preview` the same lines go to the debugger output.

A couple of combinations are worth calling out:

* `requireEmail: true` with `showEmail: false` would be a dialog nobody could submit. The loader spots it, drops the requirement, and logs it.
* Encoding. A string file saved as ANSI will have mangled accents; one saved as UTF-16 isn't valid JSON here and falls back to English wholesale.

### Compatibility 🔒

`schemaVersion` is `1`, and the promise runs in both directions. A theme written for a newer schema renders on an older reporter, which ignores the keys it doesn't know and logs one line saying so. A theme written for schema 1 keeps working on newer reporters, because every key has a default. Within a major schema version no key will change meaning, change type, or narrow its range.

***

When you update your dialog we'd love it if you mentioned us somewhere in it. Those mentions really help us continue to grow and develop our company — but there's absolutely no requirement, and you're free to make this dialog whatever you want it to be.

Check out our [Brand](../../about/who-is-bugsplat/brand-guidelines.md) page for examples and inspiration from our users.
