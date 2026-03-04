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




- [Nobomi PAL timings on MZ800](https://www.nobomi.cz/8bit/doc/mz800pal.php)
- [Nobomi NTSC timings on MZ800](https://www.nobomi.cz/8bit/doc/mz800pal.php)
