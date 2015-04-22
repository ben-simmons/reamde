Make your .ssh directory.
```
$ cd ~
$ mkdir .ssh
$ chmod 700 .ssh
```

If you like security, then it's a good idea to set up SSH access with every service that provides it.
First, generate a new keypair for each account you have with each service.
```
$ ssh-keygen -t rsa  # append -C "user@host" if you want to use an identifier other than your machine name
```
Set output file appropriately, e.g. something like ```id_rsa.github.personal``` for your personal Github account.
It's also a good idea to set a password for each keypair, so that your SSH keys can't be used even if your machine is compromised.
Of course, we mean "compromised" in the malicious remote access way, not in the "leaving your computer unlocked in a coffee shop" way,
or the storied "wrench held menacingly in direct opposition to your bodily person" way.

Add your private SSH keys to the keychain, so you don't have to re-enter their passwords while you use this machine.
More technically, you won't have to re-enter them for the duration of your ```ssh-agent``` session, but this duration is permanent
unless you use the ```-t``` switch on either ```ssh-agent``` (for all identities) or ```ssh-add``` (for individual identities).
```
$ ssh-add ~/.ssh/id_rsa.github.personal  # use the -t switch if you want to specify a lifetime for this particular identity
```

Each service will require you to tell them the *public* key you've generated for SSH access. This is the file with the ```.pub``` extension.
The private key should never leave your machine. You can easily copy your public key to clipboard like so:
```
$ pbcopy < ~/.ssh/id_rsa.github.personal.pub
```

Make your SSH config file.
```
$ cd ~/.ssh
$ touch config
$ chmod 644 config
```

Example config file. You'll want a unique ```Host``` entry for each of your service/account combos.
If you use multiple machines, then ideally you'll make a unique config file for each machine.
Actually, I suppose the config file could be exactly the same for each machine. But hopefully your identity files will not be the same.
Because that would mean your private keys mysteriously crossed the voids of space and time to escape the locality of their genesis. Probably through email.
Which would be a very sad state of affairs indeed. Note that the ```IdentityFile``` entry is the path to the *private* key.
```
# Your personal account with Github to be accessed from this machine
Host github-personal
  User git
  Hostname github.com
  Port 22
  IdentityFile ~/.ssh/id_rsa.github.personal
  IdentitiesOnly yes

# Your company account with Github to be accessed from this machine
Host github-company
  User git
  Hostname github.com
  Port 22
  IdentityFile ~/.ssh/id_rsa.github.company
  IdentitiesOnly yes
```

Now you can drop any of those ```Host``` identifiers into any SSH URI on this machine. Example with a git repository:
```
$ git remote set-url origin github-personal:/simmbot/dotfiles.git
```

And example output from ```git remote -v```:
```
origin  github-personal:/simmbot/dotfiles.git (fetch)
origin  github-personal:/simmbot/dotfiles.git (push)
```

