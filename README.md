# Install/Deploy/Manage Perplexica using Ansible

Perplexica is an AI-powered search engine. It is an Open Source alternative to Perplexity AI, running locally using Open Source tools like Ollama. For more information, visit https://github.com/ItzCrazyKns/Perplexica

This ansible playbook should help install/manage/upgrade an instance of Perplexica on your local or remote system.

# A Quick how to get started

To install/update Perplexica, run:
```bash
# git clone the repo, cd into the folder and run:
ansible-playbook install-perplexica.yaml --diff

# Or you can use ansible-pull, like:
ansible-pull --diff -U https://github.com/jefferyb/ansible-perplexica.git -C main install-perplexica.yaml

# If you're running docker using lima-vm on MacOS, try:
ansible-playbook install-perplexica.yaml --diff -e ollama_base_url=http://host.lima.internal:11434
```

After the playbook is complete, you should be able to access Perplexica on port 3003 ( since the default port 3000 is popular, I switched to 3003. Or, you can use `-e frontend_port=3000` to switch it to 3000, or whatever port you would like ), http://localhost:3003

### Some settings you can change/do

To test ollama connection, you can try with --tags perplexica::ollama-connection, like:

```bash
ansible-playbook install-perplexica.yaml --diff --tags perplexica::ollama-connection 
```

To specify your own/specific local/remote ollama url, you can try something like:
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

  * perplexica::ollama-connection: Useful for testing local/remote connections... It won't be able to test the `host.{docker,lima}.internal` connections
  * perplexica::update-config: Updates the config.toml file.
  * perplexica::rebuild-searxng: Rebuilds & Redeploys the perplexica-searxng image.
  * perplexica::rebuild-backend: Rebuilds & Redeploys the perplexica-backend image.
  * perplexica::rebuild-frontend: Rebuilds & Redeploys the perplexica-frontend image.

### Variables

You can update/change some settings/variables by using `-e` or `--extra-vars`. You can see which ones you can use, starting from (line 36)[https://github.com/jefferyb/ansible-perplexica/blob/main/install-perplexica.yaml#L36]

For example, you can specify a perplexica version, with something like:
```bash
ansible-playbook install-perplexica.yaml --diff -e perplexica_version=v1.3.4
```

# Debugging

### No module named 'requests'

**Error:**

If you get an error like:

```yaml
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: ModuleNotFoundError: No module named 'requests'
fatal: [localhost]: FAILED! => changed=false
  msg: Failed to import the required Python library (requests) on jefferyb-mac's Python /usr/local/opt/python@3.12/bin/python3.12. Please read the module documentation and install it in the appropriate location. If the required library is installed, but Ansible is using the wrong Python interpreter, please consult the documentation on ansible_python_interpreter
```

**Solution:**

You can resolve it with something like:

```bash
 /usr/local/opt/python@3.12/bin/pip3 install --break-system-packages requests
```

### Error while fetching server API version

**Error**:
```yaml
  msg: 'Error connecting: Error while fetching server API version: (''Connection aborted.'', FileNotFoundError(2, ''No such file or directory''))'
```

**Solution**:

I've seen this when using [lima-vm](https://github.com/lima-vm/lima). Make sure that `DOCKER_HOST` is set to the right context. ( sometimes we might have to restart the docker vm too )

```bash
# check if docker is running
docker --version

# check what context is being used
docker context ls

# set DOCKER_HOST
export DOCKER_HOST=$(limactl list docker --format 'unix://{{.Dir}}/sock/docker.sock')

# try the ansible playbook again
```
