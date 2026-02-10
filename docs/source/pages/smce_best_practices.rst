SMCE Resources & Best Practices
===============================

AWS Resources available on the SMCE
-----------------------------------

There are two main types of cloud resources you will encounter in the Airborne SMCE: Compute and Storage. Users
currently have the following resource options available:

Compute Resources
-----------------

    There are two types of compute resources made available by default: SPOT and On-Demand instances. On-Demand instances
    are more stable and are recommended for active development. Choose the size most appropriate for your work (e.g.,
    16GB or 32GB or number of cpus/gpus). **Launching a compute instance opens a containerized notebook service on EC2
    instances with two primary types of storage: EFS and S3**

    At startup, you will see a progress bar launching your server which will open a JupyterHub session. If you are
    unfamiliar with Jupyter, it will be worth your time to familiarize yourself (`tutorial
    <https://realpython.com/using-jupyterlab/>`_). From the JupuyterHub you can load available conda environments
    (``conda env list``) in a terminal, run scripts, create Jupyter notebooks treating the JupyterHub as an IDE,
    navigate using the file browser, and generally use your terminal as you would with any Unix bash system, for example.

EFS (aka your scratch file system)
----------------------------------

    One of two storage solutions you will use on the SMCE: Elastic File Storage. **This is the primary location to develop
    code, save intermediate or frequent/small i/o products, stage data to move to longer storage in s3.**

    Appears as ``\/`` in the file browser (i.e., the home directory); full path is ``/home/jovyan/`` when referencing.
    Within the EFS the ``/home/shared/`` storage is split up based on the user groups you are part of. We recommend using
    the ``users`` subdirectory in ``/home/jovyan/shared/``. The shared storage is regular file-system storage. We
    strongly recommended that you create user and/or sub-project-specific subdirectories in the shared directory. Files
    you write in the shared space will have read-only access for other SMCE users unless you change permissions.

    This storage is technically unlimited, but is on a pay-for-what-you-use model, so please use responsibly. It is more
    expensive and, usually, somewhat less performant than S3 for long-term storage. See Best Practices for specifics.


S3 buckets (aka your longterm or large file storage system)
-----------------------------------------------------------

    The other storage option you will interact with are S3 buckets (simple storage service). In general, S3 buckets are
    best for less frequently accessed data (e.g., 1x/day or less). There are two types of S3 resources mounted buckets
    and non-mounted buckets. The mounted buckets can be treated like a standard file system using command line arguments
    (e.g., ``ls``, ``cd``, etc) or AWS API calls. Available mounted buckets can be viewed using ``mount | grep "s3"``;
    however, if reading data in a script, using the AWS api libraries is recommended for speed/stability if you encounter
    issues. You will notice these buckets are accessible in your JupyterHub file browser under ``~/s3/``. Non-mounted
    buckets can only be accessed via AWS API calls, including command line (aws-cli) and python (boto3, s3fs) among others.

    **Notes:**
    - Because S3 buckets are not regular filesystems, they are object stores, mounted bucket interfaces can
    behave strangely when writing files and can sometimes crash or disappear. If this happens, you can restart your
    server, and it will remount the bucket during restart.
    - S3 buckets do not have file permissions like a traditional file system.

    There are several S3 buckets associated (i.e., mounted) with the Airborne SMCE:

    * Airborne-smce-prod-user-bucket

        This is an S3 bucket set up for all SMCE users to use as another, long-term or infrequent use storage option.
        It is a good place for storing larger or final data products.



Best Practices
--------------

    The following are general recommendations to reduce headaches and help problem solve so more time can be spent on
    science.

    1. Ask SMCE admins to be added to the 618 NASA slack for better access to assistance.

    2. Use the following aws-cli commands in the terminal to navigate s3 buckets (not commands like ``mv``, ``cp``, etc):
    ::

        $ aws s3 ls # lists all s3 buckets associated with your account space
        $ aws s3 ls <s3://your-bucket/> --recursive --human-readable --summarize # a catch all to print s3 index contents similar to a file system structure

    3. If your files are approx. <50 GB, storing in EFS versus S3 storage is not very important. In the long term (months) try to remove or move files that are less frequently accessed to S3. This will lower the cost for storage as we add TBs of data.

    4. Download files from URLs to your EFS and use ``aws cp`` or the ``boto3 s3fs`` libraries in python to move data to s3 when ready. You can attempt direct to S3 but fidelity may be poor.

    5. File storage in the EFS file system should consist of:

        - Files with frequent (>1x/day) read/write (e.g., scripts, notebooks, temp data, intermediates).
        - Files you want to backup on github (e.g., using api calls).
        - Conda or related environment files.

    6. To upload local data to the smce s3 buckets from your machine or an HPC the simplest method is to use the AWS Command Line Interface (aws-cli) which handles calls to the AWS API, allowing for actions such as copying data to s3 buckets.

        - install the aws-cli with a python package manager (e.g., ``conda install -c conda-forge aws-cli``).
        - You will need to first know if you have privileges to write to the target s3 bucket.
        - If you do have permission, you may use the :doc:`s3_upload` tool for any number of files that are individually <5GB. This will upload your files to the airborne-smce-prod-user-bucket which as an SMCE user you have read/write access to. **Note: contact an SMCE admin to use the s3 upload tool**
        - If you need to upload large amounts of data, totals >100sGB contact an SMCE admin for help.
        - If you are uploading from the command line, use the ``us-west2`` zone for upload to the airborne-smce-prod-user-bucket.
        - For individual files, use the upload GUI button on your JupyterHub (uploads will be in the space currently in view in the file browser).

    7. If you are unsure what type of storage a directory/path is, use ``df -h <path>`` and look for "efs" or "s3" in the path names.
