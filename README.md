# README

## Install ArgoCD first time (manual)

Create `deployments` directory in git repo
```
mkdir deployments && cd "$_"
```

Find and pull the argocd with specific version
```
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm search repo argo/argo-cd --versions
helm pull argo/argo-cd --version 6.7.18 --untar
```

Create new custom value file
```
cd argo-cd
cp values.yaml values-dev.yaml
```

Open `values-custom.yaml` and update config

Install helm chart
```
helm install argocd . -f values-custom.yaml --create-namespace --namespace argocd
```

## Access the ArgoCD UI

Get the admin password
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Config port forwarding
```
kubectl port-forward argocd/argocd-server -n argocd 8080:8080
```

Open a web browser and access to http://localhost:8080

Enter `admin` and admin password that got in above step

## Create ArgoCD ApplicationSet

In the `deployments\argo-cd` create `.argocd-config.json` that contains the ArgoCD appliaction config
```
{
  "appType": "helm",                    # Application type: helm or directory
  "valuesFile": "values-dev.yaml",      # Value file
  "namespace": "argocd"                 # Namespace
}
```

At the root of git repo, create a `appset.yaml` file

Apply the yaml file
```
kubectl apply -f appset.yaml -n argocd
```
