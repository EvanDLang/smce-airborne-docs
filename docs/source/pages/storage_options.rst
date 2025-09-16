===============
Storage Options
===============

Users currently have the following storage options:

Home Directory
--------------

    Appears as `/` in the file browser; full path is `/home/<username>`.
    This is regular file-system storage. It is private to your user, However storing large amounts of data can generate high storage costs
    so try to be mindful of how much storage you are useing. It is persistent across sessions,
    but we recommend making sure all important files are backed up.



EFS
---

    Appears as `shared` in the file browser (relative to the home directory); full path is `/home/<username>/shared`.
    The shared storage is split up based on the user groups you are apart of. I recommend using the `user` directory.
    the shared storage is regular file-system storage. I strongly recommended that you create user and/or sub-project-specific
    subdirectories here to keep things organized. This is technically unlimited,
    but is on a pay-for-what-you-use model, so please use responsibly. 
    It is more expensive and, usually, somewhat less performant than S3.


.. _s3_buckets:

S3 buckets
----------

    There are several S3 buckets associated with the Airborne Computing Environment

    * Airborne-smce-prod-user-bucket

        This is an S3 bucket set up for users to use as another storage option. It is a good place for storing larger data products.  

        This S3 bucket is mounted, and you can interact with it like a regular filesystem. However, because S3 buckets are not regular filesystems, this interface can behave strangely when writing files and can sometimes crash or disappear. If this happens, you can restart your Jupyter server, and it will remount the bucket.


