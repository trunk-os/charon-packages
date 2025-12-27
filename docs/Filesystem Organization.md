# Filesystem Organization

This document covers how the Charon repository is organized. It has a specific structure which is required by this subsystem to function properly.

Note that the filesystem structure is critical to deploying Charon packages; if the structure is not honored, undefined behavior is a possibility (at least currently). Additionally, your packages or variables may simply be ignored.
## Versioning and Semantics

Versioning is a part of the filesystem; these version numbers are used in systemd units as well as other places packages need to be distinct at a version-level. It's important to understand that a version is an intrinsic to the package identity itself: versions are a part of the actual package name, for example.
## Packages

Packages are managed in subtrees, organized by package name first, then the version is used to mark the JSON filename. This structure lives in [packages/](../packages) off the root of the repository.

Example from an early version of this repository:

```
packages/coturn-stun
packages/coturn-stun/1.0.0.json
packages/example_package
packages/example_package/0.0.1.json
packages/nginx-example-page
packages/nginx-example-page/1.0.0.json
```
## Variables

Variables are _version-independent_ parameters that are prepopulated to be leveraged by the templating system Charon packages use.

Each variable must be in its own filename, named after the package, minus the version. They live under the subtree [variables/](../variables) off the root of the repository.

If you do not require variables, you must still create the file [(with the appropriate schema)](https://github.com/trunk-os/charon-packages/blob/main/docs/Package%20Specification.md). There is a [ticket to resolve this](https://github.com/trunk-os/control-plane/issues/53) -- it should not be necessary.

Example from an early version of this repository:

```
variables/example_package.json
variables/coturn-stun.json
variables/nginx-example-page.json
```

Contrast that with the `packages` structure listed above.