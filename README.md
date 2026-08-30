**Custom Linux kernels for DSM - with support for the hardware Synology left out.**

Synology's kernel only knows about the hardware in the box they sold you. Run
DSM on your own machine and much of it is invisible: the network card, the
sensors, the GPU. These builds add that support back.

> 🔒 **Kernel 5.10.55** - the version DSM 7.x requires. Not a choice: DSM's own
> drivers are compiled against it and won't load against anything else.

---

## ⚡ Quick check: will my hardware work?

### 🖥️ Processor

- ✅ **Intel** - any modern CPU → use `epyc7002`
- ✅ **AMD** - any modern CPU → use `epyc7002`
- ✅ **Hybrid CPUs** - P-cores and E-cores handled properly, see [below](#-hybrid-cpus-p-cores--e-cores)

> 💡 **Don't let the name fool you.** `epyc7002` is **not** AMD-only and has
> nothing to do with owning an EPYC server. It's Synology's name for their
> generic x86 image, and it's the right pick for almost every self-built
> machine - Intel or AMD, desktop or server.

### 🎮 Graphics

- ✅ **Intel** integrated, up to Meteor Lake (6th–14th gen, Core Ultra 1)
- ✅ **Intel Arc A-series** - A310, A380, A580, A750, A770
- ✅ **AMD Radeon RX 6000** - the full line, 6400 through 6900
- ✅ **AMD Radeon RX 7000** - 7600 through 7900
- ✅ **AMD integrated** - Ryzen APUs, including Ryzen AI 300
- ❌ **Intel Arc B-series**, Lunar Lake, Arrow Lake, Panther Lake
- ➖ **NVIDIA** - not in this kernel; use the **DSM Nvidia Driver Package** instead

### 🌐 Network

- ✅ **Realtek 2.5G** - RTL8125
- ✅ **Realtek 5G** - RTL8126
- ✅ **Realtek 10G** - RTL8127
- ✅ **Realtek gigabit** - RTL8168, RTL8169, RTL8152
- ✅ **Realtek USB** - RTL8152
- ✅ **Intel** - 2.5G (igc), gigabit (e1000e, igb), 10G/40G (ixgbe, i40e, ice)
- ✅ **Aquantia / Marvell 10G** - aqtion
- ✅ **Server cards** - Mellanox, Broadcom, Solarflare, QLogic

### 💾 Storage

- ✅ **SATA / AHCI** - onboard ports, plus port multipliers
- ✅ **NVMe** - including M.2 and U.2
- ✅ **LSI / Broadcom HBAs** - the 92xx/93xx/94xx family (mpt3sas), and the
  newer 95xx (mpi3mr)
- ✅ **LSI MegaRAID** - including the SAS cards (megaraid_sas)
- ✅ **Broadcom / Microsemi** - smartpqi, HP Smart Array (hpsa)
- ✅ **Adaptec** - aacraid
- ✅ **Areca, 3ware, Marvell, Intel C600** - arcmsr, 3w-9xxx/3w-sas, mvsas, isci
- ✅ **USB storage** - including UAS

> 💡 **HBAs in IT mode are the usual pick** for passing disks straight through
> to DSM. The LSI 9200/9300 series are the classic choice and work out of the
> box.

### 🔌 Other

- ✅ **Coral Edge TPU** - USB stick and PCIe/M.2 (Frigate etc.)
- ✅ **Sensors** - CPU temperature and fans, Intel and AMD
- ✅ **USB** - USB 3 (xHCI), serial adapters, audio
- 🧪 **Thunderbolt / USB4** - driver included, see [below](#-thunderbolt--usb4)
- ✅ **Filesystems** - exFAT, NTFS

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
> someone finding the time - it's structural. Buying hardware for this?
> Stop at 14th gen or Arc A-series.

### 🔴 AMD

| Generation | Examples | |
|---|---|---|
| Polaris, Vega | RX 500 / Vega | ✅ |
| RDNA - RX 5000 | 5500, 5600, 5700 | ✅ |
| RDNA 2 - RX 6000 | 6400, 6500, 6600, 6700, 6800, 6900 | ✅ |
| RDNA 3 - RX 7000 | 7600, 7700, 7800, 7900 | ✅ |
| Integrated Radeon | Ryzen APUs, incl. Ryzen AI 300 | ✅ |
| Pre-2013 cards | Southern / Sea Islands | ➖ not enabled |

### 🟢 NVIDIA

➖ **Not built into this kernel** - but that doesn't mean your card is useless.

NVIDIA cards are handled by the **DSM Nvidia Driver Package**, installed
separately in DSM rather than baked into the kernel. That package ships
NVIDIA's own proprietary driver, which can't be compiled into a kernel like
the Intel and AMD drivers are. Install it and your card is picked up there.

> 💡 So: 🔵 Intel and 🔴 AMD → handled by this kernel, nothing to install.
> 🟢 NVIDIA → install the DSM Nvidia Driver Package.

---

## ⚡ Thunderbolt / USB4

The driver is built in, and it covers both vendors: Intel controllers up to the
latest generation, and AMD USB4 through the generic host support every
compliant controller uses. Nothing to install.

🧪 **Treat it as experimental.** The driver loads and finds the controller, but
on some machines it then times out talking to it. It is not yet clear how much
of that is the hardware, the mainboard firmware, or DSM itself, so plugging a
Thunderbolt dock or enclosure in and having it work is not something to count
on.

What is worth knowing before you plan around it:

- 🔌 **Devices are not authorized automatically.** DSM has no Thunderbolt
  settings, so anything requiring approval will simply not appear.
- 💾 **Enclosures are the realistic use.** A Thunderbolt or USB4 disk enclosure
  is the case most likely to work; docks and displays are not the point here.
- ➖ **Not a supported feature.** Nobody has validated this across machines. If
  it works for you, good - it isn't something to buy hardware for yet.

---

## 🧠 Hybrid CPUs (P-cores + E-cores)

Modern CPUs often mix two kinds of core: a few fast ones and several efficient
ones. Intel calls them **P-cores and E-cores**; AMD ships fast and dense cores
(Zen 4 / Zen 4c) in the same way.

**A stock 5.10 kernel doesn't know the difference.** It treats every core as
identical and hands your heaviest job to a slow core as readily as a fast one.
On a 14th-gen Core that means single-threaded work can land on an E-core and
run noticeably slower for no reason.

✅ **These builds fix that.** The scheduler is taught each core's real speed, so
demanding work is steered onto the fast cores and background work drifts to the
efficient ones. It's automatic - nothing to configure.

### 🖥️ Which CPUs this applies to

- ✅ **Intel** - 12th gen and newer with E-cores (Alder Lake, Raptor Lake,
  Core Ultra). Verified on a Core i5-14400.
- 🧪 **AMD** - Zen 4c parts: Ryzen 3 7440U, Ryzen 5 7445U, Ryzen 5 8540U, and
  the Ryzen AI 300 series.
- ➖ **Everything else** - CPUs with only one kind of core are completely
  unaffected.

---

### Links

- <a href="https://github.com/AuxXxilium">Overview</a>
- <a href="https://xpenology.tech/wiki">FAQ & Wiki</a>
- <a href="https://github.com/AuxXxilium/arc/releases/latest">Download</a>