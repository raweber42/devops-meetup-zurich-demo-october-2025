## Setup Steps

### 1. Create Kubernetes Cluster
Create cluster (see [kubernetes-sandbox](https://github.com/raweber42/kubernetes-sandbox))

### 2. Install Crossplane
Install crossplane (also see above repository)

TIP:
> to check how compositions work and if yours are working the way you want, try running crossplane render:
```
crossplane render composition-demo/xr.yml composition-demo/composition.yml composition-demo/functions.yml > rendered_output.yml
```
this gives you the final result (in rendered_output.yml) of the composition, so you can check, how a xr created by a dev would look create resources as a result


> install functions for composition (k apply demo/functions.yaml)
> 


## Presentation

For more details, see the [presentation slides](https://docs.google.com/presentation/d/1cIln1reF7z9UFrWj60vPK3z8jqmDStQsRh_CiEfGGXg/edit?usp=sharing).
