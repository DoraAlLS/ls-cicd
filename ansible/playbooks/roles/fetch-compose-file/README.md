# Fetch Compose File Role

This Ansible role is designed to fetch and manage Docker Compose files for different environments and services. It supports fetching from both the current repository and a central repository.

## Role Variables

### Required Variables

- `env`: The environment for which the Docker Compose file is being fetched (e.g., `dev`, `stage`, `prod`).
- `service`: The service name for which the Docker Compose file is being fetched.
- `base_dir`: The base directory where the Docker Compose files are stored.

### Optional Variables

- `fetch_from_current_repo`: Boolean flag to indicate if the Docker Compose file should be fetched from the current repository. Default is `true`.
- `fetch_from_central_repo`: Boolean flag to indicate if the Docker Compose file should be fetched from a central repository. Default is `false`.
- `central_repo_url`: The URL of the central repository from which to fetch the Docker Compose file.

## Tasks

### Debugging and Validation (Dry-Run)

1. **Debug output required docker-compose variables**: Outputs the required variables for debugging purposes.
2. **Validate required variables for docker-compose**: Ensures that all required variables are defined and valid.
3. **Construct project_src**: Constructs the project source directory path.
4. **Validate fetch flags don't collide**: Ensures that both fetch flags are not set to the same value.
5. **Validate central repo name is valid**: Validates the central repository URL if fetching from the central repo.
6. **Debug print project_src**: Outputs the constructed project source directory path for debugging purposes.

### Backup Operations

1. **Ensure backup directory exists**: Creates a backup directory if it doesn't exist.
2. **Backup the latest Docker Compose file**: Backs up the latest Docker Compose file to the backup directory.

### File Operations

1. **Copy compose file from current workdir**: Copies the Docker Compose file from the current working directory to the project source directory.
2. **Create temporary directory for central repo**: Creates a temporary directory for cloning the central repository.
3. **Checkout central repo into temporary directory**: Clones the central repository into the temporary directory.
4. **Copy compose file to remote host**: Copies the Docker Compose file from the central repository to the project source directory.
5. **Clean up temporary directory for central repo**: Removes the temporary directory used for cloning the central repository.

## Example Playbook

```yaml
- hosts: all
    roles:
        - role: fetch-compose-file
            vars:
                env: "dev"
                service: "my-service"
                base_dir: "/home/ruby-server/services"
                fetch_from_current_repo: false
                fetch_from_central_repo: true
                central_repo_url: "github.com/LightSolverInternal/ls-cicd.git"
```

## License

This project is licensed under the MIT License.

## Author Information

This role was created in December 2024 by Dor Almog, DevOps Lead.