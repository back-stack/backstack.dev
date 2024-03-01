# Development

## Local Development

If you are interested in working on The BACK stack and its components there are a few different components which are utilized within the repository.

```sh
├── .devcontainer
├── argocd
├── backstage
├── bundle
├── crossplane
└── kyverno
```

In the following sections we will breakdown each components use and workflows.

### Dev Container

The [showcase](https://github.com/back-stack/showcase) repository includes a [devcontainer](https://containers.dev/) configuration to provide all of the necessary tooling for development of the BACK stack. The only prerequisites on your local machine are:

- a container runtime that supports Docker (e.g. [Docker Desktop](https://www.docker.com/products/docker-desktop/))
- a runtime for devcontainer (e.g. [VSCode](https://code.visualstudio.com/docs/devcontainers/containers), [DevPod](https://devpod.sh/), or [GitHub Codespaces](https://github.com/features/codespaces))

### ArgoCD

In the `argocd` folder you will find all the files necessary for deploying Kyverno and the default sets of policies it uses to secure your clusters and installation.

### Backstage

### Crossplane

### Kyverno

### Porter/Bundle

If you are interested in working on The BACK stack and its components
