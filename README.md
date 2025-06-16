# CI/CD pipeline with Jenkins and ArgoCD  
This project implements a continuous integration and continuous deployment (CI/CD) pipeline to automate the deployment of a Python application to a Kubernetes cluster using Jenkins, GitHub, Docker Hub, and ArgoCD. The workflow is as follows:

1. Push the Code: The process begins when the Python application code (e.g., .py files) is pushed to the GitHub code repository.
Trigger Job to Push the Latest Docker Image: The code push triggers a Jenkins job that builds and pushes the latest Docker image to Docker Hub.
2. Push the Latest Docker Image: Jenkins builds the Docker image and uploads it to Docker Hub with a tag corresponding to the build number (e.g., avindudocker1/gitops-jenkins-build:DOCKERTAG).
3. Trigger Job to Update deployment.yaml for Image Change: Jenkins triggers a second job to update the Kubernetes manifests repository on GitHub, modifying the deployment.yaml file to reflect the new Docker image tag.
4. Look in the Repository for Kubernetes Manifests Changes: ArgoCD continuously monitors the GitHub Kubernetes manifests repository for changes to deployment.yaml.
Sync the Latest Changes with the Kubernetes Cluster: ArgoCD synchronizes the Kubernetes cluster by applying the updated deployment.yaml, ensuring the cluster matches the desired state with the latest Docker image.

//paste diagram

# Jenkins setup

1. Setup jenkins credential
   i. DockerHub credential
   ii. GitHub credential(Generate a GitHub token) 
	*This 'github' credential id should match with 'manifests repo's credential

2. Create Jenkins job

   a.Repo update pipeline
   --------------------
   i. Select pipeline
   ii. Select 'pipeline from SCM'
   iii. Attach code repository(not K8 manifests repo URL) URL to REpository URL
   iv. Select script path as Jenkinsfile
  
   b.Kubernetes manifests update pipeline
   ------------------------------------
   i. select pipeline
   ii. select parameterized
       - select job and string parameter name as DOCKERTAG
   iii. select 'pipeline from SCM'
   iv. select SCM as Git
   v.  select repository URL as K8 manifests repo URL
   vi. select branch specific to Build as '*/main'    

3. Start docker image build and push to the dockerhub job('buildimage')

# ArgoCD setup
1. install ArgoCD using installation link
2. Check pods and deployments working without any issue.
3. 
```bash
kubectl port-forward -n <namespace> svc/<ArgoCD_server_service> 8080:80
```
Note: These port numbers are default ports, these port numbers can changed to any desired port number

4. Access ArgoCD UI
5. Select '+NEW APP'
6. Name the application(Jenkins-GitOps-app)
7. Select project as 'default'
8. Select sync policy as 'Automatic'
9. Select cluster URL as 'http://kubernetes.default.svc
10. Select <namespace> (namespace should match to the ArgoCD pods running namespace)
11. Change the image version in the deployment.yaml and push to the Github repository

# Links

- [Code repo](https://github.com/GitAvi001/CI-CD-Jenkins-ArgoCD-KubernetesManifests)
- [ArgoCD installation](https://argo-cd.readthedocs.io/en/stable/getting_started/)
- [Jenkins installation](https://www.jenkins.io/doc/book/installing/)
