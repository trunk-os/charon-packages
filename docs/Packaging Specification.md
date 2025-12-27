# Packaging Specification

Charon packages have a very specific structure and schema that must be honored for success. Some parameters are **required**; others must line up with other configuration, such as [filesystem organization](https://github.com/trunk-os/charon-packages/blob/main/docs/Filesystem%20Organization.md) and its relationship to versioning.
## Specification Error Behavior

It is important to discuss this first. The following mistakes will result in a package that cannot be launched:

- Not following the schema
- Omitting required parameters or subschemas (such as `title`)
- Invalid JSON
- Referencing template variables that do not exist
- Invalid types which may come from strings (such as using alphanumerics in a port definition or other variables that expect integers)

There is no current way to evaluate a package ahead of time. [There is a ticket for this.](https://github.com/trunk-os/control-plane/issues/51) At present, you can stage the repository and use the `charon` tool in the [control-plane](https://github.com/trunk-os/control-plane) to launch workloads. 
## Staging your own repository for development

Supporting alternative repositories is [very hard right now.](https://github.com/trunk-os/install/issues/11) You basically need to rebuild the whole operating system. Solutions are forthcoming, but unless you are an expert, it is strongly recommended that you wait until these features arrive.
## Behavior Verbiage

Several parts of this document use verbiage which describes how a portion of the specification should be regarded. This section attempts to clarify the meaning of those terms.

All of these terms in this document should be represented in **bold text** for recognition.

- **must** and **must not** describe situations where a package creator is expected to handle these situations a certain way, without exceptions.
- **required** and **not required** is for behavior, similar to **must** and **must not**, but describes optional capabilities instead of hard requirements.
	- Even though something is **not required**, leveraging it does not mean that you are not subject to other **must** requirements.
	- Code behavior that does not match a **must**/**must not** is a bug, and [should be reported.](https://github.com/trunk-os/community/issues)
- **should** and **should not** describe situations where a package creator should follow instructions, but is not required to.
	- It is not the intention of this specification to use this much; for clarity and simplicity's sake, we prefer solutions that fit **must** and **must not** in these scenarios.
## Variables

**NOTE:** These are **not** a part of the package schema, just placed here currently for simplicity's sake. Please "Variables" in the [Filesystem Organization document.](https://github.com/trunk-os/charon-packages/blob/main/docs/Filesystem%20Organization.md)

The variables document is currently **required**, even if you do not use templating. Please see [this issue](https://github.com/trunk-os/control-plane/issues/53) for potential remedies to this hassle.

### Format

All listed fields are currently **required**.

- `name`: must be same as the filename, which corresponds to the package name.
- `variables`: an object (which may be empty) containing key/pairs of template variable to default value. Values are always string; see the other notes on type management in this document.
### Example

```json
{
  "name": "coturn-stun",
  "variables": {
    "default_realm": "example.com",
    "listening_port": "3478",
    "tls_listening_port": "5349",
    "alt_listening_port": "3479",
    "alt_tls_listening_port": "5350",
    "min_port": "49152",
    "max_port": "65535"
  }
}
```
## Real World Example

Since it's important to understand what packages look like before discussing the details, here is an example of a real package.

**NOTE**: This is based on the actual [coturn-stun package](../../packages/coturn-stun/1.0.0.json).

```json
{
  "title": {
    "name": "coturn-stun",
    "version": "1.0.0"
  },
  "description": "Coturn STUN/TURN server for WebRTC and real-time communications",
  "source": {
    "container": "docker://coturn/coturn:latest"
  },
  "networking": {
    "forward_ports": [
      ["3478", "3478"],
      ["3479", "3479"],
      ["5349", "5349"],
      ["5350", "5350"]
    ],
    "expose_ports": [
      ["49152", "49152"],
      ["65535", "65535"]
    ],
    "hostname": "?stun_hostname?"
  },
  "storage": {
    "volumes": [
      {
        "name": "config",
        "mountpoint": "/var/lib/coturn",
        "size": "1073741824",
        "recreate": "false",
        "private": "true"
      },
      {
        "name": "logs",
        "mountpoint": "/var/log/coturn",
        "size": "2147483648",
        "recreate": "false",
        "private": "true"
      }
    ]
  },
  "system": {
    "host_pid": "false",
    "host_net": "false",
    "privileged": "false",
    "capabilities": ["NET_BIND_SERVICE"]
  },
  "resources": {
    "cpus": "2",
    "memory": "1024"
  },
  "prompts": [
    {
      "template": "stun_hostname",
      "question": "What hostname should the STUN server use?",
      "input_type": "string"
    }
  ]
}
```

## Types

Types are a bit different here. Since we can template any type, types of values in the manifest are always string. During processing, they are "compiled", which means the template variables are interpolated and so on, then the types are converted to native types.

What this means for you: you are allowed to use template variables where things like integers or booleans are expected, but be advise any attempt to convert an invalid value at compilation time will result in an error. This is to allow the question/answer system to work effectively in the face of erroneous user input.
## Top-Level Parameters

These parameters all start at the top-level of the object; more on their innards are below.
### title

`title` is the definition of the package which echoes the package name and version. It **must** match its package information in the [filesystem organization.](https://github.com/trunk-os/charon-packages/blob/main/docs/Filesystem%20Organization.md)

This field is redundant, and is planned to be [removed eventually.](https://github.com/trunk-os/control-plane/issues/52)

`title` contains two fields which **must not** be empty:

- `name`: the name of the package, such as `coturn-stun` from the example.
- `version`: the version of the package. There is no required format for this field, other than it must match the file `<version>.json` in the [filesystem organization.](https://github.com/trunk-os/charon-packages/blob/main/docs/Filesystem%20Organization.md)
### description

`description` is a friendly description of the project, and appears in the `Projects` list in the Trunk UI as well as a part of the listing API's values for the package.

It is **required** to be in the document, but **should** be filled in. This allowance is to simplify private repositories.
### dependencies

**NOTE:** dependencies are currently **not supported**, they are just defined in the format. [This ticket tracks this feature.](https://github.com/trunk-os/control-plane/issues/22)

This field is **not required**.

`dependencies` contains an array of `title` objects (without the "title" subkey). 

These package definitions **must** exist in the package repository already, along with any variables.
#### Example:

```json
{
	"dependencies": [
		{"name": "nginx-example-page", "version": "1.0.0"},
		{"name": "other-package", "version": "1.0.0"}
	]
}
```

### source

Source is **required** and **must** resolve to a working image, dependent on the type of execution environment.

Only one parameter can be supplied, and it must be one of the following keys:

- `container`: this key refers to a URL to a container registry, or when shortened, by default refers to [Docker's Registry.](https://hub.docker.com)
	- Examples:
		- `nginx:latest` - latest dockerhub nginx image
		- `https://quay.io/trunk-os/charon:stable` - stable Charon image on quay.io
- `qemu`: This refers to a QEmu image. The image must be directly raw-writeable to a block device at this time; compression is not yet supported.

#### Example:

```json
{
	"source": {
		"container": "nginx:latest"
	}
}
```

### networking

Networking is **not required**, but has a very strict schema. Particularly, around the port numbers there may be some complication around type management, especially if you are using templates.

- `forward_ports`: this is an array of pairs of integers from 0-65535. The port on the left is exposed on the router through uPnP. The port on the right is the port mapped to in the container or VM.
- `expose_ports`: same as _forward_ports_, but only exposes ports inside the local network.
- `internal_network`: builds an internal network for services to cooperate on without exposing that traffic to other network consumers. String name.
- `hostname`: sets the hostname of the container or VM. String name.

### Example:

```json
{
  "title": {
    "name": "plex-qemu",
    "version": "0.0.1"
  },
  "description": "plex under qemu",
  "source": {
    "url": "https://example.com/mirror/ubuntu.img"
  },
  "networking": {
    "forward_ports": [["1234", "5678"]],
    "expose_ports": [["2345", "6789"]]
  },
  "resources": {
    "cpus": "8",
    "memory": "4096"
  }
}
```

This forwards port `5678` to port `1234` on the router, and `2345` to `6789` on the internal LAN.

## storage

Storage is **not required**. It contains a single key called `volumes` which describes volumes to be managed; it places these under a zfs dataset created for the package.

### volume schema

Currently, only datasets are created and mapped.

- `name`: **required**. name of volume. It **must not** start with a `/`, must not contain invalid path components, and must be nameable as a zfs filesystem.
- `size`: **required**. size in bytes. Values like '50G' may also work. Actual block device size for volumes, quote for dataset.
- `mountpoint`: **not required**. mountpoint inside the container; not used for volumes, only datasets.
- `type`: **not required**. **Does not exist yet**. The type of device to create; either `volume` or `dataset`.
- `recreate`: recreate this volume every time the package is managed (reinstall, upgrade, etc). boolean.
- `private`: mounts the volume differently so that others cannot co-opt the mountpoint (either accidentally or intentionally). Boolean.

### Example

```json
{
  "storage": {
    "volumes": [
      {
        "name": "config",
        "mountpoint": "/var/lib/coturn",
        "size": "1073741824",
        "recreate": "false",
        "private": "true"
      },
      {
        "name": "logs",
        "mountpoint": "/var/log/coturn",
        "size": "2147483648",
        "recreate": "false",
        "private": "true"
      }
    ]
  },
}
```