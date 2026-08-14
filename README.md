# SNESticleRevive — 240p / Experimental Fork

A fork of ReyFxck/SNESticleRevive focused on 240p CRTs, NES/InfoNES experiments, and my own hardware-specific quirks.

Changes that prove useful or broadly applicable will be pushed to the main version when appropriate, while **updates from the main version will always be merged back into this fork.**

**Any change made in this fork is also available to anyone** who wants to cherry-pick, pull, or otherwise incorporate it into the main version or any other fork as they see fit.

Original project and credits belong to the respective authors. See LICENSE for licensing and attribution details.

**FEATURES ADDED:**

* Fixes and improvements for 240p display mode
* "Dirty fix" for font rendering in 240p (a better fix should be implemented in the future)
* Changed SRAM and RAM initialization to fix a few FC games that rely on specific initial values
* Turbo B/A for NES games
* Exit to Browser
* Region selector (trigger region-lock screens)
* Select SRAM size (trigger the anti-piracy screens/measures)

**TO BE FIXED:**

* "Browser SRAM files" *currently not working properly, files won't show yet*
* "Reset the emulator" *currently not working properly, it resets the console*

**TO BE ADDED:**

* Similar SRAM and RAM initialization for SNES, but I don't know any games that rely on specific initial values yet
* Other stupid (or not-so-stupid) ideas I might come up with. Thanks!

