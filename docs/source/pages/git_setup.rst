===========================================
Git and GitHub setup
===========================================

Authenticating your GitHub account: Personal Access Tokens
----------------------------------------------------------

Github personal access tokens (PATs) enable you to authenticate with Github from a local coding environment without needing to provide your username and password for each GitHub related action (e.g., pushing to Github).

There are various ways you can authenticate and develop github code and security access on the SMCE, but one suggested approach is outlined below using fine-grained PATs. Another common alternative is to use SSH keys. We implement fine-grained PATs for SMCE use as they allow for more control over permissions features such as read/write to repos and access to the GitHub API.

1. Create a fine-grained PAT for use with the GitHub command line interface (CLI)

   - Visit the `PAT section of your GitHub account <https://github.com/settings/personal-access-tokens>`_
   - Select your profile thumbnail > "Settings" > scroll on the left hand side to "< > Developer Settings"
   - Expand "Personal access tokens" on the left hand side and select "Fine-grained tokens"
   - Select "Generate new token"
     - name your token, set expiration date (recommended 6 month maximum)
     - the easiest method (among many) is to select "Only select repositories" under "Repository access" and add the repos you want to access on the SMCE
     - for now, once you have added at least one repository, click the "+ Add permissions" under the Permissions header, scroll down and select "Contents" and change the "Access: Read-only" option to "Read and write". This will enable push/pull functionality. If you would like to expand/reduce permissions in the future, you can update them here.

   **NOTE: before clicking generate token make sure you are ready to use the token at the SMCE command line by following steps 2-5 below.**

2. Create a new hidden directory on the SMCE that will contain your token(s). This is in your $HOME (~) directory.

   .. code-block:: bash

       mkdir -p ~/.github_tokens

3. Make a new file that contains your new token, e.g.

   .. code-block:: bash

       touch ~/.github_tokens/airborne_smce_general.txt

4. Paste your new token into this file and save the file.

5. Run ``gh auth`` to register your credentials and run through the prompts on the command line, e.g.:

   .. code-block:: bash

       gh auth login --with-token < ~/.github_tokens/airborne_smce_general.txt
       ? Where do you use GitHub? GitHub.com
       ? What is your preferred protocol for Git operations on this host? HTTPS
       ? Authenticate Git with your GitHub credentials? Yes
       ? How would you like to authenticate GitHub CLI? Login with a web browser

6. Try git cloning a GitHub repo to a directory on the SMCE, e.g.:

   .. code-block:: bash

       gh repo clone GSFC-618/airborne-smce-environments

Additional documentation:

- `GH auth login <https://cli.github.com/manual/gh_auth_login>`_
- `Managing GH tokens <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>`_

----

Setting up Global Git Variables
-------------------------------

1. Setup your global environment variables

   **Your github username, e.g.**

   .. code-block:: bash

       git config --global user.name "RobertGoddard"

   **Your email, e.g.**

   .. code-block:: bash

       git config --global user.email robertgoddard@example.com

2. Review your settings

   .. code-block:: bash

       git config --list

   e.g.

   .. code-block:: bash

       rgoddard@jupyter-rgoddard:~/Github$ git config --list
       rgoddard@jupyter-rgoddard:~/Github$ user.name=RobertGoddard
       rgoddard@jupyter-rgoddard:~/Github$ user.email=robbertgoddard@nasa.gov

----

How to add work to a GitHub repo
--------------------------------
Overview of basic workflow:

    .. code-block:: bash

        git repo clone GSFC-618/airborne-smce-environments
        git checkout -b example
        # WORK
        git add <your updated files>
        git commit -m "<A short message about your changes.>"
        # Regularly or before PR: checkout to main branch, update, checkout to example branch, merge
        git push origin example

An example to begin working with the GSFC 618 GitHub repositories on the SMCE including cloning a GitHub repo, adding a file with text, committing, branching, and pushing to the GitHub repo. This workflow is the most common string of commands to ensure your git repo is up to date with the GitHub repo, add your own work, and update the remote GitHub repo.

1. Clone this GitHub repository (assuming it does not already exist locally):

   .. code-block:: bash

       user@jupyter-user:~$ gh repo clone GSFC-618/airborne-smce-environments
       user@jupyter-user:~$ cd airborne-smce-environments
       user@jupyter-user:~$ git status
       On branch main
       Your branch is up to date with 'origin/main'.

       nothing to commit, working tree clean

