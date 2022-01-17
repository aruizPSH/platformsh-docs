---
title: Authenticate with SSH keys
description: See how to authenticate your app connections using SSH keys.
sidebarTitle: SSH keys
---

To connect to your app using SSH keys, you need two keys:

* A **private key** you must keep _secret_
* A **public key** stored in your Platform.sh account

A key pair is valid for as long as you have access to the private key on the system from which you are connecting.
If you have a key pair available, you aren't prompted to login.

To keep connection secure, you need to regularly update the keys you use.
A well-encrypted key is no substitute for regular key rotation.

If you used GitHub to sign up for your Platform.sh account
your public keys from GitHub are automatically synced to your Platform.sh account.
So you can use them already with the CLI or to [connect to your app](./_index.md#2-connect-to-an-app-with-ssh).

## Add SSH keys

If you don't have keys already added,
you might be able to [find existing keys](#1a-find-your-keys) or else you need to [generate new keys](#1b-generate-new-keys).

### 1A. Find your keys

You may have already generated SSH keys before.
Tech Republic has a guide to [finding keys on different systems](https://www.techrepublic.com/article/how-to-view-your-ssh-keys-in-linux-macos-and-windows/).

If you haven't used SSH keys before or it's been a while since you created the key,
skip right to [generating new keys](#1b-generate-new-keys).

A public key file has a name ending in `.pub`.
It contains seemingly random lines of characters,
like this example of a public [RSA key](https://en.wikipedia.org/wiki/RSA_%28cryptosystem%29)
(note the email address at the end, which wouldn't be present in a private key):

```text
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC2nDRLgPANWParTiaGIgySG+thTtnqFGI1tMWyqDdfvH+5hL91w2tK9PzaP+NJ5hA/cOyh30YRFb52Y64toU16Ko5K1mLqNFJajjWEI5Y4VukG6betrWfqdQ7XBr/s7nBuDOFQ5+eKbvug4rRSCSo8CsEI1eI0VNQkC9HJWYK28k7KurMdTN7X/Z/4vknM4/Rm2bnMk2idoORQgomeZS1p3GkG8dQs/c0j/b4H7azxnqdcCaR4ahbytX3d49BN0WwE84C+ItsnkCt1g5tVADPrab+Ywsm/FTnGY3cJKKdOAHt7Ls5lfpyyug2hNAFeiZF0MoCekjDZ2GH2xdFc7AX/ your_email_address@example.com
```

To find your public key file:

1. Open a terminal.
2. Run the following commands:

   ```bash
   cd ~/.ssh
   ls -a
   ```

If you find a file ending in `.pub`,
copy the location and [add it to your Platform.sh account](#2-add-an-ssh-key-to-your-platform-account).

If you don't find an existing key, [generate new keys](#generate-new-keys).

### 1B. Generate new keys

If you're logged in using the [Platform.sh CLI](./_index.md#1-authenticate-with-the-platformsh-cli),
generate a key and have it added to your Platform.sh account automatically.

1. In a terminal, run `platform ssh-key:add`.
1. If necessary, log in to a browser.
1. Press `Y` and `enter` to create a new SSH key.
1. Copy the location of the generated key.
1. Run the following commands (replacing `PATH_TO_YOUR_KEY` with the location you copied):

   ```bash
   val $(ssh-agent)
   ssh-add 'PATH_TO_YOUR_KEY'
   ```

To generate a key otherwise, GitHub has a good [walk-through for creating SSH key pairs](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) on various operating systems.

Then you need to [add it to your Platform.sh account](#2-add-an-ssh-key-to-your-platform-account).

### 2. Add an SSH key to your Platform account

Once you have the location of your public key, add it to your Platform.sh account.

If you're logged in using the [Platform.sh CLI](./_index.md#1-authenticate-with-the-platformsh-cli),
in a terminal run the following command (replacing `<PATH_TO_YOUR_KEY>` with the location of your public key):

```bash
platform ssh-key:add '<PATH_TO_YOUR_KEY>'
```

You can also add it in the management console,
similar to this [video](https://docs.platform.sh/videos/management-console/add-ssh-mc.mp4).

Now you are ready to use the key to [connect to an environment](./_index.md#2-connect-to-an-app-with-ssh).

### 3. Connect to your server with SSH keys

To connect to a server using SSH keys, find the details in the management console:

1. Open the [Platform.sh console](https://console.platform.sh/).
1. Select a project.
1. In the **Environment** dropdown, select the environment you want to access.
1. Click the **SSH** dropdown.
1. Copy the ssh command for where you want access.
   (Example: `ssh abcdefghi5k-main-7rqtwti--app@ssh.region.url`)
1. Enter the command into a terminal.

Note that if you have just added your SSH key,
you need to [redeploy your environment](./troubleshoot-ssh.md#redeploy-your-environment) before you can access it using SSH keys.

## Forwarding keys by default

It may be helpful to set your SSH client to always forward keys to Platform.sh servers, which can simplify other SSH or rsync commands.
To do so, include a block in your local `~/.ssh/config` file like so:

```text
Host *.us.platform.sh
       ForwardAgent yes
Host *.eu.platform.sh
       ForwardAgent yes
```

Include one `Host` entry for each Platform.sh region you want to connect to, such as `us-2` or `eu-4`.
(You can include other configuration as desired.)