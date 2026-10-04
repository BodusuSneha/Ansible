# Web Server Automation using Ansible

An Ansible playbook that installs Nginx on multiple Ubuntu servers, makes sure the service is running, and deploys a custom HTML page to each of them.

## What the Playbook Does

`main.yml` runs against the `webservers` group and performs three tasks (with `become: true`):

1. **Install Nginx** with the `apt` module (and refresh the package cache)
2. **Enable and start the Nginx service** (so it also starts after a reboot)
3. **Copy `index.html`** to `/var/www/html/` with permissions `0644`

## Architecture

```
Control node (Ansible) ──SSH──▶ Server 1 (Nginx)
                        └─SSH──▶ Server 2 (Nginx)
```

The servers are listed in `inventory.ini` under the `[webservers]` group.

## Tech Stack

Ansible · Nginx · Ubuntu (apt) · AWS EC2 · YAML · SSH

## Project Structure

```
.
├── main.yml         # playbook
├── inventory.ini    # target servers ([webservers])
├── index.html       # page deployed to each server
└── README.md
```

## Prerequisites

- Ansible installed on the control node
- Two Ubuntu servers (for example EC2 instances) reachable over SSH, with port 80 open in the security group
- Your SSH key, and your servers' IP addresses entered in `inventory.ini` (it contains placeholders)

## How to Run

```bash
git clone https://github.com/BodusuSneha/Ansible.git
cd Ansible

# 1. Edit inventory.ini with your server IPs

# 2. Check connectivity
ansible webservers -i inventory.ini -m ping -u ubuntu --private-key <path-to-key.pem>

# 3. Dry run
ansible-playbook -i inventory.ini main.yml -u ubuntu --private-key <path-to-key.pem> --check

# 4. Apply
ansible-playbook -i inventory.ini main.yml -u ubuntu --private-key <path-to-key.pem>
```

Then open `http://<server-ip>` for each server to see the page.

## Screenshots

Add to a `screenshots/` folder:
- Successful `ansible-playbook` run (PLAY RECAP with `failed=0`)
- The page served by Nginx in the browser

## What I Learned

- Writing playbooks and organizing hosts in an inventory
- Using `become` for privileged tasks and the `apt`, `service` and `copy` modules
- Running the same playbook against several servers so they end up identically configured
- Why Ansible tasks are idempotent: running it twice only changes what needs changing

## Known Limitations / Next Steps

- Move the setup into a role and use group variables
- Use a Jinja2 template for the Nginx configuration
- Provision the servers with Terraform, then configure them with Ansible

## Author

**Sneha Bodusu**: [GitHub](https://github.com/BodusuSneha) · [LinkedIn](https://linkedin.com/in/sneha-bodusu)
