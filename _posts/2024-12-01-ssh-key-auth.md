---
layout: post
title: Set up SSH public key authentication for remote server
date: 2024-12-01 15:00:00
description:
tabs: true
---

Whenever you have a personal VPS or a corporate server that allows public access, if you open the SSH password login authentication method on port 22, you will be vulnerable to brute-force attacks. You can use [fail2ban](https://github.com/fail2ban/fail2ban) or [sshguard](https://www.sshguard.net/) to protect your server from these attacks or change the default port to a non-standard port. However, the relatively safe and simple way to protect your server is to disable password authentication and use public key authentication instead.

Suppose we configure SSH to connect from computer A to server B using public key authentication.

When you attempt to connect to server B from computer A, the server generates a session-based random challenge and sends it to computer A. Computer A uses its private key to sign the challenge and returns the signed result to the server. The server then verifies the signature using the corresponding public key stored on the server. If the verification succeeds, it confirms that computer A possesses the private key matching the public key, thereby authenticating the client. The server then allows the connection and establishes a secure communication session. Throughout this process, the private key remains securely stored on the client, and all communication is encrypted to prevent interception or tampering.

## 1 - Generate SSH key pair on computer A

Type the following command in the terminal to generate an SSH key pair:

```bash
ssh-keygen -t ed25519 -C "example@example.com"
```

This command generates an SSH key pair using the [ed25519](https://en.wikipedia.org/wiki/EdDSA) algorithm. The key pair will be stored in the `~/.ssh` directory by default if you press `Enter` without specifying a file path. When prompted to enter a passphrase, it is recommended to enter a passphrase to protect your private key. The SSH key pair consists of a public key (e.g., `id_ed25519.pub`) and a private key (e.g., `id_ed25519`) in the `~/.ssh` directory.

Use [RSA](<https://en.wikipedia.org/wiki/RSA_(cryptosystem)>) algorithm by replacing `-t ed25519` with `-t rsa -b 4096` if the server does not support the ed25519 algorithm.

## 2 - Copy the public key of computer A to server B

Use the following command to get your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Output should look like this:

```Markdown
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIB8J6 example@example.com
```

Log into server B, create the `~/.ssh` directory if it does not exist, and append the public key to the `~/.ssh/authorized_keys` file:

```bash
mkdir -p ~/.ssh
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIB8J6 example@example.com" >> ~/.ssh/authorized_keys
```

Then, make sure the permissions are set correctly:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

## 3 - Disable password authentication on server B

Edit the SSH configuration file on server B:

```bash
sudo vim /etc/ssh/sshd_config.d/20-force_publickey_auth.conf
```

Add the following lines to the configuration file, then save and close the file:

```Markdown
PasswordAuthentication no
AuthenticationMethods publickey
```

It is also recommended to add an additional configuration to disable root login:

```bash
sudo vim /etc/ssh/sshd_config.d/20-deny_root.conf
```

Add the following line to the configuration file, then save and close the file:

```Markdown
PermitRootLogin no
```

## 4 - Restart the SSH service on server B

Restart the SSH service to apply the changes:

```bash
sudo systemctl restart sshd
```
