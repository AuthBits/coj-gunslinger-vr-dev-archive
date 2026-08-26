# 2026-08-25 — Static recon of CoJGunslinger.exe & data tree

Raw findings from the first (static) pass. Distilled version:
`-vr-engine-research/ENGINE-DOSSIER.md`. Game files inspected, **not**
committed (see `.gitignore`).

## 1. File inventory (whole tree, 412 files / ~4.7G)

| Count | Type | Notes |
|---|---|---|
| 117 | `.xwb` | XACT wave banks (audio) |
| 72 | `.rpack` | Techland container, magic `RP5L` |
| 18 | `.ir` | reverb impulse responses (`tunnel_3s_1chn.ir` …) |
| 16 | `.wmv` | DirectShow cinematics |
| 12 | `.pak` | plain ZIPs — locale voice/UI (`data/covoices.bin` etc.); `SpeechEn.pak` is an empty-end zip (22B) |
| 1 | `.vdf` | `installscript.vdf` (registry install: `SOFTWARE\Techland\CoJGunslinger`) |
| 1 | `.ini` | `Data/game.ini` — engine config template |

Big rpacks (level content): `borderland_PC.rpack` 863M,
`wilderness_PC.rpack` 862M, `train_PC.rpack` 606M; `common_anims_PC.rpack`
26M; `engine_PC.rpack` 3.9M (engine shaders/HUD). Per-chapter loaders:
`wild_<chapter>_load_pc.rpack` + `.xwb` pairs.

## 2. `CoJGunslinger.exe`

- `file`: `PE32 executable for MS Windows 5.01 (GUI), Intel i386, 8 sections` — **32-bit**.
- ~23 MB. Runtime: MSVCR100.

### Import table (non-Kernel/NTDLL)

| DLL | Relevance |
|---|---|
| `d3dx9_43.dll` | D3DX9 — effects/texture helpers → D3D9 effect (HLSL `.fx`) usage |
| `d3d9.dll` (resolved at runtime; also `d3d9d.dll` string for debug) | **renderer = Direct3D 9** |
| `XINPUT1_3.dll` | XInput gamepad |
| `WINMM.dll` | **planned proxy-DLL injection vector** (named exports only) |
| `steam_api.dll` | Steamworks (lobbies, rich presence, leaderboards, DLC) |
| `X3DAudio1_7.dll`, `XAPOFX1_5.dll` | XACT/XAudio2 middleware |
| `ogg_vs2010_x86_rwdi.dll`, `vorbisfile_vs2010_x86_rwdi.dll` | ogg/vorbis codecs |
| `AVIFIL32.dll`, `MSVFW32.dll` | AVI/video filters (cinematics path) |
| `dbghelp.dll` | crash dumps |
| `MSVCR100.dll`, `ole32`, `OLEAUT32`, `USER32`, `GDI32`, `SHELL32`, `SHLWAPI`, `WS2_32`, `ADVAPI32` | standard |

DirectInput is created via COM (`IID_IDirectInputDevice8` kb/mouse/joystick
strings) — not a direct `dinput8` import in the bound table.

### Renderer evidence (strings)

```
RD3D9: Device created (width: %d, height: %d, format: %d, refresh_rate: %d, vsync: %d)
D3D9DeviceReset: %dx%d, %s%s
RD3D9: RSetViewport error: viewport exceeds current render target size!
```

→ device is named `RD3D9` (R + D3D9 wrapper class).

### NVIDIA 3D Vision / stereo evidence (the big find)

```
.?AVStereoParametersD3D9@nv@@          (nv::StereoParametersD3D9, mangled)
Creating NVidia Stereo 3D texture
Destroing NVidia Stereo 3D texture     (sic — typo in original)
FAILED to create stereo 3D texture
FAILED to get nv stereo param: convergence
FAILED to get nv stereo param: eye separation
FAILED to get nv stereo param: separation
NV stereo active / NV stereo inactive / NV stereo is disabled / NV stereo is %s
NV stereo: EyeSeparation %f, Separation %f, Convergence %f
stereomap / StereoTexture / stereo_width / stereo_delay / STEREO_PARAM
DDERR_NOSTEREOHARDWARE
```

→ in-engine per-eye/stereo plumbing exists. Also:
`CAdvancedXAPOBase<CStereoEnhancerAPO>` (XAudio2 stereo APO) — audio
stereo enhancement exists too.

### Console / cvar / cheat evidence

