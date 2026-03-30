===========================
Uploading Files to the SMCE
===========================

Airborne S3 Web Tools
=====================

The Airborne SMCE S3 File Manager is a browser-based tool for uploading files to
Airborne SMCE S3 buckets. Access it at https://upload.airborne.smce.nasa.gov.
For large directory uploads, scripted workflows, or transfers over unreliable
connections, use the CLI tool instead: `airborne-sync <https://github.com/EvanDLang/airborne-sync>`_.

Authentication
--------------

When you first open the UI you will be redirected to the Airborne Hub Keycloak
login page. Sign in with your NASA credentials.

After a successful login you are returned to the file manager. Your Keycloak
group memberships are displayed as chips in the top navigation bar (e.g.
``gliht``). These groups determine which S3 buckets you can access.
The status bar below the navigation bar will show **CREDENTIALS ACTIVE** and the
number of buckets available to you once your session credentials have been issued.

Selecting a Bucket
------------------

Use the **Bucket** dropdown to choose the S3 bucket you want to upload into. The
dropdown is populated automatically from the buckets your Keycloak groups permit
you to access — you will not see buckets you do not have permission to use.

Navigating Directories
----------------------

After selecting a bucket, the objects table lists the folders and files at the
root of that bucket.

* **Enter a folder** — click any row with a ``DIR`` badge to navigate into it.
* **Go back up a level** — click any segment of the breadcrumb path displayed
  below the bucket selector. Clicking the bucket name itself returns you to the
  root.

The current path is always shown in the breadcrumb trail and also reflected in
the upload destination label (``Upload Files → s3://<bucket>/<path>/``), so you
always know exactly where files will land before you upload them.

Uploading Files
---------------

1. Navigate to the destination folder as described above.
2. Click the **upload drop zone** (the dashed box labelled *CLICK TO SELECT
   FILES*) to open a file picker. You can select one or multiple files at once.
3. The selected files appear in the **Upload Queue**. Each entry shows the
   destination filename, file size, and current status. You can edit the
   destination filename in the text field for any queued file before uploading.
4. Click **Upload All** to start the uploads. Files are uploaded sequentially.

While an upload is in progress you can see:

* A progress bar with percentage and bytes transferred.
* Current transfer speed (e.g. ``12.4 MB/s``).
* Elapsed time and estimated time remaining.
* A cancel button to abort that individual upload.

Each file in the queue shows one of the following status badges:

.. list-table::
   :widths: 15 85
   :header-rows: 1

   * - Status
     - Meaning
   * - ``QUEUED``
     - Waiting to start.
   * - ``UPLOADING``
     - Transfer in progress.
   * - ``DONE``
     - Upload completed successfully.
   * - ``FAILED``
     - Upload encountered an error.
   * - ``CANCELLED``
     - Upload was manually cancelled.

Once all uploads are complete, click **Clear** to reset the queue and select new
files.

Transfer Speed and the NASA VPN
--------------------------------

Uploads go directly from your browser to S3 — there is no intermediate server.
Transfer speed is therefore determined entirely by your local network connection.

.. warning::

   If you are connected to the NASA VPN, upload speeds will be significantly
   slower. All traffic is routed through VPN gateways, which adds latency and
   reduces throughput. For large or long-running uploads, disconnect from the
   VPN if your work permits it, or use the CLI tool instead.

Limitations
-----------

* **Large files** — uploads use the S3 multipart API with 50 MB parts, so files
  of any size are supported without exhausting browser memory. That said, very
  large files over an unstable connection are better handled by the CLI tool,
  which can resume interrupted transfers.
* **No directory upload** — files must be selected individually. The CLI tool
  supports syncing entire directories.
* **Sequential uploads** — files in the queue are uploaded one at a time.
  Parallel uploads are intentionally not supported in the browser to avoid
  exhausting memory when dealing with large files. For parallel, multi-file,
  or directory uploads use the CLI tool:
  `airborne-sync <https://github.com/EvanDLang/airborne-sync>`_.

