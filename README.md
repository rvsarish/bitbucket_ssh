# SSH Setup for Multiple Git Accounts (Company Bitbucket & GitHub)

# Make sure to replace all instances of "Company" with your actual company name. This is just a demo and get access for bitbucket and your companys repos.

This guide provides a clear, step-by-step process for setting up a dedicated SSH key for your Company Bitbucket account. This method ensures your Company key is used only for Company repositories, preventing conflicts with other accounts like a personal GitHub.

---

### Step 1: Generate a Dedicated SSH Key for Company

To avoid conflicts with any existing keys (like one for GitHub), we will create a new, specifically named key for Company.

1.  Open your terminal and run the following command, which creates a new key and names the files `id_ed25519_Company`:

    ```bash
    ssh-keygen -t ed25519 -C "sarish.rv@Company.in" -f ~/.ssh/id_ed25519_Company
    ```

2.  When prompted, enter a secure passphrase. This is a password for your SSH key and adds an extra layer of security.

This creates two files: `~/.ssh/id_ed25519_Company` (private key) and `~/.ssh/id_ed25519_Company.pub` (public key).

---

### Step 2: Configure SSH to Use Your New Key for Company

This is the most important step for managing multiple accounts. We will tell SSH which key to use for the Company Bitbucket host.

1.  Open the SSH config file in a text editor. If it doesn't exist, this command will create it:
    ```bash
    nano ~/.ssh/config
    ```

2.  Add the following block to the file. This configuration directs SSH to use your new Company key whenever it connects to `ssh.bitbucket.Company.net`.

    ```
    # Company Bitbucket Account
    Host ssh.bitbucket.Company.net
      HostName ssh.bitbucket.Company.net
      User git
      IdentityFile ~/.ssh/id_ed25519_Company
      IdentitiesOnly yes
    ```

3.  Save and close the file. (In `nano`, press `Ctrl+X`, then `Y`, then `Enter`).

---

### Step 3: Add the Public SSH Key to Your Company Bitbucket Account

1.  Copy your new **public** key to the clipboard:
    ```bash
    pbcopy < ~/.ssh/id_ed25519_Company.pub
    ```
    *(On Linux, you might need `xclip -sel clip < ~/.ssh/id_ed25519_Company.pub`)*

2.  Navigate to your Company Bitbucket account settings:
    - Click your avatar > **Personal settings**.
    - Go to **SSH keys** under the **Security** section.

3.  Click **Add key**, give it a descriptive label (e.g., "Work MacBook"), paste the copied key, and save.

---

### Step 4: Configure Git Identity (Optional but Recommended)

For Company repositories, you can set a local Git config to ensure you always commit with your work email.

1.  Clone your project first:
    ```bash
    git clone ssh://git@ssh.bitbucket.Company.net/reponame.git
    ```

2.  Navigate into the repository directory:
    ```bash
    cd repo
    ```

3.  Set your name and email **for this repository only**:
    ```bash
    git config user.name "Sarish RV"
    git config user.email "sarish.rv@Company.in"
    ```
    This overrides your global Git config, ensuring your work commits are correctly attributed without affecting your personal projects.

---

### Step 5: Test and Use

Your setup is complete. You can now clone, pull, and push to Company repositories, and your system will automatically use the correct key. Your personal GitHub and other accounts will remain unaffected.
