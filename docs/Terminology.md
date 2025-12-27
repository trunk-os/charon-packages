This covers some basic terminology you might run into within these documents:

- **Package Title**: This is the sum of the `name` and `version`, such as `nginx-1.0.0`. Package titles are technically independent packages, and while packages can be upgraded or downgraded, individual packages can be hosted simultaneously with others that have different versions but the same name.
- **Variable**: Special syntax that can be used in package manifest to refer to prompts or pre-programmed customizable input.
- **Charon**: The name of the packaging system used to power Trunk.
- **Source**: The location of a VM or container image.
- **Prompt**: A **Question** and **Answer** process where users can be queries for input. This input is then stored and can be used as a **Variable**.
- **Dataset**: A ZFS term for a local filesystem that shares co-morbidity with the overall operating system filesystem.
- **Volume**: A block device (emulation of a raw disk). It has a specific size and unless told to, is not a part of the existing operating system filesystem. It is typically used for VM deployments or a need for special filesystems that ZFS cannot support.