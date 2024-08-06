---
title: "Cloud Run"
description: "Google Cloud Run reference"
weight: 6
---

# Deploy docker image to Google Cloud Run


## Push local image to Google Artifact Registry

- Quick start: [https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images](https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images)

- Overview: [https://cloud.google.com/artifact-registry/docs/docker](https://cloud.google.com/artifact-registry/docs/docker)

### Set defaults

```
$ gcloud config set project [project-name]
```

### Push local docker image to a Docker repository at Google Cloud Artifact Registry

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


## Deploy image to Cloud Run

- [Quickstart: Deploy to Cloud Run](https://cloud.google.com/run/docs/quickstarts/deploy-container)
- [Deploying container images to Cloud Run](https://cloud.google.com/run/docs/deploying)

- **gcloud run services list**: List all Cloud Run services deployed
