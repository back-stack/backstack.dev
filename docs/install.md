# Install the BACK Stack

In order to try out the BACK Stack locally you can follow these steps

You will use [Porter][getporter] to perform the installation. Currently, the generated Bundle supports installing the BACK stack on EKS or locally using KinD

### Porter Bundle Info & Settings

Credentials:

**NOTE**: Although cloud provider credentials are not required, without them you cannot provision new clusters.

How to generate Azure AKS `kubeconfig` file:

```sh
$ az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup --file kubeconfig-azure
```

How to generate AWS EKS `kubeconfig` file:

```sh
$ aws eks update-kubeconfig --region us-west-1 --name backstack-hub --kubeconfig kubeconfig-aws
```

How to generate Azure Credentials:

```sh
$ az ad sp create-for-rbac --sdk-auth --role Owner --scopes /subscriptions/$YOURSUBSCRIPTIONID > azure.json
```

How to pass AWS Credentials:
**NOTE** Ensure you have stored your Key ID and Access Key in a file under the `default` heading (e.g.)

```sh
$ cat ~/.aws/credentials
[default]
aws_access_key_id = YOURKEYHERE
aws_secret_access_key = YOURSECRETKEYHERE
```

---

| Name              | Description                                            | Required | Comments                                           |
| ----------------- | ------------------------------------------------------ | -------- | -------------------------------------------------- |
| aws-credentials   | Credentials to be used for Crossplane `provider-aws`   | false    |                                                    |
| azure-credentials | Credentials to be used for Crossplane `provider-azure` | false    |                                                    |
| github-token      | Github API token                                       | true     |                                                    |
| kubeconfig        | kubeconfig to connect to non-local cluster             | false    | This should point to a kubeconfig for the cluster you want to install to. Ensure there is a valid long term authentication token stored in the file |
| vault-token       | This should always be `root`                           | true     |                                                    |

Parameters:

---

| Name           | Description                                                       | Type   | Default                                  | Required | Comments |
| -------------- | ----------------------------------------------------------------- | ------ | ---------------------------------------- | -------- | -------- |
| argocd-host    | DNS name for ArgoCD                                               | string | `argocd-7f000001.nip.io`                 | false    |          |
| backstage-host | DNS name for Backstage                                            | string | `backstage-7f000001.nip.io`              | false    |          |
| cluster-type   | Target kubernetes cluster type. Accepted values are `kind`, `eks` | string | `kind`                                   | true     |          |
| repository     | Gitops repository for cluster requests and catalog-info           | string | `https://github.com/$YOURUSERNAME/showcase` | true     |          |
| vault-host     | DNS name for Vault                                                | string | `vault-7f000001.nip.io`                  | false    |          |

This bundle uses the following tools: docker, exec, helm3, Kubernetes.

### Generic Installation Instructions

To install this bundle, run the following commands, passing `--param KEY=VALUE` for any parameters you want to customize:

```sh
$ porter credentials generate back-stack-cloud-creds --reference ghcr.io/back-stack/showcase-bundle:latest
```

```sh
$ porter install --reference ghcr.io/back-stack/showcase-bundle:latest --credential-set back-stack-cloud-creds --param repository=https://github.com/USER/REPO
```

### Installing Locally into KinD

The Porter bundle already includes KinD, so the only prerequisite is Docker/Docker Desktop to be running.

1.  Install porter
2.  Generate the credentials config, leaving the `kubeconfig` empty (it will be ignored)

    ```sh
    $ porter credentials generate back-stack-cloud-creds --reference ghcr.io/back-stack/showcase-bundle:latest
    ```

3.  Install the bundle; the default `cluster-type` and `*-host` parameters are configured for local deployment, and you need to allow Porter to access your local docker daemon.

    ```sh
    $ porter install back-stack --reference ghcr.io/back-stack/showcase-bundle:latest --credential-set back-stack-cloud-creds --param repository=https://github.com/USER/REPO --allow-docker-host-access
    ```

To connect to the KinD cluster running the BACK stack, update your kubeconfig:

```sh
$ porter installations output show kubeconfig-external -i back-stack > ~/.kube/config
```

### Installing into EKS

-  Existing EKS cluster with [AWS Load Balancer Controller][alb-controller] add-on installed
-  local `kubeconfig` file to connect to the cluster

1.  Install porter (see above)
2.  Generate the credentials config, specifying the path to the `kubeconfig-aws` file

    ```sh
    $ porter credentials generate back-stack-cloud-creds --reference ghcr.io/back-stack/showcase-bundle:latest
    ```

3.  Install the bundle; set `cluster-type` to `eks` and specify DNS names that you want to use to access the BACK stack services. This can either be done using `--param` flags or by generating a parameter set

    ```sh
    # using parameter set
    $ porter parameters generate back-stack-params --reference ghcr.io/back-stack/showcase-bundle:latest

    $ porter install back-stack --reference ghcr.io/back-stack/showcase-bundle:latest --credential-set back-stack-cloud-creds --parameter-set back-stack-params
    ```

4.  After installation, you need to ensure the DNS names specified for `argocd-host`, `backstage-host`, and `vault-host` all resolve to the ingress service created during installation. The endpoint for this can be found by checking the bundle outputs

    ```sh
    $ porter installations output show ingress -i back-stack
    ```

    For this showcase, you can update `/etc/hosts`.

[getporter]: https://getporter.org
[alb-controller]: https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html

