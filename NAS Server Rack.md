# DIY 9-Bay NAS — Philippines

> [!summary] Build Goal
> Cheap DIY NAS with a **4U 9-bay chassis**, designed to start with 2×1TB HDDs and expand gradually.
>
> **Target initial budget: ~₱22,600–₱23,900**
>
> HDDs for the eventual storage pool are **not included** beyond the initial 2×1TB test drives.

---

## 🎯 Build Philosophy

- Keep chassis **≤ ₱5,000**
- Start with only **2×1TB HDDs**
- Expand HDDs gradually as budget allows
- Avoid buying an HBA until it is actually needed
- Use a low-power Intel N100 platform
- Leave room for up to **9× 3.5" HDDs**
- Use NVMe for the NAS OS
- Prioritize low power consumption and low initial cost
- Eventually use larger 8TB/12TB/16TB+ drives
- Consider **Unraid** or **OpenMediaVault + mergerfs + SnapRAID** for gradual expansion

---

# 💰 Initial Hardware Budget

| Component | Recommended | Target Price |
|---|---|---:|
| Chassis | 4U 9×3.5" rackmount server chassis | ₱4,990 |
| CPU + Motherboard | Intel N100 NAS board, 6× SATA | ₱9,292 |
| RAM | 16GB DDR4-3200 SO-DIMM | ~₱2,100 |
| Boot SSD | 128GB NVMe | ~₱1,700 |
| PSU | 500W ATX | ~₱1,600–₱2,850 |
| Fans | 3×120mm budget fans | ~₱525 |
| HDD #1 | 1TB 3.5" SATA | ~₱960 |
| HDD #2 | 1TB 3.5" SATA | ~₱960 |
| SATA / Misc. | SATA cables, screws, cable ties | ~₱500 |
| **TOTAL** | | **~₱22,600–₱23,900** |

> [!tip] Budget Target
> Aim for approximately **₱23,000** for the initial NAS.
>
> Shopee vouchers/free shipping may reduce this further.

---

# 🖥️ 1. Chassis

## 4U 9-Bay 3.5" Rackmount Server Chassis

**Target price:** ₱4,990

### Requirements

- 4U rackmount
- 9× 3.5" HDD support
- Standard motherboard mounting
- ATX PSU support
- Enough depth for motherboard + PSU + HDDs
- Adequate HDD airflow
- Preferably removable HDD trays

> [!warning] Important
> The previously considered **9U network/data cabinet is NOT the NAS chassis**.
>
> **9U ≠ 9-bay.**
>
> The 9U cabinet shown in the listing is a wall-mounted network cabinet measuring approximately 530 × 450 × 400 mm. It is intended for switches, patch panels, DVR/NVR equipment, etc.
>
> Do **not** use that cabinet as the NAS chassis.

### Chassis budget limit

**Maximum: ₱5,000**

Do not spend ₱6–10K+ on a chassis unless the extra money provides a significant improvement such as:

- Proper SAS/SATA hot-swap backplane
- Better drive trays
- Better airflow
- Better construction
- More drive bays

For this budget build, the extra money is better spent on HDDs.

---

# 🧠 2. CPU + Motherboard

## Intel N100 NAS Motherboard

**Target price:** ~₱9,292

### Preferred specifications

- Intel N100
- 6× SATA 3
- 2× M.2
- PCIe slot
- DDR4
- Mini-ITX
- Multiple 2.5GbE ports

### Current example

N100 NAS motherboard with:

- 6× SATA 3.0
- 2× M.2
- PCIe
- 4× 2.5GbE
- DDR4

