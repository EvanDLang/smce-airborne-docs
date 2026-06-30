================================
Virtual Environment Management
================================

Virtual environments allow you to install specific Python packages for your projects without conflicts. 
The SMCE supports multiple approaches depending on your workflow and experience level.

With the new EBS home directories, package installation from the command line is much faster than before. See the :doc:`storage` documentation for more details.

Conda Store (Recommended for Beginners)
========================================

Conda Store is a web-based GUI for managing virtual environments. It's ideal if you're new to Python package management or prefer visual tools.

**Best for:**

* Beginners and users who prefer GUI tools
* Creating Jupyter notebook kernels
* Sharing environments with your team
* Accessing pre-built environments

Accessing Conda Store
----------------------

1. Navigate to **File → Hub Control Panel** in JupyterHub
2. Click **Conda Store** in the navigation bar
3. Log in with your SMCE username and password

Creating a New Environment
---------------------------

1. Click **New Environment**

   * For personal use: create in your **personal namespace**
   * For team access: use the **global namespace** (requires admin)

2. Name your environment and add packages using the search interface

   * Conda Store automatically selects compatible Python versions
   * For specific versions, add Python as a package and select the version you need

3. **Important:** Add the ``ipykernel`` package to make your environment available as a Jupyter kernel

4. For Dask Gateway support, also add::

       dask=2025.3.0
       distributed=2025.3.0
       dask-gateway=2025.4.0
       ipywidgets

5. Click **Create** and wait for the build to complete

.. note::
   It may take a few minutes for new environments to appear in JupyterHub. Try stopping and restarting your server if it doesn't show up.

Editing and Deleting
--------------------

* Select your environment and click **Edit**
* Add or remove packages as needed
* To delete, click **Delete Environment** at the bottom
* Changes may take a minute or two to propagate

Rolling Back Changes
--------------------

If a package update breaks your environment, you can easily roll back:

1. Select your environment and click **Edit**
2. Use the **Builds** dropdown to select a previous build
3. Click **Save**

Pixi (Recommended for CLI and Projects)
========================================

Pixi is a fast, modern package manager that uses the conda ecosystem. It's ideal for command-line workflows and project-based development.

**Best for:**

* Git repositories and project-based work
* Fast package installation (seconds vs minutes)
* Reproducible environments shared via git
* CLI-based workflows

**Key features:**

* Uses conda-forge packages (compatible with conda)
* Creates lock files for exact reproducibility
* Project-based environments
* Works great with the new EBS home directories

Installation
------------

.. code-block:: bash

   # Install Pixi
   curl -fsSL https://pixi.sh/install.sh | bash

   # Restart your shell or run:
   source ~/.bashrc

   # Verify installation
   pixi --version

Setting Up a Project
--------------------

Pixi uses a ``pixi.toml`` file to define your project environment. Here's how to create and use it:

**Step 1: Initialize the project**

.. code-block:: bash

   mkdir geospatial-analysis
   cd geospatial-analysis
   pixi init

This creates a basic ``pixi.toml`` file.

**Step 2: Edit pixi.toml to add your packages**

Open ``pixi.toml`` and add your dependencies:

.. code-block:: toml

   [project]
   name = "geospatial-analysis"
   channels = ["conda-forge"]
   platforms = ["linux-64"]

   [dependencies]
   python = "3.11.*"
   numpy = ">=1.24"
   pandas = ">=2.0"
   geopandas = ">=0.14"
   rasterio = ">=1.3"
   matplotlib = ">=3.7"
   jupyter = ">=1.0"
   ipykernel = ">=6.0"

**Step 3: Install the environment**

.. code-block:: bash

   pixi install

This reads ``pixi.toml``, resolves dependencies, creates ``pixi.lock``, and installs packages into ``.pixi/``

**Step 4: Use the environment**

.. code-block:: bash

   # Run Jupyter
   pixi run jupyter lab

   # Run a Python script
   pixi run python analyze_data.py

   # Start a shell with the environment activated
   pixi shell

**For git repositories:**

* Commit ``pixi.toml`` and ``pixi.lock`` to git
* Add ``.pixi/`` to your ``.gitignore``

Collaborators can reproduce your exact environment:

.. code-block:: bash

   git clone <your-repo>
   cd <your-repo>
   pixi install  # Installs exact same packages from pixi.lock!
   pixi run jupyter lab

Using Pixi Environments as Jupyter Kernels
-------------------------------------------

To use your Pixi environment in JupyterLab, you need to register it as a kernel.

Make sure ``ipykernel`` is in your ``pixi.toml`` dependencies:

.. code-block:: toml

   [dependencies]
   python = "3.11.*"
   ipykernel = ">=6.0"
   # ... other packages

Then register the kernel:

.. code-block:: bash

   # From your project directory
   pixi shell
   
   # Inside the pixi environment, register as a kernel
   python -m ipykernel install --user --name my-project --display-name "My Project (Pixi)"
   
   # Exit the shell
   exit

The kernel will now appear in JupyterLab's kernel selector. You can select it when creating a new notebook or change an existing notebook's kernel by clicking the kernel name in the top-right corner.

Other Options
=============

Command-Line Conda/Mamba
-------------------------

You can create environments using ``conda`` or ``mamba`` from the command line. However, Pixi is generally faster and easier for project-based workflows.

Python venv
-----------

Python's built-in ``venv`` module works for Python-only projects but doesn't handle system dependencies or languages like R and Julia.

For most SMCE workflows, we recommend **Conda Store** (GUI) or **Pixi** (CLI).
