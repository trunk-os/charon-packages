# Packaging Specification

## Example

**NOTE**: This is based on the actual [nginx-example-page package](../../packages/nginx-example-page/1.0.0.json)

```json
{
    "title": {
        "name": "nginx-example-page",
        "version": "1.0.0"
    },
    "description": "Serve the nginx example page, exposed on port 8000",
    "source": {
        "container": "nginx:latest"
    },
    "networking": {
        "expose_ports": [["?exposed_port?", "80"]]
    },
    "prompts": [
        {
            "template": "exposed_port",
            "question": "What port should we expose this server to?",
            "input_type": "string"
        }
    ]
}
```
