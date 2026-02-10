# smce-airborne-docs

This repository serves as technical documentation for accessing and interfacing with the [GSFC 618 Airborne SMCE service](https://airborne-smce.readthedocs.io/en/latest/index.html).

## Contributing to the 618 Documentation
To contribute to this documentation you may join the repository as a collaborator by cloning or forking the repository, 
working on a development/non-main branch, and creating a pull request back to this repository. The documentation uses 
Sphinx and is written in reStructuredText format (.rst files). 

Once you have added new content, you should test the stability of the build locally using the following:

```bash
cd docs/
make clean

# you should see detailed errors in the build message if present
make html
python -m http.server --directory build

# Alternative, if the make approach does not work try:
sphinx-build -b html source build
python -m http.server --directory build

# Navigate to http://[::]:8000 or the supplied web browser link
```