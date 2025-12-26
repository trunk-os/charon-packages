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

## Required Parameters

These parameters are **required**; they must exist and have valid data to be accepted.

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

