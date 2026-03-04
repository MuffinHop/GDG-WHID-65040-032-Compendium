# MZ-700 compatibility mode
The MZ-700 mode is a legacy mode attempting to replicate the behaviour of the M60719 chip found in Sharp MZ-700. 
MZ-700 mode activates when `DMD = 0x08` is written to port `0xCE`.

## I/O port register mappings
Switching to MZ-700 mode will change several I/O port register mappings. MZ-700 mode remaps I/O to memory-mapped registers in the 0xCx000-0xEx000 range.

---

### **Video Generator**

| Function | MZ-800 Mode | MZ-700 Mode | Notes |
| :--- | :--- | :--- | :--- |
| **CTC Gate** | `OUT 0xE008` | `OUT 0xE008` | I/O Port timing sync (PAL 50 Hz vs NTSC 60 Hz) |


---

### **8255AC (Parallel Interface) — Keyboard, Joystick, Audio, Tape Control**

| Function | MZ-800 Mode | MZ-700 Mode | Notes |
| :--- | :--- | :--- | :--- |
| **Port A (Keys Row Select)** | `OUT 0xD0` | `OUT 0xE000` | Keyboard row multiplexer output (4 bits select 1 of 4-16 rows), Joystick 1 and 2 strobe, CRT Cursor blink |
| **Port B** | `OUT 0xD1` | `OUT 0xE001` | Keyboard scan input |
| **Port C (Audio/Tape Control)** | `IN/OUT 0xD2` | `IN/OUT 0xE002` | PC Speaker, CMT write, input interrupt mode, CMT sense/RD, VBLN in; VBLN is V-blank signal (50/60 Hz) |
| **Control Word Register** | `OUT 0xD3` | `OUT 0xE003` | Mode control |

---

### **CTC/8253C (Counter-Timer) — Timing and Audio Generation**

| Function | MZ-800 Mode | MZ-700 Mode | Notes |
| :--- | :--- | :--- | :--- |
| **Counter 0 (Sound)** | `OUT 0xD4` | `OUT 0xE004` | Audio tone frequency (1.1 MHz clock) |
| **Counter 1 (RGB Time Base)** | `OUT 0xD5` | `OUT 0xE005` | Horizontal line sync frequency (15.625 kHz from 1.1 MHz / counter 0) |
| **Counter 2** | `OUT 0xD6` | `OUT 0xE006` | Terminal counter. CT1 countdown trigger. |
| **Control Word Register** | `OUT 0xD7` | `OUT 0xE007` | Mode control |
| **Gate Input for CT0** | Not Available | `OUT 0xE008` | Tempo, joystick and HBLNK input |

---

## **Memory Bank Control — MZ-700 Mode**

MZ-700 mode uses ports 0xE0-0xE6 to control memory bank switching. 

### **Output Port Bank Control**

| Output Port | Function |
| :--- | :--- |
| **0xE0** | `0x0000`-`0x7FFF` DRAM |
| **0xE1** | `0xD000`-`0xFFFF` DRAM |
| **0xE2** | `0x0000`-`0x0FFF` Monitor ROM |
| **0xE3** | `0xD000`-`0xDFFF` VRAM<br>`0xE000`-`0xE070` keytimer<br>`0xE070`-`0xFFFF` Monitor ROM |
| **0xE4** | `0x0000`-`0x1FFF` Monitor ROM<br>`0x1000`-`0xCFFF` DRAM<br>`0xD000`-`0xDFFF` VRAM<br>`0xE000`-`0xE070` keytimer<br>`0xE070`-`0xFFFF` Monitor ROM |
| **0xE5** | EXROM |
| **0xE6** | EXROM |

### **Input Port Bank State**

| Input Port | Function |
| :--- | :--- |
| **0xE0** | `0x1000`-`0x1FFF` (CG ROM)<br>`0xC000`-`0xCFFF` (VRAM/PCG RAM) |
| **0xE1** | `0x1000`-`0x1FFF` (DRAM)<br>`0xC000`-`0xCFFF` (DRAM) |


---

## How MZ-700 screen mode works

### 1) Video memory organization

MZ-700 display mode is character-based: `40 × 25` character cells.

- Character VRAM: `0xD000`-`0xD7FF`
- Color/Attribute VRAM: `0xD800`–`0xDFFF`
- Character generator window (ROM/PCG RAM mapping): `0xC000`-`0xCFFF`

Each screen cell uses one character byte and one color/attribute byte.

Color/attribute byte usage:
| Bit | Use |
| :--- | :--- |
| D7 | Used to activate the 2nd character set, PCG |
| D6 | Green Foreground |
| D5 | Red Foreground |
| D4 | Blue Foreground |
| D3 | Unused |
| D2 | Green Foreground |
| D1 | Red Foreground |
| D0 | Blue Foreground |
### 2) Display fetch operation

During active video, the display controller repeatedly performs a fixed fetch sequence for each visible area:

1. Address phase (row/column)
2. Character code read
3. Attribute/color read
4. Character pattern (CG) read
5. Shift/load to pixel shifter

Because these reads occur continuously while the beam is active, VRAM/CG memory bandwidth is shared between the video generator and the CPU.

### 3) Difference to the M60719

The original MZ-700 M60719 only allowed writes to VRAM during HBLANK and VBKLANK. But the MZ-800's GDG allows you to access VRAM during a display period. 
On GDG the wait is issued along with the CPU write action both during displaying in a flip-flop fashion(pseudo cycle steal) when accessing VRAM and during flyback periods. This is a critical reason you may have a lot of difficulty programming accurate chasing the beam effects on your MZ-800 even though 700 demos might work on your 800 just fine.

### 4) Scroll behavior note

The MZ-800 hardware scroll register can not be used in MZ-700 mode! This probably due to different multiplexing and address retriaval methods internally.


## Sources
- Sharp MZ-700 Personal Computer Owner's Manual
- Sharp MZ-800 Technical Reference Manual
- [SCAV GDG WHID-65040-032](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_2_WHID_65040-032.htm)
- [SCAV 8255AC Interface](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_3_8255AC.htm)
- [SCAV CTC 8253C](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_5_CTC-8253C.htm)
- [MZ-700 Color VRAM reference](https://www.sharpmz.org/mz-700/colorvram.htm)
- [MZ-800 PAL timing notes](https://www.nobomi.cz/8bit/doc/mz800pal.php)
- [MZ-800 NTSC timing notes](https://www.nobomi.cz/8bit/doc/mz800ntsc.php)

