# downscaleCMIP6-infra-argo
Argo Workflow configuration and deploy for CMIP6 downscaling project.

## Installation
Assuming `kubectl` is installed and configured for the target cluster, deploy `argo` onto the cluster with
```
kubectl apply -n argo -f manifests
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/v2.12.2/manifests/namespace-install.yaml 
```
or if using `argocd`:
```
kubectl apply -n argo -f manifests
rgocd app create argo \
    --repo https://github.com/argoproj/argo.git \
    --path manifests/cluster-install \
    --dest-server https://kubernetes.default.svc \
    --revision v2.12.3 \
    --dest-namespace argo
argocd app sync argo
```

This is a needed workaround for `kustomize` via https://github.com/kubernetes-sigs/kustomize/issues/3340. But anyways...

Test it with
```
argo submit -n argo --serviceaccount workflows-default --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml 
```
This assumes you have the `argo` CLI application installed locally. From the output, grab the workflow name and run 
```
argo logs -n argo <workflow-name>
```

You should see a happy whale like this:
```
hello-world-kqvvg:  _____________ 
hello-world-kqvvg: < hello world >
hello-world-kqvvg:  ------------- 
hello-world-kqvvg:     \
hello-world-kqvvg:      \
hello-world-kqvvg:       \     
hello-world-kqvvg:                     ##        .            
hello-world-kqvvg:               ## ## ##       ==            
hello-world-kqvvg:            ## ## ## ##      ===            
hello-world-kqvvg:        /""""""""""""""""___/ ===        
hello-world-kqvvg:   ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
hello-world-kqvvg:        \______ o          __/            
hello-world-kqvvg:         \    \        __/             
hello-world-kqvvg:           \____\______/   
```