2. Branch, add a file, commit.

   .. code-block:: bash

       user@jupyter-user:~$ git checkout -b example
       Switched to a new branch 'example'
       user@jupyter-user:~$ git branch -a
         main
       * example
         remotes/origin/HEAD -> origin/main
         remotes/origin/main
       user@jupyter-user:~$ touch example.txt
       user@jupyter-user:~$ echo "Welcome to the airborne SMCE" > example.txt
       user@jupyter-user:~$ git status
       On branch example
       Untracked files:
         (use "git add <file>..." to include in what will be committed)
               example.txt

       nothing added to commit but untracked files present (use "git add" to track)
       user@jupyter-user:~$ git add example.txt
       user@jupyter-user:~$ git commit -m "First example commit message"
       [example 7cdc9cf] First example commit message
        1 file changed, 1 insertion(+)
        create mode 100644 example.txt

   Now you have successfully added your own work to a local git repo - it is not connected to GitHub at this point. If familiar, you can alternatively stash your changes instead of committing if needed.
   Notes:

   - git checkout -b <branch_name> both creates a new branch and moves you to that branch.
   - Use git checkout <branch_name> after that branch exists

3. Ensuring your ``example`` branch is up to date with GitHub's ``main``:

   .. code-block:: bash

       user@jupyter-user:~$ git checkout main
       user@jupyter-user:~$ git pull origin main
       user@jupyter-user:~$ git checkout example
       user@jupyter-user:~$ git merge main

   At this point your working branch ``example`` has 1) your committed work and 2) is up to date with the Github version of ``main``.

4. Push to GitHub

   .. code-block:: bash

       user@jupyter-user:~$ git push origin example
       Enumerating objects: 1, done.
       Counting objects: 100% (1/1), done.
       Delta compression using up to 4 threads
       Compressing objects: 100% (1/1), done.
       Writing objects: 100% (1/1), 344 bytes | 57.00 KiB/s, done.
       Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
       remote:
       remote: Create a pull request for 'example' on GitHub by visiting:
       remote:      https://github.com/GSFC-618/airborne-smce-environments/pull/new/example
       remote:
       To https://github.com/GSFC-618/airborne-smce-environments.git
        * [new branch]      example -> example

5. Now your local branch is available online at GitHub. At this point you can follow the supplied pull request link to move your work into main if your update is complete. It will be reviewed by another developer and may even need to pass certain deployment tests to successfully merge into main. If you are no longer planning to develop on the ``example`` branch it is good practice to delete the branch AFTER a successful pull request.


----

Working with a forked GitHub repository
---------------------------------------
If you are not able to write to a GitHub repository because you do not have permissions you will need to fork the repository. The steps are similar to the above example:

1. Fork the GitHub repository so it exists in your personal GitHub

2. Clone your personal fork to a your workspace.

3. Branch, make changes, add and commit changes.

4. Push commits to your GitHub fork.

5. Select "Pull Request" from your push or on your GitHub forked repo. This will connect to the original owner's repository.

----

Git and GitHub terminology
--------------------------
.. list-table:: Git Terminology: ``git`` included to differentiate commands.
   :widths: 20 80
   :header-rows: 1

   * - Term
     - Definition
   * - Repository
     - A storage location for a project containing all files and revision history. Also referred to as repo.
   * - Remote
     - A version of the repository hosted on GitHub or a server.
   * - Clone
     - Creating a local copy of a remote repository.
   * - ``git commit``
     - A snapshot of changes saved to the repository history.
   * - ``git stash``
     - Shelves changes you've made before a commit if you want to "pause" your update. Use ``git stash pop`` to unshelve your changes.
   * - Branch
     - A parallel version of the repository that allows independent development
   * - Main
     - The default primary branch of a repository, typically used as the deployed version of a repo.
   * - ``git merge``
     - Combine changes from different branches.
   * - ``git fetch``
     - Download any changes from a remote repo, but does not merge. Will update the remote local branch (e.g., origin/main).
   * - ``git pull``
     - Fetch and merge changes from a remote repository.
   * - ``git push``
     - Sends committed changes to a remote repository.
   * - Fork
     - A personal copy of someone else's repository. Has a different process for ensuring repo is up to date with the owner's.
   * - Pull Request
     - A method to submit contributions to a repository also called a PR.
   * - Upstream
     - The original repository from which a fork was created.
   * - Staging Area
     - Where changes are prepared before committing.
   * - HEAD
     - A pointer to the most recent commit on the current branch.
   * - Merge Conflict
     - When Git can't automatically resolve differences between branches. This requires additional attention to resolve conflicts.
