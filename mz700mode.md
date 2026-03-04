# MZ-700 compatibility mode
The MZ-700 mode is a legacy mode attempting to replicate the behaviour of the M60719 chip found in Sharp MZ-700. 
MZ-700 mode activates when `DMD = 0x08` is written to port `0xCE`.

## I/O port register mappings
Switching to MZ-700 mode will change several I/O port register mappings. MZ-700 mode remaps I/O to memory-mapped registers in the 0xCx000–0xEx000 range.

---

### **Video Generator**

| Function | MZ-800 Mode | MZ-700 Mode |  Notes |
|----------|------------|-----------|---|
| **CTC Gate** | `OUT 0xE008` | `OUT 0xE008` | I/O Port | Timing sync (PAL 50 Hz vs NTSC 60 Hz) |


---

### **8255AC (Parallel Interface) — Keyboard, Joystick, Audio, Tape Control**

| Function | MZ-800 Mode | MZ-700 Mode | Notes |
|----------|------------|-----------|---|
| **Port A (Keys Row Select)** | `OUT 0xD0` | `OUT 0xE000` |Keyboard row multiplexer output (4 bits select 1 of 4–16 rows) |
| **Port B** (Port B) | `OUT 0xD1` | `OUT 0xE001`| — | — | Usage unknown (TODO)
| **Port C (Audio/Tape Control)** | `IN/OUT 0xD2` | `IN/OUT 0xE002` |  Audio mute, CMT write, input interrupt mode, CMT sense/RD, VBLN in; VBLN is V-blank signal (50/60 Hz) |
| **Control Word Register** | `OUT 0xD3` | `OUT 0xE003` | Mode control |

---

### **CTC/8253C (Counter-Timer) — Timing and Audio Generation**

| Function | MZ-800 Mode | MZ-700 Mode | Notes |
|----------|------------|-----------|---|
| **Counter 0 (Sound)** | `OUT 0xD4` | `OUT 0xE004` | Audio tone frequency (1.1 MHz clock) |
| **Counter 1 (RGB Time Base)** | `OUT 0xD5` | `OUT 0xE005` |  Horizontal line sync frequency (15.625 kHz from 1.1 MHz / counter 0) |
| **Counter 2** | `OUT 0xD6` | `OUT 0xE006` |  Terminal counter. CT1 countdown trigger. |
| **Control Word Register** | `OUT 0xD7` | `OUT 0xE007` |  Mode control |
| **Gate Input for CT0** | Not Available | `OUT 0xE008` | Tempo  , joystick and HBLNK input |

---

### **Memory Mapping Control Ports (E0–E6)**

| Port | MZ-800 Function | MZ-700 Function | Address Range Controlled |
|------|-----------------|-----------------|---|
| **0xE0** | Output: RAM/ROM/VRAM at 0x0000–0x1FFF | Output: RAM/ROM at 0x0000–0x1FFF | Lower 8 KiB (CG ROM can swap to DRAM) |
| **0xE1** | Output: second bank (0x2000–0x3FFF) | Output: same | Second 8 KiB bank |
| **0xE2** | Output: third bank (0x4000–0x5FFF) | Output: same | Third 8 KiB bank |
| **0xE3** | Output: fourth bank (0x6000–0x7FFF) | Output: same | Fourth 8 KiB bank |
| **0xE4** | Output: VRAM at 0x8000–0xBFFF | Output: PSG VRAM at 0xC000–0xDFFF | Upper VRAM region (MZ-800) vs PSG region (MZ-700) |
| **0xE5** | Output: ROM at 0xC000–0xDFFF | Output: CG RAM at 0xC000–0xDFFF | Middle bank: ROM (MZ-800) vs CG RAM (MZ-700) |
| **0xE6** | Output: EPROM at 0xE000–0xFFFF | Output: EPROM at 0xE000–0xFFFF | Top bank (EPROM) control |

## **Sources**
- Sharp MZ-700 Personal Computer Owner's Manual
- Sharp MZ-800 Technical Reference Manual
- [SCAV GDG WHID-65040-032](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_2_WHID_65040-032.htm)
- [SCAV 8255AC Interface](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_3_8255AC.htm)
- [SCAV CTC 8253C](http://www.mz-800.scav.cz/sharp_mz-800/sharp_mz-800_5_CTC-8253C.htm)

