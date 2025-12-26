# Packaging Specification

Charon packages have a very specific structure and schema that must be honored for success. Some parameters are required; others must line up with other configuration, such as [filesystem organization](https://github.com/trunk-os/charon-packages/blob/main/docs/Filesystem%20Organization.md) and its relationship to versioning.
## Specification Error Behavior

It is important to discuss this first. The following mistakes will result in a package that cannot be launched:

- Not following the schema
- Omitting required parameters or subschemas (such as `title`)
- Invalid JSON
- Referencing template variables that do not exist
- Invalid types which may come from strings (such as using alphanumerics in a port definition or other variables that expect integers)

There is no current way to evaluate a package ahead of time. [There is a ticket for this.](https://github.com/trunk-os/control-plane/issues/51)
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
