# Paresh Patches

Morphe patches for **Jain Panchang** (`com.jaindarshan.panchangtithi` v10.2) — premium unlock and ad removal.

📦 [Add this source in Morphe Manager](https://morphe.software/add-source?github=xyz-user/xyz-patches) · 📥 [Releases](https://github.com/xyz-user/xyz-patches/releases)

## Patches

| Patch | Default | What it does |
|-------|---------|--------------|
| **Premium unlock** | on | Forces `hasActiveSubscriptions` true, injects a fake purchase list via `PurchaseAndroid.Companion.fromJson`, and forces `SharedStorage.set("isPremium", true)` |
| **Remove ads** | on | Stubs full-screen/native `load`, resolves show promises with null, no-ops `BaseAdView.loadAd` / banner `requestAd`, and stubs AdMob mediation adapter entry points |

Compatibility: `com.jaindarshan.panchangtithi` `10.2` (APKS split bundle).

## 🩹 Patches list

<!-- PATCHES_START EXPANDED -->

<!-- Do not modify this section by hand. The patch list is generated when release.yml creates a new release.

     If you wish for the patches list to be collapsed, then remove the word 'EXPANDED' from the comment tag above.

     If you wish to manually keep this list updated then remove the PATCHES_START and PATCHES_END
     comment blocks entirely. -->

#### A list of your patches will automatically be shown here after your first patches release is created.

&nbsp;

<!-- PATCHES_END -->

## 🛠️ Build

Requires JDK 17+ and an Android SDK (`local.properties` → `sdk.dir`, gitignored).

```bash
./gradlew buildAndroid
# → patches/build/libs/patches-<version>.mpp
```

There is intentionally **no** `extensions/` module — these patches are pure bytecode and do not use `extendWith`.

## 📲 Apply

**Morphe Manager** (recommended): add this repo as a patch source (link above), pick the `.mpp` from a release, select Jain Panchang 10.2, patch.

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

Notes:

- Never run concurrent `patch` commands — they share temp paths and corrupt each other.
- In zsh, prefer long flags (`--patches=…`) over bundled shorts like `-pvo` (shell expansion issues).

## 📁 Project layout

```
patches/src/main/kotlin/
├── app/paresh/patches/
│   ├── shared/Constants.kt       # shared compatibility constants
│   └── jainpanchang/
│       ├── Fingerprints.kt       # target method fingerprints
│       ├── PremiumUnlockPatch.kt
│       └── RemoveAdsPatch.kt
└── util/PatchListGenerator.kt    # generates patches-list.json on release
```

## 🚀 Getting development started

1. Put your GitHub PAT in `~/.gradle/gradle.properties` as `gpr.user` / `gpr.key` (GitHub Packages).
2. Keep changes on `dev`, merge to `main` for stable releases.
3. Use semantic commits: `feat:` / `fix:` / `chore:`.

## 🧑‍💻 Dev usage

- Build: `./gradlew buildAndroid` → `patches/build/libs/patches-*.mpp`
- Generate patch list (used by release): `./gradlew :patches:generatePatchesList`
- List patches: `java -jar morphe-cli.jar list-patches --patches "$MPP" --with-packages --with-versions --with-options`
- Apply with Morphe Desktop or `morphe-cli.jar patch` (see above)

### Patch API notes (patcher 1.13.0)

- APIs live in `app.morphe.patcher.*` — `bytecodePatch`, `Fingerprint`, `InstructionExtensions`.
- The older `app.morphe.util.*` helpers (`returnEarly`, `indexOfFirstInstructionOrThrow`, `filterMethods`) do **not** exist; use `addInstructions(0, "return-void")` and manual iteration via the `instructions` extension property instead.
- Raw smali strings need `${'$'}` for a literal `$` (e.g. `PurchaseAndroid${'$'}Companion`).

```kotlin
method.addInstructions(0, "return-void")
val idx = instructions.indexOfFirst { it.opcode == Opcode.RETURN_VOID }
mutableClassDefBy("Lcom/google/ads/mediation/AbstractAdViewAdapter;")
    .methods.filter { it.name in names }
    .forEach { it.addInstructions(0, "return-void") }
```

## 📜 License

Paresh Patches are licensed under the [GNU General Public License v3.0](LICENSE) — see [NOTICE](NOTICE) for naming restrictions.
