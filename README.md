# ๐ฆ Ubiquitous Journey ๐ฅ

๐งฐ This repo embodies a GitOps approach to deploying application code, middleware infrastructure and supporting CI/CD tools. ๐งฐ

At its simplest, the repo is an [ArgoCD Application](https://argo-cd.readthedocs.io/en/stable/core_concepts/) which references [other helm charts](https://github.com/redhat-cop/helm-charts.git) and [other kustomize definitions](https://github.com/rht-labs/refactored-adventure) to deploy applications.

The idea is to reference other Charts, Kustomize, YAML snippets from within this framework. This keeps things `pluggable` to suit the needs of your team.

๐จ We have evolved the design from the original [Labs CI / CD](https://github.com/rht-labs/labs-ci-cd.git) project. The  Ubiquitous Journey (`UJ`) represents a major milestone in moving to a GitOps approach to tooling, application management and configuration drift using [ArgoCD](https://argoproj.github.io/argo-cd/).

## Table of Contents

- [Contributor Covenant Code of Conduct](./code-of-conduct.html#contributor-covenant-code-of-conduct)
  * [Our Pledge](./code-of-conduct.html#our-pledge)
  * [Our Standards](./code-of-conduct.html#our-standards)
  * [Our Responsibilities](./code-of-conduct.html#our-responsibilities)
  * [Scope](./code-of-conduct.html#scope)
  * [Enforcement](./code-of-conduct.html#enforcement)
  * [Attribution](./code-of-conduct.html#attribution)
- [๐ฆ Ubiquitous Journey ๐ฅ](./index.html#%F0%9F%A6%84-ubiquitous-journey-)
  * [Components](./index.html#components)
  * [How do I run it? ๐โโ๏ธ](./index.html#how-do-i-run-it-)
    + [Prerequisites](./index.html#prerequisites)
    + [Let's go, installing ArgoCD ๐๐ป](./index.html#lets-go-installing-argocd-)
    + [๐ค  Deploying the Ubiquitous Journey](./index.html#%F0%9F%A4%A0-deploying-the-ubiquitous-journey)
    + [Cleanup ๐งค](./index.html#cleanup-)
    + [Debugging ๐คบ](./index.html#debugging-)
- [Common Errors when installing ArgoCD](./docs%2Fargocd-install.html#common-errors-when-installing-argocd)
- [ArgoCD Master and Child ๐ฉโ๐ฆ](./docs%2Fargocd-master-child.html#argocd-master-and-child-)
- [Restricted Children](./docs%2Fargocd-master-child.html#restricted-children)
- [Bootstrap projects and ArgoCD ๐ป](./docs%2Fbootstrap-argocd.html#bootstrap-projects-and-argocd-)
  * [Tooling for Application Development ๐ฆ](./docs%2Fbootstrap-argocd.html#tooling-for-application-development-)
      - [(A) Deploy using argo app of apps ...](./docs%2Fbootstrap-argocd.html#a-deploy-using-argo-app-of-apps-)
      - [(B) Deploy using helm ...](./docs%2Fbootstrap-argocd.html#b-deploy-using-helm-)
- [Example Application Deploy ๐ฎ](./docs%2Fbootstrap-argocd.html#example-application-deploy-)
- [Cleaning up ArgoCD Apps ๐งน](./docs%2Fbootstrap-argocd.html#cleaning-up-argocd-apps-)
- [Metrics ๐](./docs%2Fbootstrap-argocd.html#metrics-)
- [Deploy to a custom namespace ๐ฆด](./docs%2Fdeploy-custom-namespace.html#deploy-to-a-custom-namespace-)
- [Help me](./docs%2Fhelp.html#help-me)
  * [Not automated yet ...](./docs%2Fhelp.html#not-automated-yet-)
- [Sealed Secrets Help](./docs%2Fsealed-secrets.html#sealed-secrets-help)
  * [๐ต๏ธโโ๏ธ Generate Sealed Secrets:](./docs%2Fsealed-secrets.html#%F0%9F%95%B5%EF%B8%8F%E2%80%8D%E2%99%80%EF%B8%8F-generate-sealed-secrets)
  * [๐ Bring your own certs](./docs%2Fsealed-secrets.html#%F0%9F%93%9D-bring-your-own-certs)
- [What's in the box? ๐จ](./docs%2Fwhats-in-the-box.html#whats-in-the-box-)
- [What it's not...๐คท๐ปโโ๏ธ](./docs%2Fwhats-in-the-box.html#what-its-not)
- [Dashboard ๐](./docs%2Fwhats-in-the-box.html#dashboard-)

## Components

The folder structure of this repo is split as follows:

```bash
โโโ archive                            <===  ๐ where the skeletons live. archived material.
โโโ docs                               <===  ๐ supporting documentation for UJ.
โโโ pet-battle                         <===  ๐ the example application `pet-battle`
โโโ templates                          <===  ๐ helm templates to create ArgoCD Applications and Projects for UJ
โโโ ubiquitous-journey                 <===  ๐ helm values files containing applications we wish to deploy
โโโ Chart.yaml                         <===  ๐ we deploy UJ using a helm chart
โโโ values.yaml                        <===  ๐ UJ's helm chart values
```

There are two main components to this repository:

1. `Ubiquitous Journey` - Contains all the tools, collaboration software and day2ops to be deployed on Red Hat OpenShift. This includes chat applications, task management apps and tools to support CI/CD workflows and testing. For the complete list and details: [What's in the box?๐จ](docs/whats-in-the-box.md)
2. An demo application called [`pet-battle`](https://github.com/petbattle) that shows you how to use the UJ structure with a three tiered application stack.

Each part can be used independently of each other but sequentially they create a full stack.

## How do I run it? ๐โโ๏ธ

If you already have an ArgoCD instance running and you want just want to add the tooling to it, [move to part 2](docs/bootstrap-argocd.md#tooling-for-application-development-๐ฆ) in the docs.

### Prerequisites

You will need:

- OpenShift 4.6+ or greater (cluster admin user required) - [Try OpenShift](https://try.openshift.com)
- Install helm v3+ (cli) or greater - [Helm Quickstart](https://helm.sh/docs/intro/quickstart)

### Let's go, installing ArgoCD ๐๐ป

Install an instance of ArgoCD. There are several methods to install ArgoCD in OpenShift. Pick your favorite flavour ๐ฆ

Use the Red Hat supported GitOps Operator (configured by default as cluster wide and to deploy the operator and an instance in `labs-ci-cd`)

```bash
helm repo add redhat-cop https://redhat-cop.github.io/helm-charts
helm upgrade --install argocd \
  --create-namespace \
  --namespace labs-ci-cd \
  redhat-cop/gitops-operator
```

โท๏ธ We **strongly** recommend that you make a copy of the `values.yaml` file and make edits that way. This values file can be checked in to this repo and be kept if further changes are needed such as adding in private `repositoryCredentials` or other handy stuff such as `secrets` and `namespaces` etc. For example, you have `argocd-values.yaml` file with your changes:

```bash
helm upgrade --install argocd \
  --create-namespace \
  --namespace labs-ci-cd \
  -f argocd-values.yaml \
  redhat-cop/gitops-operator
```

If you have trouble ๐ตโ๐ซ - we have documented some common errors [when installing ArgoCD](docs/argocd-install.md) which may help.

### ๐ค  Deploying the Ubiquitous Journey

A handy one liner to deploy all the default software artifacts in this project using their default values. Just make sure the namespace you set below is the same as your ArgoCD namespace from the previous step.

```bash
helm upgrade --install uj --namespace labs-ci-cd .
```

If you login to ArgoCD using the UI here:

```bash
echo https://$(oc get route argocd-server --template='{{ .spec.host }}' -n labs-ci-cd)
```

you should see lots of things spinning up

![argocd-ui](docs/images/argocd-uj.png)

You can set `enabled: true` on all of the application definitions in the `values-*.yaml` files if you want to deploy everything ๐งจ .... ๐ฅ

Fork the repo and make your changes in the fork if you wish to GitOp enable things. Update the `source` in values.yaml to make sure ArgoCD is pulling from the correct source repo (your fork). If you've already forked the repo and want to deploy quickly you can also run:

```bash
helm upgrade --install uj \
  --set source=https://github.com/<YOUR_FORK>/ubiquitous-journey.git \
  --namespace labs-ci-cd .
```

### Cleanup ๐งค

Uninstall and delete all resources in the various projects
```bash
# This may take a while:
helm delete uj --namespace labs-ci-cd

# Then remove your ArgoCD instance
helm delete argocd --namespace labs-ci-cd
```

### Debugging ๐คบ

Run the following command to debug one of the UJ values files to see which values are being passed:

```bash
# example debugging the ArgoCD `Application` manifests from the example deployment 
helm install debug --dry-run -f pet-battle/test/values.yaml . 
```
