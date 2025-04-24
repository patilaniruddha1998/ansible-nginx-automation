# NGINX Automation with Ansible

This project automates the deployment, upgrade, and validation of NGINX servers across multiple environments (Development, UAT, Production) using Ansible. Validation includes tasks such as syntax checks of configuration files, service status checks, and ensuring proper functionality. The folder structure follows Ansible best practices to ensure modularity, scalability, and maintainability.

---

## Folder Structure

### 1. **`ansible.cfg`**
The central configuration file for Ansible. It defines default settings such as inventory location, roles path, and connection options. For example, you can specify the inventory file path as follows:

### 2. **`inventories/`**
Contains inventory files for different environments:
- **`development/`**: Inventory for the development environment.
- **`uat/`**: Inventory for the UAT environment.
- **`production/`**: Inventory for the production environment.

Each environment contains:
- `hosts`: Static inventory file defining groups and hosts.
- `group_vars/`: Variables applied to groups.
- `host_vars/`: Variables applied to individual hosts.

### 3. **`playbooks/`**
Contains playbooks for specific tasks:
- `upgrade.yml`: Playbook for upgrading NGINX.
- `deploy.yml`: Playbook for deploying NGINX.
- `validate.yml`: Playbook for validating NGINX configuration.

### 4. **`roles/`**
Contains reusable roles for modular task execution:
- **`nginx_upgrade/`**: Role for upgrading NGINX.
- **`nginx_deploy/`**: Role for deploying NGINX.
- **`nginx_validate/`**: Role for validating NGINX configuration.

Each role contains:
- `tasks/`: Main tasks for the role.
- `handlers/`: Handlers triggered by tasks.
- `templates/`: Jinja2 templates for configuration files.
- `files/`: Static files (e.g., RPM packages).
- `vars/`: Role-specific variables.
- `defaults/`: Default variables for the role.
- `meta/`: Metadata for the role (e.g., dependencies).

### 5. **`site.yml`**
The main playbook that imports other playbooks. It serves as the entry point for running the automation.

---

## Example Inventory File (`uat/hosts`)

```ini
# Parent group for all web servers
[web-servers:children]
internet-servers
intranet-servers
ampfiles-servers

# Group for internet-facing Thanos servers
[internet-servers]
thanos_uat_internet_1 ansible_host=10.20.22.30 ansible_port=12323 ansible_user=ansibleuser # Thanos Internet Server 1
thanos_uat_internet_2 ansible_host=10.20.22.12 ansible_port=12323 ansible_user=ansibleuser # Thanos Internet Server 2

# Group for intranet-facing Thanos servers
[intranet-servers]
thanos_uat_intranet_1 ansible_host=10.20.166.28 ansible_port=12323 ansible_user=ansibleuser # Thanos Intranet Server 1
thanos_uat_intranet_2 ansible_host=10.20.166.60 ansible_port=12323 ansible_user=ansibleuser # Thanos Intranet Server 2

# Group for Ampfiles intranet servers
[ampfiles-servers]
ampfiles_uat_intranet ansible_host=10.20.166.23 ansible_port=12323 ansible_user=ansibleuser # Ampfiles Intranet Server

[test-server]
test ansible_host=10.20.166.23 ansible_port=12323 ansible_user=ansibleuser # Ampfiles Intranet Server

# The [test-server] group is used for testing purposes, allowing validation of playbooks and configurations in a controlled environment.


Example Usage
1. Run the Main Playbook (site.yml)
> **Note**: Ensure you have the correct version of Ansible installed and all prerequisites (e.g., Python, required modules) are met before running the command.


"ansible-playbook site.yml -i inventories/uat/hosts"
> This command runs all playbooks defined in `site.yml` for the UAT environment using the specified inventory file.

2. Run a Specific Playbook
Run the upgrade.yml playbook for the production environment:

"ansible-playbook upgrade.yml -i inventories/production/hosts"

3. Target a Specific Group
Run the deploy.yml playbook for the internet-servers group in the UAT environment:

"ansible-playbook playbooks/deploy.yml -i inventories/uat/hosts -l internet-servers"

4. Target a Specific Host
Run the validate.yml playbook for the thanos_uat_intranet_1 server:

a"nsible-playbook playbooks/validate.yml -i inventories/uat/hosts -l thanos_uat_intranet_1"

5. Check Connectivity
Ping all servers in the UAT environment to verify connectivity:

"ansible -i inventories/uat/hosts all -m ping"


A successful ping response will display "pong" messages from all targeted servers, indicating that they are reachable. If any server fails to respond, check the following:
- Ensure the server is powered on and connected to the network.
- Verify the inventory file has the correct `ansible_host`, `ansible_port`, and `ansible_user` values.
- Check for firewall rules or network restrictions that may block connectivity.


## Best Practices

1. **Use Version Control**: Store your Ansible playbooks, roles, and inventory files in a version control system like Git to track changes and collaborate effectively.

2. **Test in a Staging Environment**: Always test your playbooks and configurations in a staging or test environment before applying them to production.

3. **Use Dynamic Inventory**: Consider using dynamic inventory scripts or plugins for environments with frequently changing infrastructure.

4. **Follow Role-Based Structure**: Organize your tasks into reusable roles to improve modularity and maintainability.

5. **Use Vault for Sensitive Data**: Encrypt sensitive information like passwords and API keys using Ansible Vault.

6. **Enable Logging**: Configure Ansible to log outputs for auditing and troubleshooting purposes.

7. **Limit Scope with Tags**: Use tags to run specific tasks or subsets of playbooks, reducing execution time and risk.

8. **Keep Playbooks Idempotent**: Ensure your playbooks can be run multiple times without causing unintended changes.

9. **Document Your Code**: Add comments and documentation to your playbooks and roles for better understanding and maintainability.

10. **Monitor and Validate**: Regularly validate configurations and monitor the state of your infrastructure to ensure consistency.ivity manually using the `ping` or `telnet` command to the servers IP and port.
