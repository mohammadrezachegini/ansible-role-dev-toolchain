# ansible-role-dev-toolchain

Installs a project's local development tools on Ubuntu, only if they're missing, so a second run reports `changed=0`.
Shared by every project in my portfolio: each project lists the tools it needs in its own `bootstrap.yml`.

## Supported tools

| Name | What gets installed |
| --- | --- |
| `postgresql-client` | `psql` (apt) |
| `redis-cli` | `redis-cli` from `redis-tools` (apt, no server) |
| `docker` | Docker Engine + Compose v2 (Ubuntu packages), only if `docker compose` doesn't already work; adds the user to `docker` |
| `uv` | uv, pinned installer (`toolchain_uv_version`), into `~/.local/bin` |
| `python3.12` | Python 3.12 managed by uv, next to the system Python |
| `awscli` | AWS CLI v2, official installer (`/usr/local/bin/aws`) |
| `localstack` | `lstk`, LocalStack's CLI (pinned release, checksum-verified) |
| `awslocal` | AWS CLI + `lstk` (`awslocal` is deprecated; use `lstk aws`) |
| `pre-commit` | pre-commit, as a uv tool |
| `azurite` | pulls the pinned Azurite image (`toolchain_azurite_image`) |

## Use it from a project

```yaml
# requirements.yml
roles:
  - name: dev-toolchain
    src: https://github.com/mohammadrezachegini/ansible-role-dev-toolchain.git
    scm: git
    version: v0.1.0
```

```yaml
# bootstrap.yml
- hosts: localhost
  connection: local
  roles:
    - role: dev-toolchain
      vars:
        toolchain_tools: [python3.12, docker, awscli]
```

```bash
ansible-galaxy role install -r requirements.yml
ansible-playbook bootstrap.yml -K
```

Use an inventory with `ansible_python_interpreter=/usr/bin/python3` (Ubuntu's Python has `python3-apt`).

## Test the role itself

```bash
ansible-playbook -i tests/inventory tests/test.yml -K
```

Requirements: Ubuntu, `ansible-core >= 2.17`, working apt sources.
