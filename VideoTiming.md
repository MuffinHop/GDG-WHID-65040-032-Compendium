# VRAM ACCESS 
## GDG, CPU and VRAM access
Both GDG and CPU are given turns in accessing the VRAM. 
Accessing of the VRAM for the CPU is handled in a cycle-steal mode in MZ-800 mode. In MZ-700 mode this is only allowed during HBLANK, VBLANK and two writes during display period.
VRAM access for the CPU is handled via bitplane latches. When running in 320x200 16 color mode we spend two CPU cycles to write data due to needing to write to bitplanes 2 and 4.
## CPU wait
![320x200, with 16 colors timings as an example](16color_display_timings.png)
### Write (Number 1. in diagram)
• As there is a one-byte buffer in the GDG, write to the VRAM from the CPU is carried out through the buffer. But, actual write to the VRAM is done by the GDG with write latches, this usually aligns with GDG flip-flopping VRAM access during display period. 
### Read (Number 2. in diagram)
Wait is issued along with the CPU write action both during displaying and flyback periods to perform reading operation in synchronization with the CPU cycle.
![During Flyback, VRAM Read can cause a /WAIT on the CPU](WAIT_on_Flyback.png)

| Signal Name | I/O | Functional description | Note |
| :--- | :--- | :--- | :--- |
| VRAS | O | VRAM Row Address Strobe, control signal | Active low |
| VCAS | O | VRAM Column Address Strobe, control signal | Active low |
| VOE | O | Video Output Enable | Active low |
| VOD0..7 | O | VRAM address signal (multiplexer output) | - |
| VRWR | O | VVRAM write signal | Active low |
| VA0..7 | I/O | VRAM data bus (standard RAM)| |
| VC0..7 | I/O | VRAM data bus (optional RAM)| |
| HBLN | - | Horizontal Retrace aka HBLANK | Active low |
| CPU.WR | - | CPU read or write | Active low |
| VRAM.WR | - | Actual VRAM read or write event | Active low |


# PAL Video Timing 
In total, there are 312 lines, not interlaced. 

## Horizontal timing (per line)

| Segment | Cycles | Notes |
| :--- | ---: | :--- |
| H_SYNC pulse | 80 | Sync pulse at start of line timing sequence |
| Post-sync interval | 106 | Interval before visible border starts |
| Left border | 154 | Visible border area |
| Active graphics | 640 | Main pixel area |
| Right border | 134 | Visible border area |
| Pre-next-line interval | 22 | Interval before next line sync |
| **Total line period** | **1136** | `80 + 106 + 154 + 640 + 134 + 22` |

Derived visible width (including borders):

- `154 + 640 + 134 = 928` cycles

## Vertical timing (per frame, PAL)

| Parameter | Lines | Notes |
| :--- | ---: | :--- |
| VDISP | 200 | Active display lines |
| VBLANK | 112 | Vertical blanking lines |
| **VTOTAL** | **312** | Total lines (`0..311`) |


---

# NTSC Video Timing

In total, there are 262 lines, not interlaced.

## Horizontal timing (per line)

| Segment | Cycles | Notes |
| :--- | ---: | :--- |
| Pre-sync interval | 74 | Interval before visible border starts |
| Left border | 58 | Visible border area |
| Active graphics | 640 | Main pixel area (same as PAL) |
| Right border | 54 | Visible border area |
| Pre-next-line interval | 22 | Interval before next line sync |
| **Total line period** | **912** | `74 + 58 + 640 + 54 + 22` (approx.) |

Derived visible width (including borders):

- `58 + 640 + 54 = 752` cycles

## Vertical timing (per frame, NTSC)

| Parameter | Lines | Notes |
| :--- | ---: | :--- |
| VDISP | 200 | Active display lines |
| VBLANK | 62 | Vertical blanking lines |
| **VTOTAL** | **262** | Total lines (`0..261`) |

---

## **Sources**

- [Nobomi PAL timings on MZ800](https://www.nobomi.cz/8bit/doc/mz800pal.php)
- [Nobomi NTSC timings on MZ800](https://www.nobomi.cz/8bit/doc/mz800pal.php)
