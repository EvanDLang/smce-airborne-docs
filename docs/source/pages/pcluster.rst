===============
Airborne AWS Parallel Cluster
===============

These work-in-progress instructions will be used to guide users how to utilize the Airbone PCluster resource (WIP)
**Note** These are very preliminary instructions that are actively being improved 

Logging In For the First Time
=============================

Setup your ssh keys to allow logging in from a local computer (without requiring access through the Jupyter Hub)

1.  Navigate to https://hub.airborne.smce.nasa.gov

2. Select as small server instance as you will only be using this to create your ssh key handshake

3. once logged in, open a terminal in the Jupyter Hub environment and ssh into the pcluster head node `ssh [ip_address_of_pcluster_head_node]`. You should see a screen similar to below:

.. image:: ../images/pcluster/loginscreen.png

4. Once logged in you will want to confirm that you have a `~/.ssh/authorized_keys` file in your home location. You will be adding external keys to this file from the machines you want to be able to connect from

5. On you local computer, if you havent already created public/private ssh keys you will need to run `ssh-keygen -t ed25519 -C "your_email@example.com"` from your terminal, replacing the placeholder email with your email

6. Once created, you will copy the public key from the file (assuming you didnt change the default name) `id_ed25519.pub`

7. Now paste that key into your `~/.ssh/authorized_keys` file in your home directory on the Airborne PCluster head node

Now create a `~/.ssh/config` if you dont already have one on your local computer to store the information for logging into the head node and setup autocomplete

1. `touch ~/.ssh/config`

2. Edit the `~/.ssh/config` to add the following and save the edits

Host airborne-pcluster
     User sserbin
     HostName XX.XX.XXX.XX
     IdentityFile ~/.ssh/id_ed25519
     StrictHostKeyChecking no
     UserKnownHostsFile=/dev/null

3. For Mac OS `brew install bash-completion`

4. Now edit your `~/.bash_profile` to include the following

`complete -W "$(echo `cat ~/.ssh/config | grep "^Host " |awk '{print $2}'`;)" ssh scp rsync`

5. Open a new terminal tab and then try the autocomplete

`ssh airborne-pcluster`

6. The above command should connect you to the pcluster if you have completed all of the previous steps