```
.?AVCEngineConsole@@        .?AVCGameScriptConsole@@
.?AVCVariable@@             .?AVCVarFloat@@
.?AV?$CVarT@H$00@@  .?AV?$CVarT@Vvec3@@$03@@  .?AV?$CVarT@Vvec4@@$04@@
.?AVCVarSet@@            (?AVCThreadContextData@USFrameData@CVarSet@@$00$01$00$0A@@
.?AV?$CRTTIGameScript@VMenuCheat@@@@
.?AV?$CRTTIGameScript@VMenuInvokeCheat@@@@
.?AV?$CRTTIGameScript@VMenuInvokeQuickCheat@@@@
.?AV?$CRTTIGameScript@VMenuProgressCheat@@@@
.?AV?$CRTTIGameScript@VMenuCollectableCheat@@@@
.?AV?$CRTTIGameScript@VMenuVariablesCheat@@@@
.?AV?$CRTTIGameScript@VHudFloatingConsole@@@@
.?AV?$CRTTIGameScript@VQuestsConsole@@@@
AimFov
```

### Camera / FOV track evidence (rail-camera model)

```
.?AV?$CMKCameraFOVTrack@@
.?AVCKeyCameraFOV@@
.?AV?$CMKBezierTrack@VCKeyCameraFOV@@@@
.?AVCKeyCameraFOVTrack@@  (via CKeyCameraFOV usage)
.?AVCMKFXParticleTrack::SetFrame / CMKFXSeqTrack / CMKTrack family
```

"MK" track system = keyframed movie/cutscene-style tracks; camera FOV is a
tracked keyframed property → **the engine drives the camera on rails**.

### Input evidence

```
CInput::Initialize(): DirectInput object creation failed (error code 0x%x)
CInput::Initialize] failed to query for IID_IDirectInputDevice8 keyboard interface
CInput::Initialize] failed to query for IID_IDirectInputDevice8 mouse interface
CInput::Initialize] force feedback joysticks enumeration failed
CJoystick, InputConverterJoyAxis, InputConverterJoyButton
```

## 3. `rpack` container (`RP5L`)

First 64 bytes of `Data/wild_load_pc.rpack` (400KB file):

```
00000000: 5250 354c 2400 0000 0100 0000 1000 0000  RP5L$...........
00000010: 0500 0000 0600 0000 6500 0000 0600 0000  ........e.......
00000020: 0008 0000 2000 0002 0008 0000 a000 0000  .... ...........
00000030: 2300 0000 0200 0000 2100 0003 0010 0000  #.......!.......
```

Header = `RP5L` + u32 `0x24` + a run of u32 fields (counts/offsets, little-
endian). Interior strings show per-GPU shader variant tags:
`DX9_NVIDIA_LOW`, `DX9_NVIDIA_HIGH`, `DX9_AMD_FEATURE...` → compiled shader
blobs are stored per GPU family/tier. No off-the-shelf unpacker known.
Container RE = side quest (dossier risk #4).

## 4. `Data/game.ini` (engine config template)

Template with `!`-prefixed (disabled) engine options: `GameDLL(s)`
("Name of a game dll without and build postfix"), `GameClassName("GameDI")`,
`CrashCanShowMessageBox`, `GPUMemPrealloc`, `VirtualMemPrealloc` (PS3/XBOX
mem prealloc), `LoadAllAnimFilesOnRequest`, `LoadStreamAnimFilesOnRequest`,
debug options (`FastInitializePDB`, `FreeResourceLoading`).
→ cross-console engine (PC/X360/PS3) — consistent with Chrome Engine 5.

## 5. Install / locale layout

`installscript.vdf`: writes `SOFTWARE\Techland\CoJGunslinger`
(`writedir` = `Techland\CoJGunslinger`), per-language dwords (1=EN … 7=ES,
+ IT/PL/RU/JA/DE/BR…). Locale content in `DataXX.pak` zips
(`data/covoices.bin` etc.).

## 6. TODO for next recon pass (Phase 1, live)

- [ ] exe version resource (FileVersion/ProductVersion) — record exact build
- [ ] module base + ASLR behavior under x64dbg
- [ ] which thread calls D3D9 Present; render thread name
- [ ] `SetTransform` call sites & frequency (view vs world)
- [ ] does NV-stereo activate under a normal run; how to force it
  (registry key? cvar?) — `SOFTWARE\Techland\CoJGunslinger` neighbors
- [ ] console open key combo (probe: backtick, ~, F1…; or cvar file)
- [ ] chapter select / save load for deterministic launch
- [ ] d3d9 device creation args (adapter, swapchain count — is it already
  2-swapchain capable for stereo?)
