==============================
Airborne SMCE Parallel Cluster
==============================

The Airborne SMCE parallel cluster is a SLURM-based high-performance computing cluster that operates alongside JupyterHub.
It provides users with access to additional compute resources beyond what is available in JupyterHub alone.

The two environments are fully integrated, allowing you to seamlessly read from and write to your JupyterHub home directory and shared storage without encountering permission issues.

To access the Airborne SMCE Parallel Cluster, contact an administrator to have your user added to the `pcluster` group.

Logging in for the First Time
=============================

Once you have been added to the `pcluster` group, your first login to the cluster must be through JupyterHub.

1. Navigate to the `Airborne SMCE <https://hub.airborne.smce.nasa.gov>`_.
2. Select and start a server.
3. During server startup, your PCluster user will be automatically configured, including the SSH configuration.
4. Once inside JupyterLab, open a terminal and run: ``ssh pcluster``

You will be connected to the PCluster head node.

Your PCluster user and your JupyterHub user are the same, allowing you to read and write files on both systems.  
The PCluster has a separate home directory, but you can also access your JupyterHub home directory as well as the shared drive via:

- `/efs/home/<username>`
- `/efs/shared`

Mounted S3 buckets can be found at `/s3`.

Non-mounted S3 buckets can be access via the aws cli. To list available buckets: ``aws s3 ls``. Not all listed buckets can be used!


Logging in from Your Local Machine (Optional)
=============================================

After your first login via JupyterHub, you can set up direct SSH access to the PCluster from your local machine.

1. On your local machine, generate an SSH key pair: ``ssh-keygen -t ed25519 -f pcluster_key -N ""``

This will create a private and a public SSH key.

2. Open your public key (.pub file) and copy the contents to the PCluster:

- Start a JupyterHub server.
- Open a terminal and log into the PCluster: ``ssh pcluster``
- Navigate to your SSH directory: ``cd ~/.ssh``
- Edit the `authorized_keys` file: ``nano authorized_keys``
- Paste your public key on a new line, then save and exit (`Ctrl+S` followed by `Ctrl+X`).

3. Now you can SSH directly from your local machine:

.. code-block:: bash

     ssh -i <path-to-your-private-key> <username>@pcluster.airborne.smce.nasa.gov

This setup allows you to securely access the PCluster without having to go through JupyterHub each time.


Using the Pcluster
==================

Compute Options
---------------

The cluster has 4 different partitions for use:

- demand-8cpu
- demand-16cpu
- spot-8cpu
- spot-16cpu

The 8cpu instances have 16GB of memory and the 16cpu instances have 32GB.

The spot instances are much cheaper to use, but they can be interrupted by AWS and should be used for testing purposes only. The 
demand instances cost more, but will run as long as you need them to. 

You can list the different partitions by running: ``sinfo``

Using the Module System
-----------------------

To list the available modules you can run ``module avail``

To load modules run ``module load <module-name>``

Using Conda Environments on the Cluster
---------------------------------------

Your pcluster user has access to the same conda environments as the Jupyterhub. New environments can be
created through the Conda Store. **DO NOT CREATE CONDA ENVIRONMENTS ON THE HEAD NODE VIA CONDA CLI.**

To access your environments you must first load the miniconda module: ``module load miniconda3``.

If this is your first time using the miniconda3 module run: ``conda init``

If you have used the module before you can run ``source ~/.bashrc``

Now, everything is read for you to use conda!

Running a SLURM JOB
-------------------

There are many ways to use a SLURM-based cluster. Below are a few common usage patterns to help you get started. For more detailed and advanced workflows, refer to the official `SLURM documentation <https://slurm.schedmd.com/documentation.html>`_.

Submitting a Job Using ``sbatch``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Jobs are typically submitted to the scheduler using a batch script. Start by creating a script that specifies the required resources and the commands your job will execute.

.. code-block:: bash

     #!/bin/bash
     #SBATCH --partition=demand-8cpu # choose from the partitions above
     #SBATCH --ntasks=1 # run one task with using all compute resources
     #SBATCH --cpus-per-task=8 # use all available cpus
     #SBATCH --mem=15GB # The memory needs to be slightly lower than the node max
     #SBATCH --job-name=slurm_test
     #SBATCH --output=slurm-%j.out   
     #SBATCH --error=slurm-%j.err   

     # Loads modules
     module load miniconda3

     # configure conda
     conda init
     source ~/.bashrc
     
     # activate your conda env
     conda activate global-airborne-core
     
     # Run your python code
     python #<your script here>

Now that you created your script, submit it to run on the cluster using sbatch:

.. code-block:: bash
      
      sbatch <path-to-your-script>

Running an Interactive Job
^^^^^^^^^^^^^^^^^^^^^^^^^^

Another common and useful way to work on the cluster is by running an interactive job. Interactive jobs allow you to request compute resources and work directly on a compute node in real time.

