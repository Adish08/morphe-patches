# Adish Patches

Custom Morphe patches for Android applications (including **Jain Panchang** `com.jaindarshan.panchangtithi` and more).

📦 [Add this source in Morphe Manager](https://morphe.software/add-source?github=adish08/morphe-patches) · 📥 [Releases](https://github.com/adish08/morphe-patches/releases)

---

## 📱 Supported Apps & Patches

### Jain Panchang (`com.jaindarshan.panchangtithi` v10.2)

| Patch | Default | What it does |
|-------|---------|--------------|
| **Premium unlock** | on | Forces `hasActiveSubscriptions` true, injects a fake purchase list via `PurchaseAndroid.Companion.fromJson`, and forces `SharedStorage.set("isPremium", true)` |
| **Remove ads** | on | Stubs full-screen/native `load`, resolves show promises with null, no-ops `BaseAdView.loadAd` / banner `requestAd`, and stubs AdMob mediation adapter entry points |

Compatibility: `com.jaindarshan.panchangtithi` `10.2` (APKS split bundle).

---

## 🩹 Patches list

<!-- PATCHES_START EXPANDED -->
> **[v1.0.0](https://github.com/Adish08/morphe-patches/releases/tag/v1.0.0)**&nbsp;&nbsp;•&nbsp;&nbsp;`main`&nbsp;&nbsp;•&nbsp;&nbsp;2 patches total
<details open>
<summary>📦 Jain Panchang&nbsp;&nbsp;•&nbsp;&nbsp;2 patches</summary>
<br>

**🎯 Supported versions:**

| 10.2 |
| :---: |

| 💊&nbsp;Patch | 📜&nbsp;Description | ⚙️&nbsp;Options |
|----------|----------------|-----------|
| [Premium unlock](#premium-unlock) | Unlocks premium features by faking active subscriptions and purchases. |  |
| [Remove ads](#remove-ads) | Disables banner, interstitial, rewarded, app open, and native ads. |  |

</details>

<!-- PATCHES_END -->

---

## 🧩 Adding Patches for Other Apps (Multi-App Support)

All your patches for multiple apps can and should live together in this single repository. Morphe bundles compile into a unified `.mpp` package that Morphe Manager and Morphe CLI automatically filter by target package name.

To add a new app:

1. **Create a package for your app:**
   Create a new directory under `patches/src/main/kotlin/app/adish/patches/<appname>/`.

2. **Define compatibility:**
   In your app folder (or in `shared/Constants.kt`), declare the app's metadata:
   ```kotlin
   val COMPATIBILITY_NEW_APP = Compatibility(
       name = "App Name",
       packageName = "com.example.app",
       targets = listOf(AppTarget(version = "1.0.0"))
   )
   ```

3. **Define your patches and fingerprints:**
   Create `Fingerprints.kt` and `<Feature>Patch.kt` using `bytecodePatch`:
   ```kotlin
   @Suppress("unused")
   val myNewPatch = bytecodePatch(
       name = "Feature name",
       description = "Description of what it does",
   ) {
       compatibleWith(COMPATIBILITY_NEW_APP)
       execute {
           // bytecode modifications
       }
   }
   ```

4. **Build and commit:**
   When you commit with `feat: Add <App Name> patches`, the release workflow will automatically compile all patches for all apps into the release `.mpp`, update `patches-list.json`, and group each app under its own section in the README.

---

## 🛠️ Build

Requires JDK 17+ and an Android SDK (`local.properties` → `sdk.dir`, gitignored).

```bash
./gradlew buildAndroid
# → patches/build/libs/patches-<version>.mpp
```

There is intentionally **no** `extensions/` module — these patches are pure bytecode and do not use `extendWith`.

## 📲 Apply

**Morphe Manager** (recommended):
Add this repo as a patch source (`https://morphe.software/add-source?github=adish08/morphe-patches`), select the target app, choose patches, and patch.

**Morphe CLI**:

```bash
java -jar morphe-cli.jar patch \
  --patches=patches-<version>.mpp \
  --keystore=Morphe.keystore \
  --keystore-password=<pass> \
  --keystore-entry-alias=<alias> \
  --keystore-entry-password=<pass> \
  --out=patched.apk \
  target.apks
```

> [!NOTE]
> - Never run concurrent `patch` commands — they share temp paths and can corrupt each other.
> - In zsh, prefer long flags (`--patches=…`) over bundled short flags like `-pvo`.

## 📁 Project layout

```
patches/src/main/kotlin/
├── app/adish/patches/
│   ├── shared/
│   │   └── Constants.kt          # shared compatibility constants
│   ├── jainpanchang/             # Jain Panchang patches
│   │   ├── Fingerprints.kt       # target method fingerprints
│   │   ├── PremiumUnlockPatch.kt
│   │   └── RemoveAdsPatch.kt
│   └── <nextapp>/                # More apps can be added here
└── util/
    └── PatchListGenerator.kt     # generates patches-list.json on release
```

## 🚀 Getting development started & Publishing

1. **Development Branch**:
   - Always make changes on the `dev` branch.
   - Pushing to `dev` creates a pre-release (`vX.Y.Z-dev.N`).
   - An automated PR from `dev` to `main` will be opened by GitHub Actions.
2. **Stable Release**:
   - When ready, merge the PR into `main` (use **Merge Commit**, do NOT squash).
   - This triggers semantic release on `main` to create a stable release (`vX.Y.Z`).
3. **Commit Messages**:
   - `feat: ...` → Minor release bump
   - `fix: ...` → Patch release bump
   - `chore: ...` → No release created
4. **GitHub Configuration**:
   - In repo **Settings > Actions > General > Workflow permissions**, enable:
     - **Read and write permissions**
     - **Allow GitHub Actions to create and approve pull requests**

## 📜 License

Adish Patches are licensed under the [GNU General Public License v3.0](LICENSE) — see [NOTICE](NOTICE) for naming restrictions.
