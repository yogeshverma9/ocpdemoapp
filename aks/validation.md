# OCP to AKS Migration Report

- **Repository:** `ocp-source`
- **Mode:** `offline`
- **Generated:** 2026-09-14T05:06:04+00:00

## Environment(s) converted

| Name | Tier | AKS Cluster | Namespace |
|---|---|---|---|
| `dev` | `dev` | `devops-ocp-to-aks-001` | `dev` |

## Verdict: NEEDS_REVIEW

No blocking defects, but 5 finding(s) require human judgement and 5 configuration value(s) are still TBC. A pull request will be opened for review; it will not auto-merge.

| Severity | Count |
|---|---|
| HIGH | 4 |
| MEDIUM | 1 |
| INFO | 14 |
| NEEDS_INPUT | 5 |

## Transforms applied

| Rule | File | Change |
|---|---|---|
| `T1` | `07-route.yaml` | Route converted to Ingress |
| `T10` | `01-imagestream.yaml` | ImageStream 'hello-openshift' removed (no AKS equivalent) |
| `T10` | `01-imagestream.yaml` | ImageStream 'hello-nodejs' removed (no AKS equivalent) |
| `T11` | `02-buildconfig.yaml` | BuildConfig 'hello-nodejs-build' removed (no AKS equivalent) |
| `T9` | `05-deploymentconfig.yaml` | DeploymentConfig 'hello-openshift' converted to Deployment |
| `T12` | `08-hpa.yaml` | HPA scaleTargetRef remapped to Deployment |
| `L2` | `05-deploymentconfig.yaml` | LLM refinement replayed from frozen cache |
| `L2` | `07-ingress.yaml` | LLM refinement replayed from frozen cache |

## Findings requiring action

### [HIGH] `M9` — No imagePullSecrets defined in any template

**Location:** `helm/templates/deployment.yaml`

Pods cannot authenticate to a private registry.

**Remediation:** Add imagePullSecrets, or grant AcrPull to the kubelet identity.

### [HIGH] `V4` — Security: Root file system is not read-only

**Location:** `05-deploymentconfig.yaml`

An immutable root file system prevents applications from writing to their local disk. This can limit intrusions, as attackers will not be able to tamper with the file system or write foreign executables to disk.

**Remediation:** Change 'containers[].securityContext.readOnlyRootFilesystem' to 'true'.

### [HIGH] `V4` — Security: Default security context configured

**Location:** `05-deploymentconfig.yaml`

Security context controls the allocation of security parameters for the pod/container/volume, ensuring the appropriate level of protection. Relying on default security context may expose vulnerabilities to potential attacks that rely on privileged access.

**Remediation:** To enhance security, it is strongly recommended not to rely on the default security context. Instead, it is advisable to explicitly define the required security parameters (such as runAsNonRoot, capabilities, readOnlyRootFilesystem, etc.) within the security context.

### [HIGH] `V4` — Security: Default security context configured

**Location:** `05-deploymentconfig.yaml`

Security context controls the allocation of security parameters for the pod/container/volume, ensuring the appropriate level of protection. Relying on default security context may expose vulnerabilities to potential attacks that rely on privileged access.

**Remediation:** To enhance security, it is strongly recommended not to rely on the default security context. Instead, it is advisable to explicitly define the required security parameters (such as runAsNonRoot, capabilities, readOnlyRootFilesystem, etc.) within the security context.

### [MED] `L3` — LLM refinement rejected, reverted to deterministic output

**Location:** `08-hpa.yaml`

The model's response for this file failed a basic sanity/YAML check and was discarded; the deterministic transform/remediate output was kept.

**Remediation:** Optionally re-run with LLM_FORCE_REFRESH=true, or inspect the cached prompt/response under LLM_CACHE_DIR.

### [INPUT] `T1` — Route TLS termination requires a secretName

**Location:** `07-route.yaml`

The source Route specified TLS. The emitted Ingress references .Values.route.tlsSecretName, which is not yet defined.

**Remediation:** Provision/import the TLS secret on AKS and set route.tlsSecretName in the environment's values file.

### [INPUT] `T10` — ImageStream 'hello-openshift': confirm image source on AKS

**Location:** `01-imagestream.yaml`

Source: docker.io/openshift/hello-openshift:latest.

**Remediation:** Mirror docker.io/openshift/hello-openshift:latest into the target ACR (TBC_ACR_NAME.azurecr.io) (e.g. 'az acr import') and reference it directly in the container image field.

### [INPUT] `T10` — ImageStream 'hello-nodejs': confirm image source on AKS

**Location:** `01-imagestream.yaml`

Source: built in-cluster (see BuildConfig).

**Remediation:** Push the image built by the corresponding BuildConfig straight to the target ACR (TBC_ACR_NAME.azurecr.io) from the CI pipeline, and reference it directly in the container image field.

### [INPUT] `T11` — BuildConfig 'hello-nodejs-build': move build into the CI pipeline

**Location:** `02-buildconfig.yaml`

Source: https://github.com/sclorg/nodejs-ex.git (ref master), output image: 'hello-nodejs:latest'.

**Remediation:** Add a docker build+push step for this image to the target ACR in the CI pipeline (see pipeline/build.yaml for the pattern this repo already uses for containerised builds).

### [INPUT] `T9` — DeploymentConfig 'hello-openshift': verify image rollout strategy

**Location:** `05-deploymentconfig.yaml`

The removed ImageChange/ConfigChange triggers drove automatic rollouts on this DeploymentConfig; Deployment has no equivalent.

**Remediation:** Confirm the CD pipeline's image-tag update (see the T2 deploy.yaml rewrite, which sets --set image.tag=<resolved tag> on every helm upgrade) replaces what the trigger did.

## Artefacts

| File | Contents |
|---|---|
| `inventory.json` | Discovered files with OpenShift coupling scores |
| `findings.json` | Machine-readable findings |
| `verdict.json` | Judge decision and counts |
| `diff.patch` | Unified diff, source to migrated |
| `rendered/` | Migrated artefacts ready for pull request |