To start an interactive job, you can specify all required compute resources directly in an ``srun`` command and include the ``--pty bash`` argument. This tells SLURM to allocate the requested resources and open an interactive shell on the compute node once the allocation is granted.

When the resources become available, you will be placed directly onto the allocated compute node and can begin working immediately.

.. code-block:: bash

     srun \
     --partition=demand-8cpu \
     --ntasks=1 \
     --cpus-per-task=8 \
     --mem=15G \
     --time=01:00:00 \ # 1 hour of time HH:MM:SS
     --job-name=slurm_test \
     --pty bash

Other Useful Commands
^^^^^^^^^^^^^^^^^^^^^

- ``squeue``  
  Shows the status of all jobs in the queue.

- ``squeue -u <username>``  
  Shows only jobs submitted by a specific user.

- ``scancel -j <job-id>``  
  Cancels a specific job.

- ``scancel -u <username>``  
  Cancels all jobs submitted by a specific user.

- ``sinfo``  
  Displays information about available partitions and node states.




.. ===============
.. Airborne SMCE Parallel Cluster
.. ===============

.. These work-in-progress instructions will be used to guide users how to utilize the Airbone PCluster resource (WIP)
.. **Note** These are very preliminary instructions that are actively being improved 

.. **To access the Airborne SMCE parallel cluster, message an admin and have them add you to the pcluster user group**


.. Logging In For the First Time
.. =============================

.. Once you have have been added to the pcluster group, your first login to the cluster must be from Jupyterhub.

.. 1.  Navigate to https://hub.airborne.smce.nasa.gov

.. 2. Select and start up a server

.. 3. During server startup your pcluster user will automatically be configured as well as the ssh configuration

.. 4. Once in JupyterLab, open a terminal and type `ssh pcluster` and you will be placed on the pcluster head node.

.. Your pcluster and Jupyterhub user are the same allowing to you read and write to files on both systems. The pcluster has a separate home directory,
.. but you can access your Jupyterhub home directory as well as the shared drive via /efs/home/<username> and /efs/shared. Mounted S3 buckets can be found at /s3

.. Logging In from your local machine
.. ==================================

.. Once you have logged in for the first time via the Jupyerhub, you can set it up so you can ssh directly into the pcluster from your local machine.

.. 1. Open a terminal on your local machine and run `ssh-keygen -t ed25519 -f <insert-path-here>/airborne-pcluster -N ""`

.. This will generate a private and public ssh key.

.. Next, you will take the contents of your public key file (.pub) and you need to put it in your authorized key file on the pcluster.
.. Start up a Jupyterhub server, open a terminal and use ssh pcluster to log into the pcluster.
.. Navigate to `~/.ssh`. Open your authorized_keys file `nano authorized_keys`. Copy and paste your public key that you generated on your local machine on a new line of the authorized key files. Save and exit `control s` followed by `control x`.

.. Now you can ssh into the pcluster from your local machine using `ssh -i <path to your private key> <username>@pcluster.airborne.smce.nasa.gov`











.. 3. once logged in, open a terminal in the Jupyter Hub environment and ssh into the pcluster head node `ssh [ip_address_of_pcluster_head_node]`. You should see a screen similar to below:

.. .. image:: ../images/pcluster/loginscreen.png

.. 4. Once logged in you will want to confirm that you have a `~/.ssh/authorized_keys` file in your home location. You will be adding external keys to this file from the machines you want to be able to connect from

.. 5. On you local computer, if you havent already created public/private ssh keys you will need to run `ssh-keygen -t ed25519 -C "your_email@example.com"` from your terminal, replacing the placeholder email with your email

.. 6. Once created, you will copy the public key from the file (assuming you didnt change the default name) `id_ed25519.pub`

.. 7. Now paste that key into your `~/.ssh/authorized_keys` file in your home directory on the Airborne PCluster head node

.. Now create a `~/.ssh/config` if you dont already have one on your local computer to store the information for logging into the head node and setup autocomplete

.. 1. `touch ~/.ssh/config`

.. 2. Edit the `~/.ssh/config` to add the following and save the edits

.. Host airborne-pcluster
..      User sserbin
..      HostName XX.XX.XXX.XX
..      IdentityFile ~/.ssh/id_ed25519
..      StrictHostKeyChecking no
..      UserKnownHostsFile=/dev/null

.. 3. For Mac OS `brew install bash-completion`

.. 4. Now edit your `~/.bash_profile` to include the following

.. `complete -W "$(echo `cat ~/.ssh/config | grep "^Host " |awk '{print $2}'`;)" ssh scp rsync`

.. 5. Open a new terminal tab and then try the autocomplete

.. `ssh airborne-pcluster`

.. 6. The above command should connect you to the pcluster if you have completed all of the previous steps


