# Fonts and readability

Use **Settings → Typography** for local fonts, role sizes, spacing, and weight. Right-click, Shift+F10, or the toolbar's **Quick settings** opens the compact text-scale control. These settings preserve account, model, key, and route configuration.

## Quick text scale

The scale dialog has minus/plus buttons and a **60–125%** slider with 1% precision. Buttons change 5% per click. The dialog stays open while editing. **Automatic** follows system/browser scaling at 100% while remembering the last manual percentage; **100%** resets the scale.

Updates apply immediately and disk writes are coalesced. If saving fails, the UI restores the persisted value and reports the error. Slider arrows/Home/End are supported; Escape returns to the menu. Finish an active whole-app typography preview before changing quick scale. **Refresh** reloads through the splash with saved preferences.

## Choose and preview fonts

1. Choose the interface font and code/log font separately. These use fonts already installed on the computer, with local fallbacks when unavailable; selecting a macOS font on Windows does not install it.
2. Choose Manual scale or Automatic. Role sizes describe their values at 100%; effective sizes reflect the selected scale.
3. Inspect the sample, which follows the draft while the rest of the app keeps saved settings.
4. Use **Preview entire app** to navigate with the temporary draft. The preview bar offers return to Settings, cancel, and save.
5. **Apply fonts now** saves typography without committing unrelated General drafts. **Save settings** saves the edited Settings sections.

Canceling restores saved values. Closing Settings with Cancel/Escape also cancels an active preview. Unsaved previews are not persisted; reload restores saved settings. Saved typography loads before the first application frame.

Interface choices include System/Automatic, Segoe UI, San Francisco, Helvetica Neue, Roboto, Noto Sans, Ubuntu, Cantarell, Arial, Calibri, Tahoma, and Verdana. Code choices include System/Automatic, SF Mono, Menlo, Monaco, DejaVu Sans Mono, and Liberation Mono alongside the existing code fonts.

## Independent size roles

| Role | Default | Range at 100% |
| --- | ---: | ---: |
| Body | 16 px | 8–20 px |
| Buttons and inputs | 15 px | 8–18 px |
| Navigation | 15 px | 8–18 px |
| Supporting text/status | 14 px | 8–18 px |
| Metadata/time | 13 px | 8–16 px |
| Component title | 18 px | 8–24 px |
| Section title | 24 px | 8–30 px |
| Page title | 36 px maximum | 8–40 px maximum |
| Code/URLs/logs | 14 px | 8–18 px |

Each role can be changed in 1px steps without altering the others. Page titles respond to viewport width. The brand lockup retains its identity sizing while following browser/OS text enlargement.

Body line spacing ranges from 1.5 to 1.9. Title/button weights are 500, 600, or 700, subject to installed font support. Inputs/buttons retain 44px minimum targets and grow for wrapped labels. Lower text scales reduce readability independently of click targets; 16px at 60% is 9.6px before OS/browser enlargement.

**Reset typography** prepares a default draft; apply/save to persist it. Enlarged text may need extra vertical scrolling. The interface does not shrink everything with CSS zoom or transforms.
