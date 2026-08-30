**Custom Linux kernels for DSM — with support for the hardware Synology left out.**

Synology's kernel only knows about the hardware in the box they sold you. Run
DSM on your own machine and much of it is invisible: the network card, the
sensors, the GPU. These builds add that support back.

> 🔒 **Kernel 5.10.55** — the version DSM 7.x requires. Not a choice: DSM's own
> drivers are compiled against it and won't load against anything else.

---

## ⚡ Quick check: will my hardware work?

### 🖥️ Processor

- ✅ **Intel** — any modern CPU → use `epyc7002`
- ✅ **AMD** — any modern CPU → use `epyc7002`

> 💡 **Don't let the name fool you.** `epyc7002` is **not** AMD-only and has
> nothing to do with owning an EPYC server. It's Synology's name for their
> generic x86 image, and it's the right pick for almost every self-built
> machine — Intel or AMD, desktop or server.

### 🎮 Graphics

- ✅ **Intel** integrated, up to Meteor Lake (6th–14th gen, Core Ultra 1)
- ✅ **Intel Arc A-series** — A310, A380, A580, A750, A770
- ✅ **AMD Radeon RX 6000** — the full line, 6400 through 6900
- ✅ **AMD Radeon RX 7000** — 7600 through 7900
- ✅ **AMD integrated** — Ryzen APUs, including Ryzen AI 300
- ❌ **Intel Arc B-series**, Lunar Lake, Arrow Lake, Panther Lake
- ➖ **NVIDIA** — not in this kernel; use the **DSM Nvidia Driver Package** instead

### 🌐 Network

- ✅ **Realtek 2.5G** — RTL8125
- ✅ **Realtek 5G** — RTL8126
- ✅ **Realtek 10G** — RTL8127
- ✅ **Realtek gigabit** — RTL8168, RTL8169, RTL8152
- ✅ **Realtek USB** — RTL8152
- ✅ **Intel** — 2.5G (igc), gigabit (e1000e, igb), 10G/40G (ixgbe, i40e, ice)
- ✅ **Aquantia / Marvell 10G** — aqtion
- ✅ **Server cards** — Mellanox, Broadcom, Solarflare, QLogic

### 🔌 Other

- ✅ **Coral Edge TPU** — USB stick and PCIe/M.2 (Frigate etc.)
- ✅ **Sensors** — CPU temperature and fans, Intel and AMD
- ✅ **USB** — USB 3 (xHCI), serial adapters, audio
- ✅ **Filesystems** — exFAT, NTFS

---

## 🎮 Graphics

### ✨ What you get

**Hardware transcoding.** Plex, Jellyfin, Emby and Video Station hand video
conversion to the GPU instead of grinding the CPU. That's the reason to care.

### 🔵 Intel

| Generation | Examples | |
|---|---|---|
| Skylake → Rocket Lake | 6th–11th gen Core | ✅ |
| Alder Lake / Raptor Lake | 12th–14th gen Core | ✅ |
| Meteor Lake | Core Ultra series 1 | ✅ |
| Arc A-series | A310, A380, A580, A750, A770 | ✅ |
| Lunar Lake, Arrow Lake, Panther Lake | Core Ultra series 2+ | ❌ |
| Arc B-series | B570, B580 | ❌ |

> 🛑 **Meteor Lake is a hard ceiling.** Everything newer needs a different
> driver (`xe`) that cannot work on a 5.10 kernel. This isn't waiting on
> someone finding the time — it's structural. Buying hardware for this?
> Stop at 14th gen or Arc A-series.

### 🔴 AMD

| Generation | Examples | |
|---|---|---|
| Polaris, Vega | RX 500 / Vega | ✅ |
| RDNA — RX 5000 | 5500, 5600, 5700 | ✅ |
| RDNA 2 — RX 6000 | 6400, 6500, 6600, 6700, 6800, 6900 | ✅ |
| RDNA 3 — RX 7000 | 7600, 7700, 7800, 7900 | ✅ |
| Integrated Radeon | Ryzen APUs, incl. Ryzen AI 300 | ✅ |
| Pre-2013 cards | Southern / Sea Islands | ➖ not enabled |

### 🟢 NVIDIA

➖ **Not built into this kernel** — but that doesn't mean your card is useless.

NVIDIA cards are handled by the **DSM Nvidia Driver Package**, installed
separately in DSM rather than baked into the kernel. That package ships
NVIDIA's own proprietary driver, which can't be compiled into a kernel like
the Intel and AMD drivers are. Install it and your card is picked up there.

> 💡 So: 🔵 Intel and 🔴 AMD → handled by this kernel, nothing to install.
> 🟢 NVIDIA → install the DSM Nvidia Driver Package.

### Links

- <a href="https://github.com/AuxXxilium">Overview</a>
- <a href="https://xpenology.tech/wiki">FAQ & Wiki</a>
- <a href="https://github.com/AuxXxilium/arc/releases/latest">Download</a>