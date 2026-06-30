================
Storage Overview
================

The SMCE storage infrastructure is being upgraded to improve I/O performance and cost efficiency. We are migrating from Amazon Elastic File System (EFS) to a hybrid storage solution:

* **Home Directories**: 30GB Amazon EBS volumes per user
* **Shared Storage**: 1.2 TB Amazon FSx for Lustre with virtually unlimited storage via mounted S3 buckets

This new architecture provides faster local storage for home directories and high-performance shared storage with seamless S3 integration.

Home Directory 
==============

Home directories will have a dedicated EBS volume for their home directory. This provides improved I/O performance compared to the previous EFS setup.

**Storage Limit:** 30GB per user

**Benefits:**

* **Better Performance**: Reduced latency for local file operations
* **Faster Virtual Environment Installation**: Install Python, R, and Julia packages from the command line much more quickly
* **Multiple Environment Managers**: Improved support for tools like Pixi, virtualenv, and others

The Conda Store remains available and is still a recommended option for standardized environments. However, the new EBS volumes make it more practical to use alternative virtual environment managers or install custom packages as needed.

**Best Practices:**

The following are good examples of how to best use your home directory storage:

* Virtual environments (Conda, venv, Pixi, etc.)
* Personal scripts and code under active development
* Jupyter notebooks you're currently working on
* Configuration files and dotfiles
* Small, frequently accessed files


Shared Storage
==============

The shared file storage is migrating from EFS to FSx for Lustre. FSx for Lustre is a high-performance parallel filesystem optimized for:

* Large-scale data processing workloads
* Machine learning training pipelines
* High-throughput data analysis
* POSIX-compliant file operations while automatically managing data movement between local storage and S3.
* Seamless S3 integration with local caching

The Lustre filesystem is split into two types of storage, local and S3 based. The local storage, represents files that
are stored directly on the filesystem. The S3 based storage or data repository associations (DRAS) dynamically pull data from S3 buckets and cache it temporarily (4 hours) 
on the Lustre filesystem providing high performance reads without using the S3 API.

Shared FSx Storage (``/shared/users-local/<username>``)
--------------------------------------------------------

**Storage Limit:** ~1.2TB shared across all users

**Benefits:**

* Actual FSx for Lustre filesystem space - data stored directly on high-performance parallel filesystem
* Fast I/O operations ideal for active workloads
* Shared access allows collaboration between users
* Good for files that need to be accessed repeatedly during active projects

**Best Practices:**

The following are good examples of how to best use the users-local storage:

* Production scripts and code shared with collaborators
* Analysis pipelines and workflows
* Intermediate results from current analyses
* Frequently accessed reference files
* Code repositories being actively used by teams


S3-Backed Storage (``/shared/users-bucket`` and other S3 buckets)
------------------------------------------------------------------

**Storage Limit:** Virtually unlimited

**Benefits:**

* Files written directly to S3 but accessible via standard filesystem operations
* Cost-effective for large datasets
* Transparent caching: files read from S3 are cached on FSx for 4 hours for improved performance
* Files automatically stored in S3 for durability and backup
* Additional project-specific S3 buckets will be mounted as needed

**Best Practices:**

The following are good examples of how to best use the users-bucket storage:

* Large datasets (raw data, NetCDF files, HDF5 files, satellite imagery, etc.)
* Model output files
* Archived results and processed data
* Long-term data storage
* Infrequently modified files
* Any data that exceeds the capacity of local or shared FSx storage

Project Specific Buckets
------------------------

* Project-specific S3 buckets are mounted on a per-user basis
* Users must be added to the corresponding project group by an admin to gain access
* Once added to a group, the project bucket will be accessible in your filesystem
* Provides dedicated storage for collaborative research projects with controlled access

Quick Reference
===============

