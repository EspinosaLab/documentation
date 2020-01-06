
# Set up SSH keys

## Create the RSA Key Pair

The first step is to create the key pair on the client machine, your
computer:

    ssh-keygen -t rsa

## Store the keys and passphrase

Once you have generated the key, you will get a few more questions:

    Enter file in which to save the key (~/.ssh/id_rsa):

You can press enter here, saving the file to the user home. If you’ve
already done this, be sure not to overwrite your existing key\! You can
skip ahead to the `cat` command, sending the public key to Rosalind.

    Enter passphrase (empty for no passphrase):

Hit enter twice for no passphrase. This then generates two files in your
local ‘~/.ssh’ directory, which looks like this:

    ~/.ssh
    ├── id_rsa
    ├── id_rsa.pub
    └── known_hosts

`id_rsa` is your private key, that can stay in your directory, the
id\_rsa.pub contains your public key for paired authentication.

To add your public key to ~/.ssh/authorized\_keys on the jumphost,
execute the from your local machine following (at your own
    discretion):

    $ cat ~/.ssh/id_rsa.pub | ssh USERNAME@cubipmaccess.ucdenver.pvt "cat >> ~/.ssh/authorized_keys"

This step will prompt you for a password because the paired
authentication has not been setup yet.

It’s possible you’ll see the following message if it’s the first time
you have connected to that server and it must be added to the known
hosts on the computer you are connecting to: type `yes` and hit
    enter.

    The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
    ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
    Are you sure you want to continue connecting (yes/no)? yes

## Create an alias

If you setup ssh-keygen without a passphrase, you should now be able to
ssh into the jumphost server without having to type in a password. To
access Rosalind you’ll still have to execute `ssh hpc`. This can be
automated\! There are two usual routes of configuring ssh connections.
The more elegant way of saving an configurations in a file at
~/.ssh/config. The TICR administration doesn’t allow our connection to
be configured this way, so we’ll use a slightly less refined technique.
Enter `alias`. Using an alias we can piggyback another ssh connection on
our first one and send some commands to keep the server from timing out
so
    quickly.

    $ alias rosalind='ssh -o "ServerAliveCountMax=5" -o "ServerAliveInterval=240" -tt USERNAME@cubipmaccess.ucdenver.pvt ssh -o "ServerAliveInterval=240" -o "ServerAliveCountMax=5" -tt hpc'

After you execute this, upon typing the following alias from your local
machine

    $ rosalind

you will be connected through the jumphost and to Rosalind, potentially
without having to enter a password. The connection will stay alive if no
signal has been transmitted for 240x5 seconds.
