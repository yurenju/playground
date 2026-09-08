# theme-color probes

Four installable PWAs, built in September 2026 to answer one question: on Android, what actually
decides the colour of the system status bar in an installed PWA?

The question came from Tidemarks — in the dark theme the status bar stayed paper-coloured while
everything below it was night. The web is full of confident, contradictory answers, so these
pages settle it by measurement instead: each one declares a set of deliberately clashing colours,
and the bar names its own winner.

Install each from Chrome's menu and open it from the home screen. `display: standalone` only
behaves this way once installed — a tab tells you nothing.

## What each one asks

| Probe | Setup | Question |
| --- | --- | --- |
| `/` | manifest magenta; three meta tags (two `media`-conditioned, one plain) | Does anything in the document reach the bar? |
| `/a/` | manifest names **no** colour; one plain meta tag | Without a manifest colour, who decides? |
| `/b/` | manifest green; one plain meta tag | Does a single clean tag beat the manifest? |
| `/c/` | manifest deep blue; a mock Tidemarks shelf that switches theme | What does one fixed colour look like against both themes? |
| `/d/` | `/c/` with the manifest colour removed | What does following the system look like against both themes? |

`/a/` also has three buttons that write different brightnesses into the tag. They are there for
the second half of the answer — see below.

## What they measured, on Chrome 152 / Android

An installed PWA splits the top edge into three parts, and each is decided by something
different:

| Part | Decided by |
| --- | --- |
| The status bar's **fill** | The manifest's `theme_color`. With no `theme_color`, the system's own light/dark setting. |
| Whether the bar's **icons** are drawn dark or light | The brightness of whatever decides the fill — so the manifest, or, when the manifest is silent, `<meta name="theme-color">`. |
| The **thin band** directly below the bar | `<meta name="theme-color">`, including runtime writes. |

So the document's tag never reaches the bar's fill. Not through a runtime write, not through a
`media`-conditioned tag, not through the value present at load. `/b/` came out green with a
purple tag sitting in its head.

Two consequences worth writing down:

- A manifest is one static file, so any `theme_color` is one colour for both themes. The manifest
  member that would have ended that (`user_preferences.color_scheme_dark`) was reviewed by the
  W3C TAG in 2021 and never shipped. Other people's PWAs are stuck the same way — X's does not
  follow the theme either.
- Dropping `theme_color` hands the fill to the system and the icons to the meta tag. When those
  two disagree — a reader who picks the theme opposite their system — you get white icons on a
  white bar, and no clock.

Tidemarks took the second path knowingly: yurenju/tidemarks#209.

Desktop Chromium's title bar and iOS's status bar both follow the document, so none of this
applies to them.
