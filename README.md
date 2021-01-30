# downscaleCMIP6-infra-argo
Argo Workflow configuration and deploy for CMIP6 downscaling project.

Manifests for Argo Workflow itself are in `argo/`. The `workflows-default/` directory contains manifests required to run analysis Workflows.

## Updating

If there is already has a deployment -- file a pull request with changes to `main`. Merged PRs are checked and automatically pulled. 

## Deploying

Assuming `kubectl` and `kustomize` is installed and configured for the target cluster, deploy `argo` onto the cluster with

```
kustomize argo/ | kubectl apply -f -
kustomize workflows-default/ | kubectl apply -f -
```

or if using `argocd`:

```
argocd app create argo \
    --repo https://github.com/ClimateImpactLab/downscaleCMIP6-infra-argo.git \
    --path argo \
    --dest-server https://kubernetes.default.svc \
    --sync-policy automated \
    --auto-prune
argocd app create workflows-default \
    --repo https://github.com/ClimateImpactLab/downscaleCMIP6-infra-argo.git \
    --path workflows-default \
    --dest-server https://kubernetes.default.svc \
    --sync-policy automated \
    --auto-prune
```

Test either deployment method with

```
argo submit -n default --serviceaccount workflows-default --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml 
```

This assumes you have the `argo` CLI application installed locally. From the output, grab the workflow name and run

```
argo logs -n argo <workflow-name>
```

If all is well, you will see a happy whale:

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

### Argo artifact repo (minio)

Deploys `minio` on the cluster as an S3 wrapper to an underlying Azure Blob Storage. With the S3 interface, we can easily use Azure storage as an Argo Workflows artifact repository.

Deploy `minio` into the `argo` namespace with:

```
argocd app create minio \
    --repo https://github.com/ClimateImpactLab/downscaleCMIP6-infra-argo \
    --revision add_minio \
    --path minio \
    --values values.yaml \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace argo \
    --sync-policy automated \
    --auto-prune \
    --self-heal \
    --port-forward-namespace argocd
```