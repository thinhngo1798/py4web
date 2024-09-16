# Skill Pixel
This document provides instructions to deploy the project to Google Cloud Platform from your local machine.

    SkillPixel
    |__ myvenv
    |__ notebook-platform
    |   |__ apps
    |       |__ notebookgrader-app
    |           |__ requirements.txt
    |
    |__ py4web
        |__ apps
        |   |__ notebookgrader-app          (symlink)
        |
        |__ deployment_tools
        |   |__ gae   
        |       |__ apps       
        |       |   |__ .service
        |       |   |__ _default            (symlink)
        |       |   |__ notebookgrader-app  (symlink)
        |       |
        |       |__ lib
        |       |__ app.yaml
        |       |__ index.yaml
        |       |__ main.py
        |       |__ Makefile
        |       |__ requirements.in
        |       |__ requirements.txt
        |
        |__ requirements.txt

## Table of Contents

1. [Set up Google Cloud SDK](#set-up-google-cloud-sdk)
2. [Set up Dependencies](#set-up-dependencies)
3. [Deploy the Project](#deploy-the-project)

## Set up Google Cloud SDK
Install the Google Cloud SDK by following the instructions [here](https://cloud.google.com/sdk/docs/install).

After installation, initialise the Google Cloud CLI from the Command Prompt:

``` bash
gcloud init
```

Follow the prompts for the active configuration `default`:
* Sign into your Google Account
* Select the Skill Pixel project
* Set the default region and zone (recommended: `asia-southeast1`)

For development purposes, set the `promote` flag as follows to automatically redirect traffic to the newly deployed version.

``` bash
gcloud config set app/promote_by_default True
```

## Set up Dependencies
Navigate to `py4web/deployment_tools/gae`. At the start, the `apps` and `lib` folders may not exist.

### 1. Set up `apps` folder
Run the following commands:

``` bash
mkdir apps
touch apps/__init__.py

# Symlink apps to deploy to GAE
cd apps

# On MacOS:
ln -s ../../../apps/notebookgrader-app _default
ln -s ../../../apps/notebookgrader-app .

# On Windows:
mklink /D _default ..\..\..\apps\notebookgrader-app
mklink /D notebookgrader-app ..\..\..\apps\notebookgrader-app
```

The `gae/apps/.service` folder will be automatically created when the application is built.

### 2. Set up `lib` folder
Double-check the `Makefile` for the following lines:

```
cat ...
cat ./apps/notebookgrader-app/requirements.txt >> requirements.txt

echo >> requirements.txt
echo ...
```

Run the `Makefile`:

``` bash
make setup
```

**Note**: The `make` command may not be available by default.
* On MacOS: Install **Xcode Command Line Tools**
* On Windows: Install **WSL/MSYS2/Cygwin/MinGW** then install the command

The `gae/lib` folder should now be created, along with `gae/requirements.txt` listing all necessary dependencies:

``` python
wheel
ombott==2.0
click
colorama
cryptography
portalocker
renoir >= 1.4.0
requests
threadsafevariable >= 20230507.1
pyjwt >= 2.0.1
pycryptodome
pluralize >= 20240515.1
rocket3 >= 20230507.1
yatl >= 20230507.3
pydal >= 20240601.3
watchgod >= 0.6

# optional modules:

### for documentation only ###
# use also docs/requirements.txt
google-api-python-client
google-auth
google-auth-oauthlib
py4web
nqgcs
google-cloud-tasks
nbformat
mysqlclient
```

## Deploy the Project
While in `py4web/deployment_tools/gae`, deploy the code to Google Cloud Platform using the command:

``` bash
# To deploy the app
gcloud app deploy

# To visit the deployed app
gcloud app browse

# To check logs for errors/bugs
# `default` is the active configuration
gcloud app logs tail -s default
```

**Best Practice**: 
* Develop and test features locally on the `develop` branch
* Only deploy to GCP when necessary on the `main` branch