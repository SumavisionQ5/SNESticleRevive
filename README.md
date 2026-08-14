# SNESticleRevive — 240p / Experimental Fork

A fork of ReyFxck/SNESticleRevive focused on 240p CRTs, NES/InfoNES experiments, and my own hardware-specific quirks.

Changes that prove useful or broadly applicable will be pushed to the main version when appropriate, while **updates from the main version will always be merged back into this fork.**

**Any change made in this fork is also available to anyone** who wants to cherry-pick, pull, or otherwise incorporate it into the main version or any other fork as they see fit.

Original project and credits belong to the respective authors. See LICENSE for licensing and attribution details.

Keep in mind: this fork uses **AI-generated code** but it's always tested by me, and I'm a Human according to reCAPTCHA.

**FEATURES ADDED:**

* Fixes and improvements for 240p display mode
* "Dirty fix" for font rendering in 240p *(a better fix should be implemented in the future)*
* Changed SRAM and RAM initialization to fix a few FC games that rely on specific initial values
* Turbo B/A for NES games
* Region selector (to intentionally trigger region-lock screens)
* Select SRAM size (to intentionally trigger anti-piracy screens and measures)
* Browse SRAM files

**TO BE FIXED:**
* Option to reset the emulator *(currently not working, it resets the whole console instead)*

**TO BE ADDED:**

* Similar SRAM and RAM initialization for SNES, but I don't know any games that rely on specific initial values yet
* Exit to Browser
* Other stupid (or not-so-stupid) ideas I might come up with. Thanks!

