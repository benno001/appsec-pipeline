# appsec-pipeline
AppSec pipeline on Kubernetes with Jenkins

**NB:** This Minikube/Helm setup is not suitable for production use.

1. Install Minikube (https://kubernetes.io/docs/setup/minikube/#installation)
1. Install Helm (https://docs.helm.sh/using_helm/#installing-helm)
1. Start minikube using `minikube start --vm-driver=virtualbox` or desired other provider (check out the link to Minikube installation for a list).
  * Check whether minikube is alive with `minikube status`.
1. Clone this repository and `cd` to it.
1. Create a Jenkins namespace using `kubectl create -f minikube/jenkins-namespace.yaml`. 
  * Check whether it has succeeded with `kubectl get ns`
1. Create a persistent Jenkins volume using `kubectl create -f minikube/jenkins-volume.yaml`.
1. Change the API key secret, **BASE64 encode it**, and create the secret in kubernetes with `kubectl create -f kubectl/defect-dojo-secrets.yaml --namespace=jenkins-ns`
1. `cd` to the `helm`  directory and use helm to initialize Jenkins (run the command `helm init`).
1. `cd` back to the top directory and deploy Jenkins: `helm install --name jenkins -f helm/jenkins-values.yaml stable/jenkins --namespace jenkins-ns`
  * Verify whether it has been deployed sucessfully with `helm ls`.
1. Somewhere in the output of the 'install' command, we can find the command for retrieving the admin password for Jenkins from our kubernetes secret store: `printf $(kubectl get secret --namespace jenkins-ns jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo`. Retrieve it and store it.
1. Visit http://192.168.99.100:32000/ and login with `admin`  and the previously retrieved password.
