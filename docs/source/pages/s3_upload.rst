===========================
Uploading Files to the SMCE
===========================

**To gain access to the S3 upload tool, contact an administrator and ask to be added to the ``s3-upload`` group.**

Uploading files to the SMCE from your local machine can be challenging. While JupyterHub provides a built-in upload button, it is not well suited 
for large files and may be slow or fail to complete the upload.

To address this, we provide a dedicated upload application that allows users to upload files directly to the SMCE user bucket. 
The application works by submitting a list of filenames to the service, which then generates pre-signed URLs. These URLs are used to upload files directly
to S3 using HTTP ``PUT`` requests, bypassing JupyterHub entirely.

**At this time, each individual file must be 5 GB or smaller.**  
If you need to upload larger files, please contact an administrator for assistance.

The following script can be used to upload files from your local machine.

To run the script, you must provide:

- Your Keycloak username and password
- The local path to the file or directory you would like to upload  
  (see examples included in the script)

Optional:

- A bucket sub-path indicating where the files should be placed within your user bucket:  
  ``/s3/airborne-smce-prod-user-bucket/<username>/<provided-bucket-path>``

.. code-block:: python

    import requests
    import os
    from time import time

    # Do not change
    API_URL = "https://upload.airborne.smce.nasa.gov/s3_upload"
    KEYCLOAK_HOST = "https://auth.airborne.smce.nasa.gov"
    REALM = "airborne-smce"
    CLIENT_ID = "s3_upload"
    
    # Input username and password
    USERNAME = ""
    PASSWORD = ""

    # Local path to file or files
    LOCAL_PATH = '.'
    
    # Path in the S3 bucket where files will be uploaded.
    # Your username will automatically be prepended.
    # Example: BUCKET_PATH = 'sub_dir' → uploaded to <your_username>/sub_dir
    BUCKET_PATH = ''

    # -------------------------------
    # Selecting files to upload
    # -------------------------------

    # Example 1: Upload the entire directory
    # This will include all files in LOCAL_PATH
    LOCAL_PATHS = [os.path.join(LOCAL_PATH, f) for f in os.listdir(LOCAL_PATH)]
    FILE_LIST = [os.path.join(BUCKET_PATH, f) for f in os.listdir(LOCAL_PATH)]

    # Example 2: Upload a single file
    # FILE_NAME = ""
    # LOCAL_PATHS = [os.path.join(LOCAL_PATH, FILE_NAME)]
    # FILE_LIST = [os.path.join(BUCKET_PATH, FILE_NAME)]

    # Example 3: Upload selected files from a directory
    # Create a list of selected files from a directory
    # Example: only upload files ending with .nc
    # SELECTED_FILES = [f for f in os.listdir(LOCAL_PATH) if f.endswith(".nc")]
    # LOCAL_PATHS = [os.path.join(LOCAL_PATH, f) for f in SELECTED_FILES]
    # FILE_LIST = [os.path.join(BUCKET_PATH, f) for f in SELECTED_FILES]

    token_resp = requests.post(
        f"{KEYCLOAK_HOST}/auth/realms/{REALM}/protocol/openid-connect/token",
        headers={"Content-Type": "application/x-www-form-urlencoded"},
        data={
            "grant_type": "password",
            "client_id": CLIENT_ID,
            "username": USERNAME,
            "password": PASSWORD
        }
    )

    token_resp.raise_for_status()
    access_token = token_resp.json()["access_token"]

    presigned_resp = requests.post(
        API_URL,
        headers={
            "Authorization": f"Bearer {access_token}",
            "Content-Type": "application/json"
        },
        json={
            "file_paths": FILE_LIST
        }
    )

    presigned_resp.raise_for_status()
    presigned_data = presigned_resp.json()

    for idx, (filename, info) in enumerate(presigned_data.items()):
        start = time()
        upload_url = info["upload_url"]
        with open(LOCAL_PATHS[idx], "rb") as f:
            upload_resp = requests.put(upload_url, data=f)
            if upload_resp.status_code in [200, 201]:
                end = time()
                print(f"✅ Uploaded {LOCAL_PATHS[idx]} to {info['key']} in {end - start}s successfully")
            else:
                print(f"❌ Failed to upload {LOCAL_PATHS[idx]} to {info['key']} : {upload_resp.status_code} {upload_resp.text}")


If you have any questions or issues upload a file please contact an administrator.