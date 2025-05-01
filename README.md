# Deploy LLMs with Ansible

Deploy and serve LLMs using either Ollama or llama.cpp on a Debian-based VM.
Only whitelisted IPs will be able to request it.

## Prerequisites

- Ansible installed on your local machine
- Debian-based VM with Docker installed
- SSH access to the VM

## Configuration

Modify the inventory files under `ollama` or `llamacpp` folders, depending on which technology you want to use.

Each folder contains an `inventory.example.yml` file that you can use as a template. Copy the example file to create your own `inventory.yml`:

```bash
# For Ollama
cp ollama/inventory.example.yml ollama/inventory.yml

# For llama.cpp
cp llamacpp/inventory.example.yml llamacpp/inventory.yml
```

Then modify the `inventory.yml` file with your specific configuration.

### llama.cpp Configuration

Configure the following variables in your inventory file:

- `ansible_host`: The IP address of your VM
- `ansible_user`: The SSH username for connection
- `ansible_ssh_private_key_file`: Path to your SSH private key file
- `model_url`: URL to the GGUF model file
- `allowed_ip_for_8080`: IP addresses allowed to access the server (port 8080)

### Ollama Configuration

Configure the following variables in your inventory file:

- `ansible_host`: The IP address of your VM
- `ansible_user`: The SSH username for connection
- `ansible_ssh_private_key_file`: Path to your SSH private key file
- `model_name`: The model name as defined in Ollama library (e.g., for Qwen3-0.6B, use `qwen3:0.6b`)
- `allowed_ip_for_8080`: IP addresses allowed to access the server (port 8080)

## Deployment

### Deploy with Ollama

```bash
ansible-playbook -i ollama/inventory.yml ollama/playbook.yml
```

### Deploy with llama.cpp

```bash
ansible-playbook -i llamacpp/inventory.yml llamacpp/playbook.yml
```

## API Usage

The server will be available on port 8080. You can make requests using the OpenAI API specification.

Example request:

```bash
curl --location 'http://YOUR_VM_IP:8080/v1/chat/completions' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer no-key' \
--data '{
    "model": "your-model-name",
    "messages": [
        {
            "role": "system",
            "content": "You are a helpful assistant"
        },
        {
            "role": "user",
            "content": "Hello, how are you?"
        }
    ]
}'
```

### Model Name Specification

The `model` field in your request must match exactly with the model you deployed:

- for Ollama: use the same value as the `model_name` specified in your `inventory.yml` file
- for llama.cpp: value isn't important
