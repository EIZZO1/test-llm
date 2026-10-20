# OpenShift AI & Lightspeed Stack (CPU Deployment)

Declarative GitOps repository managed by OpenShift GitOps / Argo CD to deploy a complete CPU-based AI inference stack with OpenShift Lightspeed.

## Architecture & Sync Waves

The deployment is coordinated using Argo CD sync waves to ensure resources are created in strict dependency order:

| Wave | Component | File | Description |
|---|---|---|---|
| **0** | RBAC | `00-argocd-rbac.yaml` | Grants `openshift-gitops-argocd-application-controller` permissions for custom resources, operators, CRDs, and batch jobs. |
| **1** | Operators | `01-operators.yaml` | Deploys Namespaces, OperatorGroups, and Subscriptions for Service Mesh, Serverless, OpenShift AI (RHOAI), and Lightspeed. |
| **2** | DSC / CRD Waiter | `02-dsc-init.yaml` | Initializes `DSCInitialization` & `DataScienceCluster` in RawDeployment mode; runs an Argo CD `Sync` hook Job waiting for KServe CRDs. |
| **3** | CPU LLM Serving | `03-cpu-llm-serving.yaml` | Deploys namespace `llm-cpu-serving`, `llama-cpp-cpu-runtime` `ServingRuntime`, and `cpu-llm` `InferenceService` (Qwen2.5-1.5B-Instruct GGUF via llama.cpp). |
| **4** | Lightspeed Config | `04-lightspeed-config.yaml` | Creates `cpu-llm-api-key` Secret and cluster-scoped `OLSConfig` referencing the internal KServe CPU model endpoint. |

## Quick Start

Apply the Argo CD Application manifest to your OpenShift cluster:

```bash
oc apply -f apps/ai-stack-app.yaml
```
