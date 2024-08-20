---
title: "Google Cloud Run"
description: "Google Cloud Run reference"
weight: 6
---

# Deploying a service

There are several ways to deploy your apps to Cloud Run:

## 1. Deploy from source code 

1. The service account will need the **storage.objects.list** permission in order to deploy to Cloud Run. Assigning the role **cloudbuild.builds.builder** will grant that permission among others:
```
$ gcloud projects add-iam-policy-binding [PROJECT-NAME] --member="serviceAccount:[ACCOUNT-ID]-compute@developer.gserviceaccount.com" --role="roles/cloudbuild.builds.builder"
```

2. In your local machine, go to your project's root directory.

3. A Dockerfile must be present in the project's root directory.

4. Run: `$ gcloud run deploy`

You will be prompted to respond several questions:
- **Source code location (/home/user/Code/myapp/)**: *press enter if you already are in your project root directory*
- **Service name (myapp)**: *myapp*
- **Please specify a region. Please enter numeric choice or text value (must exactly match list item)**: *32*
- **Allow unauthenticated invocations to [myapp] (y/N)?** ***yes** if you want people to visit your app* 

After answering the questions, gcloud will upload the source code to Google Cloud where it will start to build an image. This image will be stored in a repository in **Artifact Registry**. This image will then be deployed to Cloud Run.

All the process is printed out in the terminal with its status. After a succesful operation, you will get the URL of your app.

You can check the service deployed by running:

```
$ gcloud run services list
```

## 2. Deploy container image 


### 2.1 Push local image to Google Artifact Registry

- Quick start: [https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images](https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images)

- Overview: [https://cloud.google.com/artifact-registry/docs/docker](https://cloud.google.com/artifact-registry/docs/docker)

#### Set defaults

```
$ gcloud config set project [project-name]
```

#### Push local docker image to a Docker repository at Google Cloud Artifact Registry

1. Enable Artifact Registry API [[docs](https://cloud.google.com/sdk/gcloud/reference/services/enable)]
```
$ gcloud services enable artifactregistry.googleapis.com
```

2. Create a Docker repository named **test** [[docs](https://cloud.google.com/sdk/gcloud/reference/artifacts/repositories/create)]
```
$ gcloud artifacts repositories create test --repository-format=docker
```
3. Create a service account called **dockerpusher** that can push Docker images from our local machine to Google Cloud Artifact Registry [[docs](https://cloud.google.com/sdk/gcloud/reference/iam/service-accounts/create)]
```
$ gcloud iam service-accounts create dockerpusher --description="In charge of pushing docker images to Artifact Registry" --display-name="Docker Pusher"
```

4. Give **dockerpusher** the role of *Artifact Registry Writer* [[docs](https://cloud.google.com/sdk/gcloud/reference/iam/service-accounts/add-iam-policy-binding)]
``` bash
$ gcloud projects add-iam-policy-binding beach-walks-azure --member="serviceAccount:dockerpusher@beach-walks-azure.iam.gserviceaccount.com" --role="roles/artifactregistry.writer"
```
5. Create a service account key file for **dockerpusher** to indentify it on other platforms (in this case Docker) [[docs](https://cloud.google.com/iam/docs/keys-create-delete#iam-service-account-keys-create-gcloud)]
```
$ gcloud iam service-accounts keys create ~/dockerpusher-private-key.json --iam-account=dockerpusher@beach-walks-azure.iam.gserviceaccount.com
```

6. Authenticating to Artifact Registry for Docker for a service account (in this case **dockerpusher**) using '*gcloud CLI credential helper*' method [[docs](https://cloud.google.com/artifact-registry/docs/docker/authentication#gcloud-helper)]
```
$ gcloud auth activate-service-account dockerpusher@beach-walks-azure.iam.gserviceaccount.com --key-file=~/dockerpusher-private-key.json
```
Note that you can also authenticate for a user gmail account with the '*gcloud CLI credential helper*' method by using `$ gcloud auth login`

7. Authenticating to a repository using '*gcloud CLI credential helper*' method [[docs](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling#auth)]

- If your `~/.docker/config.json` file doe not exist, create one and add curly braces
```
$ echo "{}" >  $HOME/.docker/config.json
```
- and then add *us-central1* to config.json:
```
gcloud auth configure-docker us-central1-docker.pkg.dev
```

8. Tag a local image called **go-app** [[docs](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling#tag)]
```
$ docker tag go-app us-central1-docker.pkg.dev/beach-walks-azure/test/myimage
```
If you don't tag the image like the above command, `docker push` will try to push the image to **Docker Hub** instead of **Artifact Registry** in the step below 

9. Push the tagged image to Artifact Registry [[docs](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling#push-tagged)]
```
$ docker push us-central1-docker.pkg.dev/beach-walks-azure/test/myimage
```

10. Show information of **test** repository
```
$ gcloud artifacts repositories describe test
$ gcloud artifacts docker images list us-central1-docker.pkg.dev/beach-walks-azure/test --include-tags
```


### 2.2 Deploy image to Cloud Run

- [Quickstart: Deploy to Cloud Run](https://cloud.google.com/run/docs/quickstarts/deploy-container)
- [Deploying container images to Cloud Run](https://cloud.google.com/run/docs/deploying)

You can check the service deployed by running:

```
$ gcloud run services list
```

# Deleting a service

If you don't know the name and the region of the service to be deleted, you can check the list of active services:

```
$ gcloud run services list
```

Once you know the service name and region, you can delete it:

```
$ gcloud run services delete SERVICE --region REGION
```

- Deleting a service deletes all resources related to this service, including all revisions of this service whether they are serving traffic or not.
- Deleting a service does not automatically remove container images from Container Registry. To delete container images used by the deleted revisions from Container Registry, refer to Deleting images.
- Deleting a service with one or more Eventarc triggers does not automatically delete these triggers. To delete the triggers refer to Manage triggers.
- After deletion, the service remains visible in the Google Cloud console and in the command line interface until the deletion is fully complete. However, you cannot update the service.
- Deleting a service is permanent: there is no undo or restore. However, if after deleting a service, you deploy a new service with the same name in the same region, it will have the same endpoint URL.


# Artifact Registry management

### List repositories

```
$ gcloud artifacts repositories list
```

### Delete a repository

```
$ gcloud artifacts repositories delete REPOSITORY-NAME
```

### List images in a repository

```
$ gcloud artifacts docker images list us-central1-docker.pkg.dev/PROJECT-NAME/REPOSITORY-NAME
```

### Delete an image in a repository

```
$ gcloud artifacts docker images delete us-central1-docker.pkg.dev/PROJECT-NAME/REPOSITORY-NAME/IMAGE-NAME
```

