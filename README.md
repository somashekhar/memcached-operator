# memcached-operator
PoC to understand the kubebuilder through Memchached

# This sample will
- Create a Deployment with the Memcached image
- Not allow more instances than the size defined in the CR which will be applied
- Update the Memcached CR status
- Reconcile a Memcached CR - which represents an instance of a Memcached deployed/managed on cluster

# Commands

```

# download kubebuilder and install locally.
    curl -L -o kubebuilder "https://go.kubebuilder.io/dl/latest/$(go env GOOS)/$(go env GOARCH)"
    chmod +x kubebuilder && sudo mv kubebuilder /usr/local/bin/

# Create a project
    mkdir -p projects/memcached-operator
    cd projects/memcached-operator
    kubebuilder init --domain example.com --repo github.com/somashekhar/memcached-operator

# Create the Memcached API(CRD)
    kubebuilder create api --group cache --version v1alpha1 --kind Memcached

# Generating manifests with the specs and validations
    To generate all required files:
    Run make generate to create the DeepCopy implementations in api/v1alpha1/zz_generated.deepcopy.go.
    Then, run make manifests to generate the CRD manifests under config/crd/bases and a sample for it under config/samples.

# Create a docker image of the operator and run it in kind cluster
    docker build -t memcached-operator:v1 .
    docker images

    kind load docker-image memcached-operator:v1 --name my-cluster

    IMG=memcached-operator:v1 make deploy

# Create the Custom Resources
    kubectl apply -f config/samples/cache_v1alpha1_memcached.yaml
    kubectl logs -f -n memcached-operator-system deployment/memcached-operator-controller-manager

# Check the created resources
    kubectl get memcached
    kubectl get deployment memcached-sample
    kubectl get pods -l app=memcached
    kubectl get memcached memcached-sample -o yaml # Check status

```