# Ansible Role: Docker Compose Management

## Description
This role manages Docker Compose projects in a structured and automated way. It performs operations such as removing and restarting Docker containers and services using Docker Compose. The role also supports refreshing AWS ECR credentials for private container repositories.

## Features
- **Dynamic Variables**:
  - Constructs the `project_src` path dynamically using `base_dir`, `env`, and `service`.
- **Validation**:
  - Ensures required variables are defined and adhere to specific patterns (e.g., regex validation for `env` and AWS region).
- **Error Handling**:
  - Implements robust error handling with retries for Docker Compose operations.
  - Logs detailed debug information upon failure.
- **Support for AWS ECR**:
  - Automatically refreshes ECR credentials for pulling private Docker images.

## Requirements
- **Dependencies**:
  - `community.docker` collection for Docker Compose operations.
  - `amazon.aws` collection for AWS ECR login.

Install required collections:
```bash
ansible-galaxy collection install community.docker amazon.aws
```

- **Software**:
  - Docker and Docker Compose installed on the target machine.
  - AWS CLI installed and configured (for ECR login).

- **Roles**:
  - This role is intended for execution after fetch-compose-file, in order to get a latest version of the docker-compose file - Although it can be executed independently.

## Role Variables
The role uses the following variables. Default values are provided in defaults/main.yml.
| Variable            | Description                                                 | Default Value              |
|---------------------|-------------------------------------------------------------|----------------------------|
| `env`               | The environment (e.g., `dev`, `stage`, `prod`).             | `dev`                     |
| `base_dir`          | The base directory for the project.                         | `/home/ruby-server/services` |
| `service`           | Name of the Docker Compose service.                         | _None (must be provided)_  |
| `aws_account_id`    | AWS account ID for ECR authentication.                      | `541441380680`            |
| `default_aws_region`| Default AWS region for ECR authentication.                  | `eu-central-1`            |

## Tasks Overview
The role executes the following tasks:

1. **Validate Variables**:
   - Ensures required variables are defined.
   - Validates the `env` variable using a regex pattern.

2. **Refresh AWS ECR Credentials**:
   - Refreshes AWS ECR credentials for pulling private Docker images.

3. **Remove Docker Containers**:
   - Stops and removes Docker containers for the specified service.

4. **Restart Docker Compose Service**:
   - Restarts the Docker Compose service.

5. **Error Handling**:
   - Implements error handling with retries for Docker Compose operations.

## Example Playbook
```yaml
- hosts: localhost
  roles:
    - role: deploy-compose-app
      vars:
        service: gp-solver
        env: stage
        base_dir: /home/ruby-server/Update
```

## Tags
The following tags can be used for selective execution:
- `dry-run`: Mainly performs validation and prints the variables.
- `docker-ops`: Executes Docker Compose operations - *Without Validation*

## Contributing
Contributions are welcome! Please feel free to raise an issue or submit a pull request. Contact @DevOps on any issue

## License
Internal - Not for public distribution

## Author Information
This role was created in December 2024 by Dor Almog, DevOps Lead