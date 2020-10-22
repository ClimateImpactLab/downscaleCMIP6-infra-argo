# downscaleCMIP6-infra-argo
Argo Workflow configuration and deploy for CMIP6 downscaling project.

## Installation
Assuming `kubectl` is installed and configured for the target cluster, deploy `argo` onto the cluster with
```
kubectl apply -n argo -f manifests
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/namespace-install.yaml 
```

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
