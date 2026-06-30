=========================
Compute Resources
=========================

The SMCE provides multiple compute options to support different research workflows. This guide will help you choose the right resources for your work.

JupyterHub Server Options
==========================

When starting a JupyterHub server, you can select from different images and instance types.

Available Images
----------------

**JupyterLab (Default)**

* Standard Python and scientific computing environment
* Includes VS Code extension for code editing

**JupyterLab-RStudio**

* Includes RStudio Server for R development
* See :doc:`rstudio` for detailed usage instructions

**JupyterLab-QGIS**

* Includes QGIS desktop via virtual desktop environment
* See :doc:`qgis` for detailed usage instructions

**JupyterLab-GPU**

* CUDA-enabled for GPU computing
* **Requires admin approval** - contact an administrator to request access

Instance Types
--------------

Choose an instance type based on your computational needs:

.. list-table:: Available Instance Types
   :header-rows: 1
   :widths: 20 15 15 20 30

   * - Instance Type
     - CPU
     - RAM
     - GPU
     - Availability
   * - Small
     - 4 cores
     - 16 GB
     - None
     - Spot & On-Demand
   * - Medium
     - 8 cores
     - 32 GB
     - None
     - Spot & On-Demand
   * - GPU
     - 4 cores
     - 16 GB
     - 16 GB GPU memory
     - Spot only

Spot vs On-Demand Instances
----------------------------

**Spot Instances** offer compute at up to 70-90% lower cost than on-demand pricing, but can be interrupted with little 
notice when capacity is needed elsewhere. Availability varies — some days you'll see no interruptions, other days they may happen every 10 minutes.

.. note::
   The Airborne SMCE has background processes to handle spot interruptions gracefully. If you're kicked off your spot instance, 
   a new one will be ready. If you restart your hub and select the same instance type, you'll be dropped back into your environment with a much shorter spool-up time.

**On-Demand Instances** cost more but guarantee your instance stays running until you stop it.

* Predictable pricing, no interruptions
* The cost is reasonable — don't be afraid to use on-demand when you need it

**Which should you use?**

* Use **spot** for: testing, resumable tasks, and quick tasks — anything that can be interrupted without causing a headache
* Use **on-demand** for: longer-running tasks or anything where an interruption would be annoying

.. tip::
   Keep getting kicked out of your spot instance? Switch to On-Demand instances.

Monitoring Memory Usage
--------------------------

You can monitor your memory usage in JupyterLab by checking the **status bar at the bottom** of the interface. It displays current RAM usage.

If you're running out of memory, consider:

* Clearing the kernel on unused notebooks
* Clearing large variables
* Switching to a larger instance type
* Using Dask Gateway for distributed computing

Best Practices
--------------

**Start small, scale up as needed**

* Begin with the 4 CPU / 16 GB instance
* Monitor your resource usage
* Upgrade to 8 CPU / 32 GB only if you need more memory or CPU power

**Stop your server when not in use**

* Saves resources for other users
* Go to **File → Hub Control Panel → Stop My Server**

**Choose the right image**

* Use the default JupyterLab image for most Python work
* Only select specialized images (RStudio, QGIS, GPU) when you need those specific tools

Additional Compute Options
===========================

For workloads that require more compute power than a single JupyterHub server can provide, the SMCE offers two additional options:

Dask Gateway
------------

Dask Gateway allows you to spin up additional worker nodes for parallel Python computing.

**Best for:**

* Large array operations (xarray, NumPy)
* Parallel data processing (pandas, dask.dataframe)
* Processing data that doesn't fit in memory

**Requirements:**

* Requires membership in the ``developers`` group. Run ``id`` in a terminal to verify; contact an admin if you need to be added.
* Specific packages must be installed in your environment

See :doc:`dask_gateway` for detailed setup and usage instructions.

Parallel Cluster (SLURM)
-------------------------

The SMCE Parallel Cluster is a SLURM-based HPC cluster for traditional batch computing.

**Best for:**

* Long-running batch jobs
* MPI-based parallel computing
* Non-interactive workflows
* Command-line tools and processing pipelines

**Requirements:**

* Requires admin approval - contact an administrator to be added to the ``pcluster`` group
* Access via SSH from JupyterHub or your local machine

See :doc:`pcluster` for detailed setup and usage instructions.