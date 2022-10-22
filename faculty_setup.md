## Faculty Client Installation

The git-keeper client is a command line application that runs in a Unix-like
environment. It works natively in macOS and Linux, and in Windows requires the
Windows Subsystem for Linux (WSL). The client also requires Git and Python 3.8
or greater.

You can install the client system-wide using `sudo` using the command below, or
you can make a virtual environment and install the client there.

```
sudo python3 -m pip install git-keeper-client
```

If you are using Ubuntu within WSL, you may not have pip for Python 3. You can
install it like this:

```
sudo apt install python3-pip
```

## Client Configuration

All client commands are run using `gkeep` along with a subcommand. To
configure the client, run `gkeep config` and follow the prompts. See the
example output below for the hostname, and use your username on the git-keeper
server for the username:

```
gkeep config
Configuring gkeep
Server hostname: ec2-18-205-161-251.compute-1.amazonaws.com
Username: <your username>
Server SSH port (press enter for port 22): 
Submissions fetch path (optional, press enter to skip): 
Assignment templates path (optional, press enter to skip): 
The following will be written to /home/username/.config/git-keeper/client.cfg: 
[server]
host = ec2-18-205-161-251.compute-1.amazonaws.com
username = <your username>

# optional section
[local]

Would you like to proceed? (y/n) y
```

## SSH Keys

All communication with the server is done over SSH. If you have never generated
SSH keys in your environment, you will need to do so now. Run the following:

```
ssh-keygen
```

You can use the default filename, and you do **NOT** want to set a password.

Now you can copy your key to the git-keeper server with `ssh-copy-id`. Use the
password that you received in an email from git-keeper:

```
ssh-copy-id <username>@ec2-18-205-161-251.compute-1.amazonaws.com
```

If everything is set up correctly, `gkeep` should now be able to communication
with the server. Use `gkeep check` to check this:

```
gkeep check
/home/username/.config/git-keeper/client.cfg parsed without errors

Successfully communicated with ec2-18-205-161-251.compute-1.amazonaws.com

Server information:
  Version: 1.0.1
  gkeepd uptime: 20h44m33s
  Firejail installed: True
  Docker installed: True

Server default assignment settings that can be overridden:
  env: firejail
  use_html: True
  timeout: 10
  memory_limit 1024
```
