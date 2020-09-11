# AK8S-02 Working with Cloud Build
## Login with the qwiklab credentials
    gcloud auth login
    gcloud config set project qwiklabs-gcp-00-022aa5706761
## Task 1: Confirm that needed APIs are enabled
Make sure that Cloud Build and Container Registery APIs are enabled.

## Task 2. Building Containers with DockerFile and Cloud Build
 - `nano quickstart.sh`

    #!/bin/sh
    echo "Hello, world! The time is $(date)."

 - `nano Dockerfile`

    FROM alpine
    COPY quickstart.sh /
    CMD ["/quickstart.sh"]

 - `chmod +x quickstart.sh`

 - `export GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-02-b92db0c6b6d6`
 - `gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image .`

Check if the image is created :
    gcloud container images list
The quickstart-image Docker image appears in the list

## Task 3. Building Containers with a build configuration file and Cloud Build
    git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst

    cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/a

## Task 4. Building and Testing Containers with a build configuration file and Cloud Build
Change to the directory that contains the sample files for this lab
    cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/b

Start a Cloud Build using cloudbuild.yaml as the build configuration file:
    gcloud builds submit --config cloudbuild.yaml .

