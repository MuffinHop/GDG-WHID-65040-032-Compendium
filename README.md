# GDG WHID-65040-032 
This is a loose compendium of the Sharp MZ-800's Graphics Display Generator and some additional hardware information to help solving your GDG WHID-65040-032 programming challenges. 
This document was started as a resource for me to gather all the info into one place about the graphics chip since it's scattered all over the internet.
A lot of the forum and bbs discussions have already been lost so it is high time to gather information on this chips behaviour in one sure place.
There is still a lot of unknowns and mysteries related to GDG WHID-65040-032 and I am more than happy to take pull requests or have a discussion. 

*The document may shorten GDG WHID-65040-032 to just GDG*

## Brief warning when creating MZ-700 software on the MZ-800
The GDG attempts to replicate the behaviour of the MZ-700's display generator M60719, but you should be warned that it is rather different in its behaviour although there are some similarities. 
- The VRAM is slower.
- The hardware asserts the /WAIT signal when the drawable area is being generated. The /WAIT is only released during the Horizontal Blanking (HBLANK) or Vertical Blanking (VBLANK) intervals. 
- ROM from 0xE000 may be mapped together with VRAM on mode switch.
- There may be other unkwowns that aren't well documented.
