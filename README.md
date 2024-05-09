# Install/Deploy/Manage Perplexica using Ansible

Perplexica is an AI-powered search engine. It is an Open Source alternative to Perplexity AI, running locally using Open Source tools like Ollama. For more information, visit https://github.com/ItzCrazyKns/Perplexica

# A Quick how to get started

To install/update Perplexica, run:
```bash
ansible-playbook install-perplexica.yaml --diff
```

To test ollama connection, you can try with --tags perplexica::ollama-connection, like:

```bash
ansible-playbook install-perplexica.yaml --diff --tags perplexica::ollama-connection 
```

To specify your own/specific ollama url, you can try something like:
```bash
# Test with:
ansible-playbook install-perplexica.yaml --diff -e ollama_base_url=ollama.example.com --tags perplexica::ollama-connection

# Run/Use with:
ansible-playbook install-perplexica.yaml --diff -e ollama_base_url=ollama.example.com
```

To try a specific perplexica version, you can try something like:
```bash
ansible-playbook install-perplexica.yaml --diff -e perplexica_version=v1.3.4
```

If you want to run it with https, you can try something like:
```bash
ansible-playbook install-perplexica.yaml --diff -e next_public_url=perplexica-backend.example.com -e use_https=true
```

### Tags

The playbook uses the following tags:

  * perplexica::ollama-connection: Test the Ollama Connection
  * perplexica::update-config: Updates the config.toml file.
  * perplexica::rebuild-searxng: Rebuilds & Redeploys the perplexica-searxng image.
  * perplexica::rebuild-backend: Rebuilds & Redeploys the perplexica-backend image.
  * perplexica::rebuild-frontend: Rebuilds & Redeploys the perplexica-frontend image.
