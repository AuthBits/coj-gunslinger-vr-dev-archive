# Contributing & policy

This repository is part of a non-commercial, fan **flat-to-VR game-modding**
project. The same few principles govern every repository of this project —
this file states them so they're covered everywhere. (For the reusable
tooling and the public knowledge library this project builds on, see
[flat-to-vr-RE-toolkit](https://github.com/TefMeister/flat-to-vr-RE-toolkit)
and
[flat-to-vr-cross-engine-research](https://github.com/TefMeister/flat-to-vr-cross-engine-research).)

## How we treat other people's work

1. **Study everything public — online only; nothing gets downloaded to
   study.** We read, study, and learn from anything publicly available —
   repos, docs, articles, videos, forum posts — strictly online (browser, web
   fetch, a repo's own file viewer). We never clone or download someone else's
   repo, mod, or files onto our machines just to look inside them. (This
   doesn't cover tools we actually install to run — OpenVR, MinHook, x64dbg,
   official SDKs, and the like are dependencies, not material we're studying.)
   We then build our mods and tools **ourselves, by trial and error**, and
   everything we ship is **our own work**.
2. **Zero code or files from anyone else.** We do **not** paste, transcribe,
   or lightly-reword anyone else's source code, config, or files into our
   repositories — **not even a short snippet, whole or partial, regardless of
   license or price, and regardless of whether it is free to download.** Free
   to download is not the same as ours to take. If code appears in our docs,
   it is our own or generic pseudocode. What we take from others is the
   publicly-explained *idea*; the implementation stays theirs.
3. **No original game files, ever.** We never commit or upload game
   executables, assets, or archives. Only files we create. (Interface
   metadata we generate ourselves — export dumps, `.def` files, name/offset
   lists, our own recon notes — is fine; game content is not.)
4. **Credit everyone and link the source.** Tools, open-source projects,
   community knowledge, and individuals — even inspiration. If we drew on it,
   we name it and link it.

## How anyone may use our work

**Everything *we* create — our writing, our tooling, and our mods — is free
for anyone to use, modify, and build on, as long as you credit us.** We hold
others' work to *link-and-learn, never copy*; we release our own on *take it
freely, just say where it came from*. (Our documentation is CC-BY-4.0; our
code is offered in the same spirit.)

## How to contribute

- **Research findings** (engine behavior, offsets, cvar effects, dead ends):
  best in `-dev-archive` (raw) or here in the dossier (distilled). One
  finding per commit, evidence attached.
- **Problems/ideas**: open an issue in any of the project repos.
- **Code**: pull requests welcome on `-dev-archive` / `-staging`; releases
  land in `-mod` and **we ask before pushing there**.
- **External leads** (prior art, community discussion, vendor docs):
  `-external-research`, with links to primary sources.

## Corrections & removals — no questions asked

If you are credited or referenced here — as an author, contributor, or
source — and want your name or information changed or removed, open an issue
(or use the contact noted in the repo READMEs once a GitHub org exists) and
tell us **exactly what to remove**. We will do it promptly, no problem and no
argument; you do **not** need to prove ownership or explain why. Likewise, if
you should be credited and aren't, tell us and we'll fix it.

## Legal footing

Non-commercial fan work. Using or building any of our mods requires
**owning a legitimate copy** of Call of Juarez: Gunslinger, and we
**redistribute no original assets**. The reverse-engineing techniques
involved (DLL proxying, hooking, injection, memory patching) resemble malware
only in tooling; the context is personal modding of games we own.
