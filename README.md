# SNESticleRevive — 240p / Experimental Fork

A fork of ReyFxck/SNESticleRevive focused on 240p CRTs, NES/InfoNES experiments, and my own hardware-specific quirks.

Changes that prove useful or broadly applicable will be pushed to the main version when appropriate, while **updates from the main version will always be merged back into this fork.**

**Any change made in this fork is also available to anyone** who wants to cherry-pick, pull, or otherwise incorporate it into the main version or any other fork as they see fit.

Original project and credits belong to the respective authors. See LICENSE for licensing and attribution details. **HUGE thanks** to @ReyFxck for his effort put into bringing this emulator back to life!

Keep in mind: this fork uses **AI-generated code** but it's always tested by me, and I'm a Human according to reCAPTCHA.

---

**FEATURES ADDED:**

* Fixes and improvements for the 240p display modes
* "Dirty fix" for font rendering in 240p *(a better fix should be implemented in the future)*
* Changed SRAM and RAM initialization for both NES and SNES. This will fix all the very few games that rely on specific initial values to work properly.
* Turbo B/A for NES games
* Region selector (to intentionally trigger region-lock screens) for SNES games
* Select SRAM size (to intentionally trigger anti-piracy screens and measures) for SNES games
* Browse SRAM files
* In-game soft reset (L2+R2+SELECT)
* Famiclone audio option (swap duty cycles)


**TO BE FIXED:**

* Scrolling and graphical bugs in many NES games, specially Super Mario Bros. 3 and Gauntlet. *(I'm trying to fix the mappers by comparing their codes to codes from other emulators)*
* Option to reload the .elf *(currently goes to the Memory Card browser)*

**TO BE ADDED:**

* Individual and improved screen positioning and overscan options for each system and graphical resolution/modes.
* Other stupid (or not-so-stupid) ideas I might come up with. Thanks!

