# React App on Google Kubernetes Engine (GKE)

A reference project to deploy a React app onto Google Kubernetes Engine (GKE)

Referencing [this](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app#cloud-shell_1) GCP documentation

## Pre-requisites

- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) already installed locally
- Install the `kubectl` CLI tool:

```bash
gcloud components install kubectl # installs the kubectl CLI tool
```

## Build/Push the Docker image

- Create a GCP project. Then, run the following commands:

```bash
docker build -t gcr.io/<your_project_id>/gcp-k8-react-app:v1 . # builds the Docker image
docker run --rm -p 8080:8080 gcr.io/<your_project_id>/gcp-k8-react-app:v1 # runs the image in a container at http://localhost:8080/
gcloud auth configure-docker # configure Docker CLI tool to authenticate with Container Registry
docker push gcr.io/<your_project_id>/gcp-k8-react-app:v1 # pushes image to Container Registry!
```

## Deploy app to GKE

- Create a GKE cluster:

```bash
gcloud config set project <your_project_id> # set your gcloud project ID
gcloud config set compute/zone europe-west2-a # set your gcloud compute zone
gcloud container clusters create gcp-k8-react-cluster # create a GKE cluster
gcloud container clusters get-credentials gcp-k8-react-cluster --zone europe-west2-a --project <your_project_id> # set your local kubectl command to run against the GKE environment
```

- Create a deployment:

```bash
kubectl create deployment gcp-k8-react-cluster --image=gcr.io/<your_project_id>/gcp-k8-react-app:v1 # creates a deployment
kubectl expose deployment gcp-k8-react-cluster --name=gcp-k8-react-app-service --type=LoadBalancer --port 80 --target-port 8080 # exposes the deployment via a load balancer
kubectl get service # lists services. Find app URL in EXTERNAL-IP
```

- Clean-up:

```bash
kubectl delete service gcp-k8-react-app-service # deletes service
gcloud container clusters delete gcp-k8-react-cluster # deletes cluster
gcloud container images delete gcr.io/<your_project_id>/gcp-k8-react-app:v1  --force-delete-tags --quiet # deletes image
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

[MIT](https://choosealicense.com/licenses/mit/)
