# coj-gunslinger-vr-dev-archive

Full development history for the **Call of Juarez: Gunslinger (2013)** VR
mod — the messy in-progress record: raw recon dumps, probes, snapshots,
dead ends, transfer sessions. Nothing here is curated; the distilled truth
lives in [coj-gunslinger-vr-engine-research](https://github.com/AuthBits/coj-gunslinger-vr-engine-research)
(ENGINE-DOSSIER.md).

Structure:

- `recon/` — static & dynamic recon artifacts (binary analysis notes,
  import tables, format notes, shader-reflection dumps of *names/offsets*).

## The six repositories for CoJ Gunslinger VR

| Repository | What lives here |
| --- | --- |
| [coj-gunslinger-vr-mod](https://github.com/AuthBits/coj-gunslinger-vr-mod) | The mod itself — once code exists, it lands here. **Releases only; ask before pushing.** |
| **[coj-gunslinger-vr-dev-archive](https://github.com/AuthBits/coj-gunslinger-vr-dev-archive)** ← you are here | Full development history — snapshots, probes, dead ends, raw recon. |
| [coj-gunslinger-vr-modding-notes](https://github.com/AuthBits/coj-gunslinger-vr-modding-notes) | Readable field notes / progress ledger. |
| [coj-gunslinger-vr-staging](https://github.com/AuthBits/coj-gunslinger-vr-staging) 🔒 | **Private** — unverified WIP builds, cross-machine handoff. |
| [coj-gunslinger-vr-engine-research](https://github.com/AuthBits/coj-gunslinger-vr-engine-research) | Distilled engine reference (dossier) + reusable VR RE playbook. |
| [coj-gunslinger-vr-external-research](https://github.com/AuthBits/coj-gunslinger-vr-external-research) | Ongoing public-research leads, gathered separately from hands-on modding work. |

## Contributing & policy

See [CONTRIBUTING.md](CONTRIBUTING.md). Reminder of the hard rule: **no game
files, ever** — only files we create (see `.gitignore`).
