# GitHub SSH Key Setup (macOS)

This guide helps you generate a new SSH key for GitHub, add it to the SSH agent, configure SSH, and copy the public key to your clipboard.

---

## 1. Generate a New SSH Key

Run the following command:

```bash
ssh-keygen -t ed25519 -a 100 -f ~/.ssh/github_ed25519 -C "github-$(whoami)-$(date +%Y%m)"
```

### What this does

* `-t ed25519` → Uses the modern Ed25519 algorithm
* `-a 100` → Adds extra key derivation rounds for better security
* `-f ~/.ssh/github_ed25519` → Saves the key with a custom filename
* `-C` → Adds a helpful comment to identify the key

---

## 2. Start the SSH Agent

```bash
eval "$(ssh-agent -s)"
```

This starts the SSH authentication agent in the background.

---

## 3. Add the SSH Key to macOS Keychain

```bash
ssh-add --apple-use-keychain ~/.ssh/github_ed25519
```

This:

* Adds the key to the SSH agent
* Stores the passphrase securely in the macOS Keychain

---

## 4. Configure SSH for GitHub

Append the following configuration to your SSH config file:

```bash
cat >> ~/.ssh/config << 'EOF'
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_ed25519
  IdentitiesOnly yes
  UseKeychain yes
  AddKeysToAgent yes
EOF
```

### Explanation

| Option                | Description                                   |
| --------------------- | --------------------------------------------- |
| `Host github.com`     | Applies this config when connecting to GitHub |
| `HostName github.com` | Actual GitHub hostname                        |
| `User git`            | GitHub uses the `git` user for SSH            |
| `IdentityFile`        | Path to your SSH private key                  |
| `IdentitiesOnly yes`  | Uses only the specified identity              |
| `UseKeychain yes`     | Stores passphrase in macOS Keychain           |
| `AddKeysToAgent yes`  | Automatically adds key to SSH agent           |

---

## 5. Copy the Public Key

```bash
cat ~/.ssh/github_ed25519.pub | pbcopy
```

This copies your public SSH key to the clipboard.

---

## 6. Add the SSH Key to GitHub

1. Open GitHub
2. Go to:

   * **Settings**
   * **SSH and GPG keys**
   * **New SSH key**
3. Paste the copied key
4. Save

GitHub SSH Keys Page:

https://github.com/settings/keys

---

## 7. Test the SSH Connection

Run:

```bash
ssh -T git@github.com
```

Expected output:

```text
Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Optional: Verify Loaded Keys

```bash
ssh-add -l
```

---

## Optional: View Public Key

```bash
cat ~/.ssh/github_ed25519.pub
```

---

## Notes

* Your private key is stored at:

```text
~/.ssh/github_ed25519
```

* Your public key is stored at:

```text
~/.ssh/github_ed25519.pub
```

* Never share your private key.
