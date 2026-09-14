# OCP to AKS Migration Report

- **Repository:** `ocp-source`
- **Mode:** `offline`
- **Generated:** 2026-09-14T01:46:15+00:00

## Environment(s) converted

| Name | Tier | AKS Cluster | Namespace |
|---|---|---|---|
| `dev` | `dev` | `devops-ocp-to-aks-001` | `dev` |

## Verdict: BLOCK

4 blocking finding(s) must be resolved before this workload can be deployed to AKS. No pull request will be opened for deployment. The most severe items are structural defects the OpenShift platform tolerated but AKS will reject.

| Severity | Count |
|---|---|
| BLOCK | 4 |
| HIGH | 4 |
| INFO | 8 |
| NEEDS_INPUT | 1 |

## Transforms applied

| Rule | File | Change |
|---|---|---|
| `T1` | `07-route.yaml` | Route converted to Ingress |
| `L1` | `07-ingress.yaml` | LLM reviewed and updated this file |

## Findings requiring action

### [BLOCK] `V7` — Forbidden apiVersion image.openshift.io/v1

**Location:** `01-imagestream.yaml`

image.openshift.io/v1 is not permitted on AKS.

**Remediation:** Migrate to the supported API version.

### [BLOCK] `V7` — Forbidden apiVersion build.openshift.io/v1

**Location:** `02-buildconfig.yaml`

build.openshift.io/v1 is not permitted on AKS.

**Remediation:** Migrate to the supported API version.

### [BLOCK] `V7` — Forbidden apiVersion apps.openshift.io/v1

**Location:** `05-deploymentconfig.yaml`

apps.openshift.io/v1 is not permitted on AKS.

**Remediation:** Migrate to the supported API version.

### [BLOCK] `V7` — Forbidden apiVersion apps.openshift.io/v1

**Location:** `08-hpa.yaml`

apps.openshift.io/v1 is not permitted on AKS.

**Remediation:** Migrate to the supported API version.

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

### [INPUT] `T1` — Route TLS termination requires a secretName

**Location:** `07-route.yaml`

The source Route specified TLS. The emitted Ingress references .Values.route.tlsSecretName, which is not yet defined.

**Remediation:** Provision/import the TLS secret on AKS and set route.tlsSecretName in the environment's values file.

## Artefacts

| File | Contents |
|---|---|
| `inventory.json` | Discovered files with OpenShift coupling scores |
| `findings.json` | Machine-readable findings |
| `verdict.json` | Judge decision and counts |
| `diff.patch` | Unified diff, source to migrated |
| `rendered/` | Migrated artefacts ready for pull request |
