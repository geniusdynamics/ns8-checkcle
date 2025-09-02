# NS8 Checkcle Module

[![Build Status](https://github.com/geniusdynamics/ns8-checkcle/workflows/test-module/badge.svg)](https://github.com/geniusdynamics/ns8-checkcle/actions)
[![License](https://img.shields.io/github/license/geniusdynamics/ns8-checkcle)](LICENSE)

A template module for [NethServer 8](https://github.com/geniusdynamics/ns8-core) that provides checkcle functionality.

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Uninstallation](#uninstallation)
- [Smarthost Settings](#smarthost-settings)
- [Debugging](#debugging)
- [Testing](#testing)
- [UI Translation](#ui-translation)
- [License](#license)

## Installation

Instantiate the module with the following command:

```bash
add-module ghcr.io/geniusdynamics/checkcle:latest 1
```

The output will return the instance name, for example:

```json
{
  "module_id": "checkcle1",
  "image_name": "checkcle",
  "image_url": "ghcr.io/geniusdynamics/checkcle:latest"
}
```

## Configuration

Assuming the instance is named `checkcle1`, launch `configure-module` with the following parameters:

- `host`: Fully qualified domain name for the application
- `http2https`: Enable or disable HTTP to HTTPS redirection (true/false)
- `lets_encrypt`: Enable or disable Let's Encrypt certificate (true/false)

Example configuration:

```bash
api-cli run configure-module --agent module/checkcle1 --data - <<EOF
{
  "host": "checkcle.domain.com",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```

This command will:

- Start and configure the checkcle instance
- Configure a virtual host for Traefik to access the instance

## Usage

### Get Configuration

Retrieve the current configuration:

```bash
api-cli run get-configuration --agent module/checkcle1
```

### Update Module

To update the module to a new version or image, use the following command:

```bash
api-cli run update-module --data '{
  "module_url": "ghcr.io/geniusdynamics/checkcle:latest",
  "instances": ["checkcle1"],
  "force": true
}'
```

This will update the specified instances to the new module URL.

## Uninstallation

To uninstall the instance:

```bash
remove-module --no-preserve checkcle1
```

## Smarthost Settings

Some configuration settings, like the smarthost setup, are discovered automatically by checking Redis keys. The module ensures it stays up-to-date with the centralized [smarthost setup](https://geniusdynamics.github.io/ns8-core/core/smarthost/).

- The `bin/discover-smarthost` command refreshes the `state/smarthost.env` file with fresh values from Redis on startup.
- If smarthost settings change while the module is running, the event handler `events/smarthost-changed/10reload_services` restarts the main service.
- Refer to `systemd/user/checkcle.service` for more details.

This is an example implementation and can be customized or removed as needed.

## Debugging

Use these CLI commands for debugging:

### Verify Environment Variables

Check environment variables set by the agent:

```bash
runagent -m checkcle1 env
```

### Become Runagent

Switch to the runagent environment for testing:

```bash
runagent -m checkcle1
```

The PATH will be updated to:

```
/home/checkcle1/.config/bin:/usr/local/agent/pyenv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/
```

### Inspect Containers

Translations are managed with [Weblate](https://hosted.weblate.org/projects/ns8/).

To set up the translation process:

- Add the [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- Add your repository to [hosted.weblate.org](https://hosted.weblate.org) or ask a NethServer developer to add it to the NS8 Weblate project

## License

This project is licensed under the terms specified in the [LICENSE](LICENSE) file.
