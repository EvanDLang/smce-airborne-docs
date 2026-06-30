=========================
Using RStudio on the SMCE
=========================

Launching RStudio
-----------------
Click the **Launcher** tab and select **RStudio**. Currently running R 4.5.1 — contact an admin if you need an additional version.

Preventing Idle Shutdown
------------------------
JupyterLab cannot detect RStudio activity and will shut down your compute instance after 30–60 minutes of perceived inactivity. To prevent this, use the **jupyter-keepalive** extension.

Open the Command Palette via **View → Activate Command Palette** or ``Ctrl+Shift+C`` (``Cmd+Shift+C`` on Mac), search for "keepalive", and select your desired option. Enter the duration you want to keep your session alive.

Installing R Packages
---------------------
A number of geospatial packages are pre-installed. Additional packages can be installed via the terminal using ``install.packages()``.

.. note::
   Packages with system-level C/C++ dependencies require a Docker image update and cannot be self-installed. Contact an admin to request additional dependencies.

Please report any issues to an admin.