[Current N100 6-SATA motherboard listing](https://shopee.ph/N100-i3-N305-N5105-NAS-Board-DDR4-Motherboard-4x-Intel-i226-V-2.5G-2%2A-M.2-NVMe-6%2A-SATA3.0-HDMI2.0-DP-2%2A-M.2-For-Soft-Router-Storage-Server-Mini-ITX-board-With-PCIE-17X17CM-i.599807607.25306291935)

### Why N100?

- Very low power consumption
- 4 cores / 4 threads
- Intel Quick Sync
- Hardware video transcoding
- More than enough CPU for file serving
- Suitable for Docker
- Suitable for Plex/Jellyfin
- Suitable for Syncthing
- Suitable for Home Assistant
- Low heat output

For a home NAS, a high-end Ryzen/Core i5 CPU would mostly be unnecessary unless running lots of VMs or heavy workloads.

---

# 🧮 3. RAM

## 16GB DDR4-3200 SO-DIMM

**Target price:** ~₱2,100

### Recommendation

Start with:

**16GB**

rather than 8GB.

This gives more headroom for:

- NAS services
- Docker
- Plex/Jellyfin
- File indexing
- Syncthing
- ZFS, if used
- Other applications

If the motherboard only has one SO-DIMM slot, buy a single 16GB module.

---

# 💾 4. Boot SSD

## 128GB NVMe

**Target price:** ~₱1,700

A 128GB NVMe SSD is sufficient for the NAS operating system.

### Suggested configuration

```text
M.2 #1
└── 128GB NVMe
    └── NAS OS
```

The second M.2 slot remains available.

### Why not a large SSD?

The NAS OS doesn't need 1TB or 2TB.

Save the money for HDD storage.

---

# 🔌 5. Power Supply

## 500W ATX PSU

**Target:** ~₱1,600–₱2,850

### Budget option

**MSI MAG A500N-H 500W**

~₱1,595

### Preferred option

**Seasonic S12III 500W 80+ Bronze**

~₱2,850

### Recommendation

If budget is extremely tight:

> MSI 500W

If the NAS will run 24/7 for years:

> Seasonic 500W Bronze

A 500W PSU is more than enough for:

- Intel N100
- Motherboard
- HBA
- NVMe
- 9× HDD
- Fans

The important thing is PSU quality and sufficient SATA power connectors, not enormous wattage.

---

# 🌬️ 6. Case Fans

## 3×120mm fans

**Target:** ~₱525

Budget 120mm fans are sufficient.

Suggested airflow:

```text
FRONT                         REAR

HDDs                          Exhaust
 ↓                              ↑
→ → → → → → → → → → → → → → → →
```

### Priority

**HDD cooling > CPU cooling**

The N100 generates very little heat.

Nine mechanical HDDs generate significantly more heat, especially when all drives are active.

---

# 💿 7. Initial HDDs

## 2×1TB SATA HDD

**Target:** ~₱960 each

**Total:** ~₱1,920

These drives are intended for:

- NAS installation
- Testing
- Learning
- Temporary storage
- Testing Docker/services
- Testing networking
- Testing backups

### Important

Do **not** consider these the final storage drives.

Eventually replace them with larger, more reliable drives.

---

# 🧪 Initial NAS Layout

```text
4U 9-BAY CHASSIS

Bay 1 → 1TB HDD
Bay 2 → 1TB HDD
Bay 3 → EMPTY
Bay 4 → EMPTY
Bay 5 → EMPTY
Bay 6 → EMPTY
Bay 7 → EMPTY
Bay 8 → EMPTY
Bay 9 → EMPTY
```

Motherboard:

```text
SATA 1 → 1TB HDD
SATA 2 → 1TB HDD
SATA 3 → EMPTY
SATA 4 → EMPTY
SATA 5 → EMPTY
SATA 6 → EMPTY

M.2 #1 → 128GB NVMe
M.2 #2 → EMPTY

PCIe → EMPTY
```

---

# 🚫 8. Do NOT Buy the HBA Yet

## LSI 9207-8i

Typical price:

**~₱2,200–₱2,900**

Example:

[LSI 9207-8i IT Mode + SFF-8087 cables](https://shopee.ph/LSI-9207-8i-6Gbs-SAS-HBA-P20-IT-Mode-For-ZFS-FreeNAS-unRAID-2%2A-8087-SATA-Cable-i.1252464771.28770819843)

The LSI 9207-8i is a good HBA, but it isn't necessary initially.

### Motherboard already provides:

**6× SATA**

### Initial requirement:

**2× SATA**

So:

```text
6 motherboard SATA ports

Used:
██░░░░

2 / 6
```

Eventually:

```text
6 HDDs

██████

6 / 6
```

Only when installing **HDD #7** should the HBA be purchased.

---

# 🔧 Future HBA Configuration

When all six motherboard SATA ports are occupied:

```text
N100 Motherboard
│
├── SATA 1 → HDD 1
├── SATA 2 → HDD 2
├── SATA 3 → HDD 3
├── SATA 4 → HDD 4
├── SATA 5 → HDD 5
└── SATA 6 → HDD 6

LSI 9207-8i
│
├── HDD 7
├── HDD 8
└── HDD 9
```

The LSI 9207-8i provides enough additional connectivity for the remaining bays.

### Important

Use the HBA in:

**IT Mode**

Not hardware RAID mode.

This allows the NAS OS to directly see the drives.

---

# 📈 Expansion Strategy

The entire point of this build is gradual expansion.

## Phase 1 — Initial NAS

```text
1TB
1TB
EMPTY
EMPTY
EMPTY
EMPTY
EMPTY
EMPTY
EMPTY
```

Learn and test the NAS.

---

## Phase 2 — Add larger drives

Example:

```text
1TB
1TB
8TB
8TB
EMPTY
EMPTY
EMPTY
EMPTY
EMPTY
```

---

## Phase 3

```text
1TB
1TB
8TB
8TB
8TB
8TB
EMPTY
EMPTY
EMPTY
```

At this point:

**6 SATA ports are occupied.**

---

## Phase 4 — Install HBA

Buy:

**LSI 9207-8i**

Then:

```text
Motherboard SATA
├── HDD 1
├── HDD 2
├── HDD 3
├── HDD 4
├── HDD 5
└── HDD 6

LSI HBA
├── HDD 7
├── HDD 8
└── HDD 9
```

---

# 💽 Recommended Long-Term Drive Strategy

Do not buy nine HDDs immediately.

Instead:

```text
Start
↓
2 × 1TB
↓
2 × 8TB
↓
3 × 8TB
↓
4 × 8TB
↓
6 × 8TB
↓
9 × 8TB
```

Or use whatever drive capacity has the best **₱/TB** price when you actually purchase.

Possible future capacities:

- 8TB
- 12TB
- 16TB
- 18TB
- 20TB+

There is no need to decide the final drive capacity today.

---

# 🗄️ NAS Software

## Recommended for gradual expansion

### Option 1 — Unraid

**Best fit for this particular project.**

Advantages:

- Easy to add drives
- Drives can have different capacities
- Very convenient Docker support
- Easy GUI
- Good home-server ecosystem
- Excellent for gradual expansion

Example:

```text
Start:
1TB
1TB

Later:
+8TB

Later:
+8TB

Later:
+12TB

Later:
+16TB
```

This matches the intended "buy drives when I can afford them" strategy.

---

## Option 2 — OpenMediaVault + mergerfs + SnapRAID

Best if:

**minimum software cost is important.**

Advantages:

- Free
- Flexible
- Good for mixed-size drives
- Good for gradual expansion
- SnapRAID provides parity protection

More configuration is required compared with Unraid.

---

## Option 3 — TrueNAS SCALE

Excellent NAS platform, especially if using ZFS.

However, it is **not my first recommendation for this particular project** because the goal is gradual, one-drive-at-a-time expansion.

Traditional ZFS layouts require more planning around vdevs, expansion and redundancy.

TrueNAS is excellent when the storage architecture is planned upfront.

---

# 🏆 Recommended Build

## Buy Now

| Part | Budget |
|---|---:|
| 4U 9×3.5" chassis | ₱4,990 |
| N100 6×SATA motherboard | ₱9,292 |
| 16GB DDR4 | ~₱2,100 |
| 128GB NVMe | ~₱1,700 |
| 500W PSU | ~₱1,600–₱2,850 |
| 3×120mm fans | ~₱525 |
| 2×1TB HDD | ~₱1,920 |
| Miscellaneous | ~₱500 |
| **TOTAL** | **~₱22,600–₱23,900** |

### 🎯 Budget target

# **~₱23,000**

---

# 🛒 Buy Later

| Part | When to buy | Approx. cost |
|---|---|---:|
| LSI 9207-8i IT Mode | When reaching HDD #7 | ~₱2,200–₱2,900 |
| SFF-8087 → 4× SATA cables | With HBA if not included | ~₱500–₱1,000 |
| Large HDDs | As needed | Varies |
| UPS | Before storing important data | Varies |
| 10GbE NIC/switch | If needed | Varies |

---

# 💰 Final Initial Budget

## Approximately **₱23,000**

This gets:

- 4U rackmount chassis
- 9×3.5" bay capacity
- Intel N100
- 6× SATA
- 16GB RAM
- 128GB NVMe
- 500W PSU
- 3× fans
- 2×1TB HDD
- Room for 7 additional HDDs
- PCIe slot available for future HBA

---

# ⭐ Recommended Upgrade Order

```text
1. Build NAS
       ↓
2. Install 2×1TB
       ↓
3. Install/configure NAS OS
       ↓
4. Test everything
       ↓
5. Buy first large HDD
       ↓
6. Buy additional HDDs as budget allows
       ↓
7. Reach 6 HDDs
       ↓
8. Buy LSI 9207-8i
       ↓
9. Fill bays 7–9
       ↓
10. Add UPS
       ↓
11. Upgrade networking if needed
```

---

# ⚠️ Important Checks Before Buying the Chassis

The **₱4,990 chassis is currently the least certain component**.

Before purchasing, confirm with the seller:

- [ ] Supports Mini-ITX motherboard
- [ ] Supports standard ATX PSU
- [ ] Has 9× 3.5" drive positions
- [ ] Actual internal depth is sufficient
- [ ] HDD mounting/trays are included
- [ ] Adequate front-to-back airflow
- [ ] Enough fan mounts
- [ ] Whether a SATA/SAS backplane is included
- [ ] If there is a backplane, determine whether it uses:
  - [ ] Individual SATA connectors
  - [ ] SFF-8087
  - [ ] SFF-8643
  - [ ] Other connector

> [!warning] Do not assume "9-bay" means hot-swap.
> A chassis can have nine HDD mounting positions while requiring individual SATA cables.

---

# 📝 Notes

### Current target

**~₱23K NAS without the future large-capacity HDDs.**

### Philosophy

> Spend as little as possible on the server itself and put the money into storage.

### Key design decision

> **Do not buy the HBA until HDD #7.**

The N100 motherboard's 6 SATA ports are sufficient for the first six drives.

### Long-term goal

**9-bay 4U NAS with gradual HDD expansion.**

### Preferred software

**Unraid** for convenience and gradual expansion.

**OMV + mergerfs + SnapRAID** for lowest software cost.

**TrueNAS SCALE** if ZFS and a more structured storage architecture become priorities.
