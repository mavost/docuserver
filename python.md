# Python customization

## Adding alias for python3

Edit the .bashrc file and add an alias:

```bash
alias python=python3
```

## Support for virtual environments

- Install *venv* module, and add a new python environment:

```bash
sudo apt install python3.10-venv
mkdir ~/pythonenvs
python -m venv ~/pythonenvs/<custom_env_name>
```

- Activate using: `source ~/pythonenv/<custom_env_name>/bin/activate`
- Deactivate using: `deactivate`
