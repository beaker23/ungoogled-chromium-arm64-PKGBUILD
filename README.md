# ungoogled-chromium-arm64
PKGBUILD and associated files to compile Ungoogled Chromium on Arch Linux ARM 64-bit

Package compiles using an Odroid-N2 with 4GB of RAM and an 8GiB SWAP partition, using a 32GB eMMC module.

Failure to create a SWAP partition of a sufficient size will cause compilation to fail if compiling from an Odroid-N2.

Build times are quite excessive, expect >= 24 hours if compiling on an aarch64 SBC depending upon the hardware used.

All releases provided have been compiled using Arch Linux ARM on an Odroid-N2.

Releases are not provided with any frequency except when I feel like doing so, feel free to modify this work and create your own updates.

Permission granted to do it yourself!
