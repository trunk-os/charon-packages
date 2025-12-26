# Charon Packages Repository

This is the package repository for Trunk, powered by the Charon subsystem. It is automatically pulled by charond as a part of its boot and presented to users in the "Packages" panel.

Charon is not focused on system packaging: similar to systems like Kubernetes or docker-compose, it is centered around runtime support: just supporting it in a manner that is as user-friendly as we can make it. It is bolstered by a powerful GRPC API (exposed through HTTP) that you can manage remotely. It currently has direct support for both qemu and podman.

## Features

Charon has a number of novel features:

- **Question and Answer capability**: Similar to `debconf`, but with a HTTP API, we are able to request information in a variety of formats (which are represented similar to types in a programming language: int, string so on). This information can then be used in templates that describe other dependencies.
- **Functionality to expose ports at the border in local networks with `UPnP`**: this allows users to open up ports at home for things like HTTP servers or video chat functionality, etc.
- **Enforced Security**: Charon tightly controls the execution environment. There is no commandline. You cannot do a lot of things you might want be able to do with a tool like docker or qemu. This ensures a consistent experience for end-users.
- **Automatic management of storage in a configurable manner**: qemu automatically gets a block device to use for backing storage; containers get a filesystem created for them that they can leverage. Additional block devices and filesystems can be also be created as a part of this process, and they are organized and manageable by the user if desired.
- **In control of its own supervision**: it writes its own systemd units, manages the execution environment by wrapping it with something capable of directly reading the package format, allowing it to orchestrate updates (both to the underlying execution environment as well as the package manifest itself) as well as supervise the underlying package runtime.

## Documentation on the Package Format

See [docs](./docs).
