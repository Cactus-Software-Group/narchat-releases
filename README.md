# NarChat Desktop — releases

Installers and updater manifests for the NarChat desktop app. **Nothing else
lives here**: no source, no issues, no history. Development happens in the
private `Cactus-Software-Group/narchat` repository.

## Why this repository is public

The desktop app updates itself, and every installed copy fetches its update
manifest and installer **anonymously** — it ships no GitHub token, and it must
not, because a repo-scoped token inside a binary users hold would be read access
to the private source tree. A private repository's release assets return 404 to
an anonymous client, so the artifacts have to be readable without credentials.

That is safe because the trust anchor is not the transport. `tauri-plugin-updater`
verifies every download against the minisign public key compiled into the app
before it hands over a single byte, so a public download URL cannot be swapped
for someone else's build.

## Layout

| Tag | What it is |
| --- | --- |
| `desktop-v<version>` | **Immutable.** The installers and `latest.json` for one version. Never rewritten. |
| `desktop-stable` | Mutable pointer. `latest.json` for the version stable users receive. |
| `desktop-pilot` | Mutable pointer, pilot channel. |
| `desktop-internal` | Mutable pointer, internal channel. |

The channel tags carry updater metadata only; the installers stay in the
immutable version release they were built for.

## Releasing

Nothing here is uploaded by hand. Pushing a `desktop-v*` tag in the private
repository runs its `Desktop Release` workflow, which builds, signs and publishes
the immutable release here; `Promote Desktop Update` then moves a channel pointer
onto it.

Note that rolling a channel back does **not** roll clients back — the updater
only offers a version strictly greater than the installed one, so an older
manifest simply stops offering anything. A rollback means publishing a higher
version that contains the revert.
