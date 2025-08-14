# SSH Setup for Juspay Bitbucket

> **Note:** This guide shows how to set up a dedicated SSH key for Juspay Bitbucket to work alongside other Git accounts (like a personal GitHub). This ensures you use the correct credentials for each service.

---

## Step 1: Generate a Dedicated SSH Key for Juspay

We will create a **separately named SSH key** to avoid conflicts with other services.

### macOS / Linux:
```bash
ssh-keygen -t ed25519 -C "you@juspay.com" -f ~/.ssh/id_ed25519_juspay # -t: Key algorithm, -C: Comment/Label, -f: Filename
```

### Windows (PowerShell):

```powershell
ssh-keygen -t ed25519 -C "you@juspay.com" -f "$env:USERPROFILE\.ssh\id_ed25519_juspay" # Use your Juspay email and a specific key name
```

> **Important:** When prompted for a passphrase, it is highly recommended to set one. This adds an extra layer of security to your private key.
>
> This command creates two files in your `.ssh` directory:
> * **Private key**: `id_ed25519_juspay` (Keep this secret!)
> * **Public key**: `id_ed25519_juspay.pub` (This is what you'll share)

---

## Step 2: Configure SSH to Use the New Key for Juspay

This step tells your system to use the new key **only** when connecting to the Juspay Bitbucket server.

### Open or create the SSH config file:
*   **macOS / Linux:** `nano ~/.ssh/config`
*   **Windows (PowerShell):** `notepad $env:USERPROFILE\.ssh\config`
    > If the file doesn't exist, the command will create it.

### Add this configuration block to the file:

```
# Juspay Bitbucket Account
Host bitbucket.juspay.net
  HostName bitbucket.juspay.net      # The actual hostname of the server.
  User git                          # The user to connect with (always 'git').
  IdentityFile ~/.ssh/id_ed25519_juspay # The path to the specific private key.
  IdentitiesOnly yes                # Ensures SSH only uses this specific key for this host.
```

> **Note for Windows users:** If the `~` shortcut doesn't work, use the full path for `IdentityFile`, like `C:\Users\YourUsername\.ssh\id_ed25519_juspay`.
>
> Save and close the file after adding the block.

---

## Step 3: Add Your Public Key to Juspay Bitbucket

Now, you need to provide your **public key** to Bitbucket so it recognizes your machine.

### Copy the public key to your clipboard:
*   **macOS:** `pbcopy < ~/.ssh/id_ed25519_juspay.pub`
*   **Linux:** `xclip -sel clip < ~/.ssh/id_ed25519_juspay.pub` (You may need to install xclip: `sudo apt-get install xclip` or `sudo dnf install xclip`)
*   **Windows (PowerShell):** `Get-Content "$env:USERPROFILE\.ssh\id_ed25519_juspay.pub" | Set-Clipboard`

### Then, in your Juspay Bitbucket account:

1.  Navigate to your user profile.
2.  Go to **Personal settings → SSH keys**.
3.  Click **Add key**.
4.  Give it a descriptive **Label** (e.g., "Work Laptop - MacBook Pro").
5.  Paste the copied public key into the **Key** field and save.

---

## Step 4: Configure Your Git Identity (Per Repository)

This step ensures that commits you make in Juspay repositories are attributed to your **work identity**.

```bash
git clone git@bitbucket.juspay.net:project/repo.git # Example clone URL, replace with your project's URL.

cd repo # Navigate into the newly cloned repository.

git config user.name "Your Full Name"      # Set your name for this repository.
git config user.email "you@juspay.com" # Set your Juspay email for this repository.
```

> **Why per-repository?**
> Setting your identity this way prevents your work email from being used on personal projects by mistake. Your global Git config remains unchanged for other repositories.

---

## Step 5: Test the SSH Connection

Verify that your machine can successfully authenticate with Bitbucket using the new key.

```bash
ssh -T git@bitbucket.juspay.net # The -T flag tests the connection.
```

You should see a success message from Bitbucket, often including your username. If it works, you are ready to use `git pull`, `git push`, and other commands.

---

## Quick Troubleshooting

*   **"Permission denied" errors:**
    *   Double-check that the `Host` in your `~/.ssh/config` file is `bitbucket.juspay.net`.
    *   Ensure the private key file has the correct permissions. On macOS/Linux, run: `chmod 600 ~/.ssh/id_ed25519_juspay`.
*   **"Key not recognized" or password prompt:**
    *   Confirm you added the **public key** (`.pub` file) to Bitbucket, not the private key.
    *   Make sure the `IdentityFile` path in `~/.ssh/config` is correct.
*   **Windows path issues:**
    *   Always use the full, absolute path for `IdentityFile` in your `.ssh/config` file if you encounter issues (e.g., `C:/Users/YourUser/.ssh/id_ed25519_juspay`).
