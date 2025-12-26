This is the documentation for the Charon Package Format.

# This is an Obsidian Repository

This directory can be used as an [obsidian vault](https://obsidian.md)for easier browsing and editing. Just open it as a vault. Note, I use vim mode so use the reader.

Features:

- Runtime Integration: You mostly describe how your package _runs_. How it manages storage, how it talks to the network, where it comes from.
- Uses container images or qemu.
    - Qemu still needs a lot of work
    - LXC is coming as a lightweight alternative for situations where Qemu is too heavy and container images are too simple.