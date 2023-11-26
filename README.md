# Mini CI/CD using Jenkins to Deploy in a Kubernetes Cluster with Minikube

This lab provides a quick way to deploy a Jenkins container and a Kubernetes cluster locally.

## Scenario

You need to provision a Jenkins container and deploy to a Kubernetes cluster from Jenkins.

## Pre-requisites

- Docker

## Action

1. **Start Minikube:**

```bash
minikube start --base-image "gcr.io/k8s-minikube/kicbase:v0.0.32"
```

For more version check `minikube-kicbase`(https://console.cloud.google.com/gcr/images/k8s-minikube/global/kicbase)

2. **Run Jenkins Container:**

```bash
docker run --name jenkins --rm -d -p 8080:8080 -p 50000:50000 -v jenkins_data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock --env JENKINS_ADMIN_ID=admin --env JENKINS_ADMIN_PASSWORD='P@ssword2#J&N1ks' --network minikube alexsimple/jenkins_jcasc:v5
```

The Jenkins container is configured as Code (JCasC) and comes with pre-installed plugins (e.g., Kubernetes). It runs in
the same network as Minikube, enabling communication between both.

3. **Configure Jenkins to Connect to Minikube:**

Copy the Minikube configuration file to a new location:

```bash
cp ~/.kube/config /home/user/config/minikube_config
```

Edit the new file and look for the following parameters:

```yaml
- cluster:
certificate-authority: /home/user/.minikube/ca.crt
...
users:
  - name: minikube
user:
client-certificate: /home/user/.minikube/profiles/minikube/client.crt
client-key: /home/user/.minikube/profiles/minikube/client.key
```

Retrieve the information using the commands below and replace it in the Minikube configuration file:

```bash
cat ~/.minikube/ca.crt | base64 -w 0 ; echo # Copy content
cat ~/.minikube/profiles/minikube/client.crt | base64 -w 0; echo  # Copy content
cat ~/.minikube/profiles/minikube/client.key | base64 -w 0; echo  # Copy content
```

Update the Minikube configuration file with the base64-encoded content:

```yaml
- cluster:
certificate-authority-data: LS0tLS1CRUdJTiBDRV... #example
...
users:
  - name: minikube
user:
client-certificate-data: QjJZRWxjZ1o3Ckp4VDZBSj... #example
client-key-data: BQTRJQkFRQmZ0bm9lczdYd04... #example
```

4. **Configure Jenkins Kubernetes Plugin:**

- Access the Jenkins web console.
- Navigate to Jenkins -> Set up a distributed build -> Configure a Cloud.
- Create a new Kubernetes cloud configuration.
- Create a new credential, choosing "Secret File" and loading the Minikube configuration file.
- Save the credential.
- Test the connection using the new credential.

If successful, you'll receive a message confirming that Jenkins has connected to Minikube.

Congratulations! You can now experiment with Jenkins and your local Kubernetes cluster.

![InitialScreen](/images/InitialScreen.png)
![InitialScreen](/images/img.png)
![InitialScreen](/images/img_1.png)
![InitialScreen](/images/img_3.png)
![InitialScreen](/images/img_4.png)


## Troubleshooting

"https://devopscube.com/jenkins-build-agents-kubernetes/"