.. list-table:: Storage Location Quick Reference
   :header-rows: 1
   :widths: 20 25 25 15 15

   * - Location
     - Best For
     - Examples
     - Size
   * - ``/home/jovyan``
     - Virtual environments, active development
     - Pixi environments, notebooks, scripts
     - 30GB
   * - ``/shared/users-local/<username>``
     - Shared code, intermediate results
     - Git repos, small data products, temp files
     - 1.2TB (shared)
   * - ``/shared/users-bucket/<username>``
     - Large data, long-term storage
     - Flightlines, archives, large model outputs
     - Unlimited

Migrating Your Files
====================

The old EFS filesystem will be temporarily mounted at ``/efs`` to allow you to migrate your data.

Migration Locations
-------------------

**Old Locations:**

* Home directories: ``/efs/home/<username>``
* Shared space: ``/efs/shared/<group>/<username>``

**New Locations:**

* Home directories: ``/home/jovyan`` (30GB EBS volume)
* Shared local storage: ``/shared/users-local/<username>``
* S3-backed storage: ``/shared/users-bucket/<username>``

Migration Strategy
------------------

**Important:** Your new home directory is only 30GB, compared to the previous EFS setup. You should **not** copy everything from your old home directory. Instead, migrate selectively based on file type and usage.

**What to move to each location:**

* **Home directory** (``/home/jovyan``): Active notebooks, scripts you're currently developing, small working files, virtual environments (or reinstall fresh)
* **Shared local storage** (``/shared/users-local/<username>``): Scripts, codebases, collaborative projects, small datasets and data products
* **S3-backed storage** (``/shared/users-bucket/<username>``): Large datasets, raw data, model outputs, archived results, long-term storage

Migration Instructions
----------------------

Use ``rsync`` to copy files from the old EFS locations to your new storage. Rsync is recommended because it preserves file permissions, allows resume if interrupted, and shows progress.

**Basic rsync syntax:**

.. code-block:: bash

   rsync -avhP <source-directory> <destination-directory>

* ``-a``: Archive mode (preserves permissions, timestamps, symbolic links)
* ``-v``: Verbose output
* ``-h``: Human-readable file sizes
* ``-P``: Shows progress and allows partial file resume


**Step 1: Organize files in your old EFS home directory**

.. code-block:: bash

   # Create staging directories
   mkdir -p /efs/home/<username>/to-migrate-home
   mkdir -p /efs/home/<username>/to-migrate-local
   mkdir -p /efs/home/<username>/to-migrate-bucket
   
   # Move files to appropriate staging directories
   # For home directory (small, active files)
   mv /efs/home/<username>/my-notebooks /efs/home/<username>/to-migrate-home/
   mv /efs/home/<username>/current-project /efs/home/<username>/to-migrate-home/
   
   # For shared local (scripts, code, small data)
   mv /efs/home/<username>/scripts /efs/home/<username>/to-migrate-local/
   mv /efs/home/<username>/analysis-code /efs/home/<username>/to-migrate-local/
   
   # For S3 bucket (large datasets)
   mv /efs/home/<username>/data /efs/home/<username>/to-migrate-bucket/
   mv /efs/home/<username>/model-outputs /efs/home/<username>/to-migrate-bucket/

**Step 2: Migrate with rsync**

.. code-block:: bash

   # Migrate to new home directory
   rsync -avhP /efs/home/<username>/to-migrate-home/ /home/jovyan/
   
   # Migrate to shared local storage
   rsync -avhP /efs/home/<username>/to-migrate-local/ /shared/users-local/<username>/
   
   # Migrate to S3-backed storage
   rsync -avhP /efs/home/<username>/to-migrate-bucket/ /shared/users-bucket/<username>/


Migration Tips
--------------

* **Assess your storage usage first**: Use ``du -sh /efs/home/<username>/*`` to see which directories are largest
* **Be selective**: Your new home directory is only 30GB - don't copy everything
* **Large files belong in S3**: Any datasets over a few GB should go to ``/shared/users-bucket``
* **Consider reinstalling environments**: Instead of copying old conda/pip environments, reinstall them fresh to take advantage of the faster EBS storage
* **Verify after migration**: After copying, check that files transferred successfully before deleting from EFS
* **Clean as you go**: This is a good opportunity to delete old files you no longer need
