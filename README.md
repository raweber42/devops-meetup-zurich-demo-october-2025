# DevOps Meetup Zurich - Crossplane Talk Demo

This is the demo repository for a talk about Crossplane at the DevOps Meetup Zurich by Ralf Weber. Questions are very welcome!

## Setup Steps

### 1. Create Kubernetes Cluster
Create a Kubernetes cluster to run Crossplane. You can follow the instructions in my dedicated [kubernetes-sandbox](https://github.com/raweber42/kubernetes-sandbox) repo for a quick setup.

### 2. Install Crossplane
Install Crossplane in your cluster (see the repository above for detailed installation steps, a simple `--crossplane` flag will do!).

#### Configuration Steps:

Crossplane uses a composition-based approach where you define reusable infrastructure patterns. Follow these steps in order:

1. **Apply RBAC** (Role-Based Access Control)
    ```bash
    kubectl apply -f composition-demo/rbac.yaml
    ```
    This gives Crossplane the necessary permissions to create and manage cloud resources on your behalf.

2. **Install function for composition**
    ```bash
    kubectl apply -f composition-demo/functions.yaml
    ```
    This installs the functions that Crossplane will use to generate resources.

3. **Apply composition** (Infrastructure Template)
    ```bash
    kubectl apply -f composition-demo/composition.yaml
    ```
    This defines the actual kubernetes resources that will be created when someone requests infrastructure through the XRD above.

4. **Apply XRD** (Composite Resource Definition)
    ```bash
    kubectl apply -f composition-demo/xrd.yaml
    ```
    This creates a custom API that defines what fields users can specify when requesting infrastructure (like database name, size, etc.).

5. **Apply XR** (Composite Resource Instance)
    ```bash
    kubectl apply -f composition-demo/database.yaml
    ```
    This creates an actual database instance using the template you defined. This is what end users would create to get their infrastructure.

6. **Verify Database is Running**
    ```bash
    # Check that the StatefulSet is ready
    kubectl get statefulsets
    
    # Connect to the database to verify it's working
    kubectl exec -it <pod-name> -- psql -U devopsuser -d devopsdb
    ```
    This confirms that the PostgreSQL database is running correctly and accessible.


#### Additional Context:
- Crossplane tracks all created resources using Kubernetes owner references, making it easy to see which resources belong together
- The XRD acts like a custom Kubernetes API for your infrastructure
- The composition is the template that converts user requests into actual cloud resources
- The XR is an instance created by users to request specific infrastructure

#### Testing Your Composition:
Before applying changes, test your composition locally using `crossplane render`:

```bash
crossplane render composition-demo/xr.yaml composition-demo/composition.yaml composition-demo/functions.yaml > rendered_output.yaml
```

This command shows you exactly what cloud resources Crossplane will create, helping you verify your composition works correctly before deploying.

For more details on testing compositions, see the [Crossplane documentation](https://docs.crossplane.io/latest/composition/compositions/#test-a-composition).

## Cleanup

To clean up all resources created during the demo, run the following commands in reverse order:

```bash
# Delete the composite resource instance (this will clean up all managed resources)
kubectl delete -f composition-demo/database.yaml
```
```bash
# Delete the composition
kubectl delete -f composition-demo/composition.yaml
```
```bash
# Delete the XRD
kubectl delete -f composition-demo/xrd.yaml
```
```bash
# Delete the functions
kubectl delete -f composition-demo/functions.yaml
```
```bash
# Delete the rbac
kubectl delete -f composition-demo/rbac.yaml
```

**Note**: Deleting the composite resource instance first will trigger Crossplane to clean up all the managed cloud resources automatically due to owner references.

## Presentation

For the complete context and explanation, see the [presentation slides](https://docs.google.com/presentation/d/1cIln1reF7z9UFrWj60vPK3z8jqmDStQsRh_CiEfGGXg/edit?usp=sharing).

