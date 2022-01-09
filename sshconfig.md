# How to set up ssh so that you can access virtual machines without having to type your user@host and password every time
1. If you don't have it already, make the `.ssh` directory in your homedir with `mkdir .ssh`

## So that you don't have to type your username every time...
1. In the `.ssh` directory, create the file `config` (no extension).
	(I just use `vim config`, but this will vary by your editor.)
2. The `config` file's contents will be something like:

```
# MY VIRTUAL MACHINE
Host vm
	HostName thehostname.com
	User gobborg
```

## So that you don't have to type your password every time...
1. In the `.ssh` directory, run `ssh keygen -t rsa` to create public and private RSA keys.
	(For whatever reason, RSA is preferable to DSA for my hosts.)
2. Copy the public key file to your virtual machine with `scp id_rsa.pub user@host:`. The : is important.
3. SSH like normal (with user@host and password) in to your virtual machine.
4. In your virtual machine's homedir, create the `.ssh` directory if necessary.
5. Do `chmod 700` on your VM's .ssh directory to give you and only you *read write execute* permissions.
6. `cd` into the VM's `.ssh` directory.
7. Run `cp ../id_rsa.pub authorized_keys` to copy the public key from the homedir and create the file `authorized_keys`. The `authorized_keys` file must have the \_.
8. Do `chmod 644` on the `authorized_keys` file to give you *read write* permissions and others can only *read* it.
9. Exit the VM.

## Test your access
1. In the terminal, run `ssh vm` or whatever you put next to "Host" in the `config` file.
2. You should be able to access now.

## Caveats and troubleshooting
1. GNOME implemented a keystore or something, so if you're running GNOME, you might be asked to create a password when you do `ssh keygen`. This password is different from your user password. You'll have to input that password when you ssh.
2. If you have trouble gaining access, try running `ssh -v -v user@host` and read the output for errors.
	I first had trouble because I created DSA instead of RSA keys. I ran that command and got `debug1: Skipping ssh-dss key /home/gobborg/.ssh/id_dsa - not in PubkeyAcceptedKeyTypes`
