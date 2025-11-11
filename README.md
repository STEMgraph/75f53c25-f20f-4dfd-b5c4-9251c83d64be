<!---
{
  "id": "75f53c25-f20f-4dfd-b5c4-9251c83d64be",
  "teaches": "Using SSH Config Files to Manage Connections",
  "depends_on": ["SSH"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-04-03",
  "keywords": ["SSH", "config", "IdentityFile", "Host alias"]
}
--->

# Using SSH Config Files to Manage Connections

## 1) Introduction
Once you've generated and installed your SSH key pair, you'll likely find yourself connecting to several remote servers—perhaps daily. Typing full commands like `ssh -i ~/.ssh/my-special-key.pem user@192.168.178.24 -p 2234` can become tedious and error-prone. To streamline your workflow, OpenSSH provides a powerful yet underused tool: the `~/.ssh/config` file.

This file allows you to define **per-host settings** like:
- Default usernames
- Ports
- Hostnames
- Identity files
- Proxy settings
- KeepAlive intervals

With this configuration file in place, you can connect to a remote machine simply by typing `ssh myserver`, even if it uses a non-default port or key. This abstraction increases usability **without compromising security**—a hallmark of well-designed systems.

The `~/.ssh/config` file has been part of OpenSSH since the early days of secure remote access, and its use is championed by seasoned engineers such as **Theo de Raadt** (OpenBSD) and **Bryan Cantrill** (ex-Sun Microsystems), who advocate for tools that help automate secure practice.

Let’s explore how to write a good config file and use it to manage multiple connections with ease.

<details>
  <summary>Fun Fact</summary>
  Did you know that `.ssh/config` supports wildcard patterns, nested includes, and even conditional directives? For large teams or complex deployments, it becomes a tiny domain-specific language for secure access.
</details>

## 2) Tasks

1. **Inspect Your SSH Directory**:
   - Run `ls -la ~/.ssh` to check if a `config` file already exists.
   - If not, create one with `touch ~/.ssh/config`.
   - Set the correct permissions: `chmod 600 ~/.ssh/config`

2. **Create a Simple Config Entry**:
   Edit your `~/.ssh/config` file with your favorite editor (e.g. `vim ~/.ssh/config`) and add the following:

   ```ssh-config
   Host panda
       HostName 192.168.178.24
       User pandauser
       Port 2234
       IdentityFile ~/.ssh/id_ed25519
   ```

   Replace the values with those matching your server. Save the file.

3. **Use Your Alias**:
   Try connecting to your server simply by typing:

   ```sh
   ssh panda
   ```

   This should behave exactly like the longer command you used before.

4. **Add Multiple Hosts**:
   Add a second block for another host you frequently access. Optionally, give it a nickname like `labmachine`.

5. **Use a Wildcard Entry**:
   Add a default rule for all unknown hosts:

   ```ssh-config
   Host *
       ServerAliveInterval 60
       ServerAliveCountMax 3
   ```

6. **Verify Config Behavior**:
   Use `ssh -v panda` to see the verbose output and confirm that your config file is being respected.

7. **Include an Additional Config File**:
   Create a sub-config file `~/.ssh/config.d/lab` and include it in your main config:

   ```ssh-config
   Include ~/.ssh/config.d/*
   ```

   Add a new host entry in `config.d/lab` and verify that it's being picked up.


## 3) Questions

1. What are the benefits of using the SSH config file over typing full commands?
2. What permissions should your config file have and why?
3. How does the SSH client decide which IdentityFile to use?
4. What does the `Host *` wildcard mean, and when is it useful?
5. What happens if two entries match the same host? Which one wins?


## 4) Advice
Using a `~/.ssh/config` file is not just about comfort—it's about reproducibility and clarity. Document your access patterns like you would document your code. For complex systems, version-controlled SSH config files can be a game changer. Also, consider watching the ["SSH Tricks" talk by Julia Evans](https://www.youtube.com/watch?v=1z2xG_ZJwck), which covers many practical patterns.

If you're curious how all this works under the hood, check out the OpenSSH source code, particularly the `readconf.c` file which implements config parsing.

