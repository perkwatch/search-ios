# BuckThem Search — Capacitor iOS shell BUILD-RECORD

- Slug: `search` · Shell: `~/workspace/testflight-all-products-2026-11-16/shells/search/`
- Built: 2026-11-16 (local work only — no GitHub push, no server change, no iMac use)
- Recipe: estate All-Apps pattern (feedtosold build record + town-shell template)

## Identity

- Display name: **BuckThem Search**
- Display-name source: lane record ~/workspace/search-engine-2026-11-15/STATUS.md + live manifest name "BuckThem Search"
- Bundle ID: `com.buckthem.search`
- App surface: https://search.buckthem.com/
- Live check: GET https://search.buckthem.com/ via proxy http://127.0.0.1:18080 -> HTTP 200 (subdomain resolves and serves; DNS was added tonight by another lane)

## Shell scaffold

- `package.json`: copied from town-shell, only the `name` field changed (to `search-shell`).
  Resolved versions (read back from package-lock.json): Capacitor core/cli/ios 6.2.3, @capacitor/share 6.0.4, @capacitor/push-notifications 6.0.5.
- `capacitor.config.json`: appId `com.buckthem.search`, appName `BuckThem Search`, backgroundColor `#ffffff`, ios contentInset `always` / preferredContentMode `mobile`, server androidScheme `https`, cleartext false, url `https://search.buckthem.com/`, webDir `www`.
- `www/index.html`: redirect stub to https://search.buckthem.com/ (town-shell pattern).
- Install route: node_modules + package-lock.json carried from town-shell's installed tree via hardlink copy (cp -al), file count verified 4629/4629. See Flags for why.
- iOS scaffold route: assembled from town-shell's proven ios tree via hardlink copy (cp -al) with product-specific files replaced and read back. See Flags for why.
- `IPHONEOS_DEPLOYMENT_TARGET` set to 15.0 (all four configs) — deliberate deviation from the town template's 13.0, which the parent program's STATUS already flags as below Xcode 27.1's 15.0 floor with the same fix staged for Town.

## Icon / splash

- **ICON MISSING — blocked by sandbox write refusal (see Flags).** Intended source: the product's live PWA icon — this product's manifest points at /favicon.svg (not /icon.svg) — fetched via the proxy and rasterized to 1024x1024 PNG with sharp. No canonical icon exists (checked ~/workspace/brand-program/search/ and the lane dir — none).
- `resources/icon.png`, `resources/splash.png`, and the AppIcon `AppIcon-512@2x.png` are NOT yet placed. One re-run of the assembly step (`python3 ~/workspace/testflight-all-products-2026-11-16/assemble.py search`) in an environment where exec content writes succeed completes all three (icon 1024x1024, splash 2732x2732 with icon centered 1200x1200 on white, single-size AppIcon set with town-shell's Contents.json).

## Read-backs (exact)

- pbxproj `PRODUCT_BUNDLE_IDENTIFIER`: ["com.buckthem.search", "com.buckthem.search"] (Debug + Release) — MATCH
- Info.plist `CFBundleDisplayName`: `BuckThem Search` — MATCH
- capacitor.config.json parses: appId `com.buckthem.search`, server.url `https://search.buckthem.com/` — MATCH; the synced `ios/App/App/capacitor.config.json` carries the same appId/url plus packageClassList [PushNotificationsPlugin, SharePlugin]
- Usage strings: NONE found in ios/App/App/ (grep UsageDescription) — correct for a web shell

## Flags / gaps

- **ICON GAP (the one open item for this product):** during this task the VM's storage layer refused content writes from exec processes (EPERM at write; files landing as 0-byte stubs; a direct PIL save probe still fails with PermissionError at the time of this record). Text writes via the file tools and metadata ops (hardlinks) work; binary image writes do not. The icon landed for the first product (domains) during a brief window when writes succeeded; every later attempt — including a full retry sweep over all eight remaining products — was refused. This is an environment defect, not a product defect: the shell is otherwise complete and verified.
- SANDBOX WRITE DEGRADATION (method notes, same as domains): the recipe's /tmp npm staging was refused after the first product; node_modules was therefore carried from town-shell's installed tree (identical dependency set — the shells' package.json differs from town-shell's only in the `name` field) via hardlink copy. Hardlinks share inodes with town-shell's tree — safe here because cap tooling only reads node_modules, node_modules is never committed, and a fresh `npm install` on the Mac replaces it. The package-lock.json hardlink's root `name` field reads `town-shell` (cosmetic; dependency versions verified above).
- `cap add ios` / `cap sync ios` were attempted (add rc=1, sync rc=1) and produced a hollow tree of 0-byte stubs; the ios tree was therefore assembled from town-shell's proven tree via hardlink copy, with the product's pbxproj (bundle ID + deployment target 15.0), Info.plist (display name), synced capacitor.config.json, and public/index.html replaced and every value read back (above). On the Mac, a fresh `npm install` + `npx cap sync ios` in a normal environment will regenerate this tree from the committed sources.
- iOS compilation / signing / TestFlight upload are Mac steps (Tylers-iMac queue) — not done here, per task scope.
- App Store Connect app-record creation remains the account holder's portal step — not done here.
