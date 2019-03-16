# helm-install-init-gke-error
Helm install in GKE leads to an error. Article explains how to resolve it.

## How this document helps you?
When you use Helm to install your application on GKE, you will encounter the error "Error: release name is required." The article describes the next steps that you need to take to resolve the issue.

I will go through the list of steps from installing the Helm till installing my application using Helm. Note: For this article, the app that I'm using is irrelevant, as the focus of this article is to document what would be the next step to resolve the error rather than talking about the actual application that I'm trying to install using Helm. For your example, you can try using Wordpress.

> Note: I'm doing all this on GCP Platform directly. Not using `gcloud sdk`

## First, Install Helm
```
<account info>@cloudshell:~ (<project-name>)$ curl https://raw.githubusercontent.com/helm/helm/master/scripts/get > get_helm.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7234  100  7234    0     0  41309      0 --:--:-- --:--:-- --:--:-- 41337
```

## Second, change permission and invoke the script to install
```
chmod 700 get_helm.sh && ./get_helm.sh
```

> Follow this article to learn more ways to install Helm: https://helm.sh/docs/using_helm/#installing-helm

## Third, install Helm
```
<account info>@cloudshell:~ (<project-name>)$ ./get_helm.sh
Downloading https://kubernetes-helm.storage.googleapis.com/helm-v2.13.0-linux-amd64.tar.gz
Preparing to install helm and tiller into /usr/local/bin
helm installed into /usr/local/bin/helm
tiller installed into /usr/local/bin/tiller
Run 'helm init' to configure helm.
```

## Fourth, invoke `helm init` to setup Tiller
```
<account info>@cloudshell:~ (<project-name>)$ helm init
$HELM_HOME has been configured at /home/partnergcp/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
```

## Fifth, Install any app using `helm install`. You will get an error
```
<account-info>@cloudshell:~ (<project-name>)$ helm install .
Error: release name is required
```

## Sixth, give relevant permissions to helm to install an app
```
<account-info>@cloudshell:~ (<project-name>)$ kubectl create serviceaccount --namespace kube-system tiller
serviceaccount/tiller created
<account-info>@cloudshell:~ (<project-name>)$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller-cluster-rule created
<account-info>@cloudshell:~ (<project-name>)$ kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
deployment.extensions/tiller-deploy patched
```

## Final, now try again installing an app using Helm. It should complete the job successfully (provided your app is right)
