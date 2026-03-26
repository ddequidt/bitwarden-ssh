# Bitwarden SSH Auto-Login

A Bash wrapper script that automates SSH authentication using Bitwarden as your credential store. Supports multi-hop scenarios with ProxyJump and OTP (one-time password) authentication.

## Features

- 🔐 Automatically retrieve SSH credentials from Bitwarden vault
- 🔄 Support for multi-hop SSH connections (ProxyJump)
- 🔑 OTP (One-Time Password) support for both proxy and target hosts
- 🛡️ Secure credential handling using kernel keyring
- 🚀 Transparent wrapper around standard SSH commands

## Prerequisites

- **Bitwarden CLI** - [Installation guide](https://github.com/bitwarden/cli)
- **expect** - For automated password/OTP input
- **jq** - JSON query tool
- **keyctl** - Kernel keyring utilities
- A Bitwarden account with SSH credentials stored

### Installation

#### Ubuntu/Debian

```bash
apt install expect jq keyutils
```

#### Install Bitwarden CLI

```bash
# Using npm
npm install -g @bitwarden/cli

# Or download directly from releases
# https://github.com/bitwarden/cli/releases
```

## Installation

1. Clone the repository:
```bash
git clone https://github.com/ddequidt/bitwarden-ssh.git
cd bitwarden-ssh
```

2. Make scripts executable:
```bash
chmod +x bwssh bwssh.expect
```

3. Add to your PATH (optional):
```bash
sudo ln -s "$(pwd)/bwssh" /usr/local/bin/bwssh
```

## Setup

### Bitwarden Vault Structure

Store SSH credentials in Bitwarden with the following fields:
- **Title**: The hostname or connection name
- **Username**: SSH username
- **Password**: SSH password or passphrase
- **Custom Fields**:
  - `otp` - One-time password (if required)
  - `proxy_host` - Proxy jump host (for multi-hop)
  - `proxy_user` - Proxy username
  - `proxy_otp` - Proxy OTP (if required)

### Login to Bitwarden

```bash
bw login your-email@example.com
# Follow the prompts and save your session
export BW_SESSION="<session_token>"
```

## Usage

### Basic SSH connection

```bash
bwssh hostname
```

### With port specification

```bash
bwssh -p 2222 hostname
```

### With additional SSH options

```bash
bwssh -v -X hostname  # Verbose with X11 forwarding
```

### Multi-hop connection (ProxyJump)

The script automatically handles ProxyJump if configured in your Bitwarden vault.

## How It Works

1. Script intercepts SSH command and arguments
2. Queries Bitwarden for credentials matching the hostname
3. Extracts passwords, usernames, and OTP if present
4. Uses `expect` script to handle interactive authentication
5. Passes session forward via kernel keyring for secure credential handling
6. Executes the SSH command with credentials automatically provided

## Security Considerations

- Credentials are stored securely in Bitwarden
- Session tokens are cached in the kernel keyring
- Passwords are not written to disk or shell history
- Uses `set +o history` to prevent command logging
- Kernel keyring provides additional isolation

### Session expiry
Bitwarden sessions expire after inactivity. Re-login:
```bash
bw logout
bw login your-email@example.com
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

Davy Dequidt

## Disclaimer

This tool handles credentials. Use responsibly and ensure Bitwarden master password security. The authors are not responsible for credential misuse or unauthorized access.
