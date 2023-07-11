---
authors:
- Vincent Lin
categories:
- General
- Announcements
date: 2023-01-20
draft: false
lastmod: 2023-01-20
summary: Now, we are excited to announce that KubeEdge v1.13.0, released on January 18, 2023, achieves SLSA 3 compliance, first of its kind in the CNCF community.
tags:
- KubeEdge
- edge computing
- slsa
- Provenance
- slsa-github-generator
- sigstore
- Software supply chain
title: KubeEdge - CNCF's First SLSA 3 Project
---

In July 2022, the KubeEdge community completed a [third-party security audit of KubeEdge](https://github.com/kubeedge/community/blob/master/sig-security/sig-security-audit/KubeEdge-security-audit-2022.pdf) and released a paper on cloud native edge computing security threat analysis and protection. Based on the security threat model and audit suggestions, the community consistently strengthens the KubeEdge software supply chain. Now, we are excited to announce that KubeEdge v1.13.0 (including both binary and container image artifacts), released on January 18, 2023, achieves SLSA 3 compliance, first of its kind in the CNCF community.

## Why Is SLSA 3 Compliance Important to KubeEdge

Software supply chain integrity attacks (unauthorized modification of software packages) have been increasing in the past three years. After KubeEdge reaches SLSA 3, the E2E security (from source code build to release) is hardened, preventing malicious tampering of binary and container image artifacts. Thanks to SLSA, we can enhance the integrity of software artifacts, against tampering or any type of unauthorized modification of the software or software package, and enable software to defend against common supply chain attacks.

## About SLSA

Proposed by Google, SLSA (Supply Chain Levels for Software Artifacts) is a security framework and a check-list of standards and controls to prevent tampering, improve software supply chain integrity, and secure software packages and infrastructure in projects, businesses or enterprises. It is not a single tool, but a step-by-step outline to prevent artifacts from being tampered with and tampered artifacts from being used, and at the higher levels, to strengthen the platforms that make up a supply chain. Manufacturers follow the SLSA guidelines to safeguard their software, and users make decisions based on the security status of software packages.

SLSA is currently in alpha, and related definitions are subject to change.

The following figure describes the known attack points in software supply chains. For details, see https://slsa.dev/.

![img](images/slsa-dev.png)

**SLSA introduces many new tools and concepts, such as:**

- Artifact: any artifact file generated by a build pipeline, such as container images, language packages, and compiled binary files.
- Provenance: built metadata, including the build steps, build sources, and dependencies.
- Digest: the result of a cryptographic hash function. The hash function generates a fixed-size value that uniquely identifies an artifact, such as an SHA-256 hash of a container image.
- Attestation: an encrypted and signed file that records the source of software artifacts.
- Immutable references: an identifier that is guaranteed to always point to the same, immutable artifact, such as a specific container image or language package.
- Build integrity: verifies the output integrity of the build pipeline.

## How KubeEdge Reaches SLSA 3

The SLSA requirements are arranged into four levels. The higher the level, the higher the requirements. For details, see https://slsa.dev/spec/v0.1/requirements.

When the third-party security audit report was released in July 2022, KubeEdge did not meet SLSA 3 requirements in terms of SLSA provenance. After continuous hardening by SIG-Security, KubeEdge finally achieved SLSA 3 in January 2023. The following table lists the compliance status of KubeEdge in **Source, Build, Provenance, and Common**. ("Y" indicates compliance, while blank indicates non-compliance.)

**SLSA requirements and compliance**

| **Requirement**        | **L1** | **L2** | **L3** | **L4** |
| ---------------------- | ------ | ------ | ------ | ------ |
| **Source**             |        |        |        |        |
| Version  controlled    |        | Y      | Y      | Y      |
| Verified  history      |        |        | Y      | Y      |
| Retained  indefinitely |        |        | Y      | Y      |
| Two-person  reviewed   |        |        |        | Y      |
| **Build**              |        |        |        |        |
| Scripted  build        | Y      | Y      | Y      | Y      |
| Build service          |        | Y      | Y      | Y      |
| Build as code          |        |        | Y      | Y      |
| Ephemeral  environment |        |        | Y      | Y      |
| Isolated               |        |        | Y      | Y      |
| Parameterless          |        |        |        | Y      |
| Hermetic               |        |        |        | Y      |
| **Provenance**         |        |        |        |        |
| Available              | Y      | Y      | Y      | To-do  |
| Authenticated          |        | Y      | Y      | To-do  |
| Service  generated     |        | Y      | Y      | To-do  |
| Non-falsifiable        |        |        | Y      | To-do  |
| Dependencies  complete |        |        |        | To-do  |
| **Common**             |        |        |        |        |
| Security               |        |        |        | Y      |
| Access                 |        |        |        | Y      |
| Superusers             |        |        |        | Y      |

 This section describes how KubeEdge meets the build and provenance requirements of SLSA 3. The build/provenance requirements and corresponding KubeEdge solutions are as follows:

**Build requirements:**

**a)  Scripted build:** All build steps were fully defined in some sort of "build script".

**b)  Build service:** All build steps ran using some build services, not on a developer's workstation. Build services include GitHub Actions and those provided by third-party cloud platforms.

**c)  Build as code:** The build definition and configuration executed by the build service is verifiably derived from text file definitions stored in a version control system.

**d)  Ephemeral environment:** The build service ensured that the build steps ran in an ephemeral environment, such as a container or VM, provisioned solely for this build, and not reused from a prior build.

**e)  Isolated:** The build service ensured that the build steps ran in an isolated environment free of influence from other build instances, whether prior or concurrent.

**f)   Parameterless:** The build output cannot be affected by user parameters other than the build entry point and the top-level source location. In other words, the build is fully defined through the build script and nothing else.

**g)  Hermetic:** All transitive build steps, sources, and dependencies were fully declared up front with immutable references, and the build steps ran with no network access.

**Solution:**

All KubeEdge build processes are automatically executed by scripts on GitHub. As a build service (with definition files and configuration files stored in the **.github/workflows** directory), GitHub Actions ensure that build steps are traceable and verifiable, the build environment is ephemeral and isolated, and the build parameters and dependencies cannot be tampered with. 

**Provenance requirements:**

**a)  Available:** The provenance is available to the consumer in a format that the consumer accepts. The format SHOULD be in-toto SLSA Provenance, but another format MAY be used if both producer and consumer agree and it meets all the other requirements.

**b)  Authenticated:** The provenance's authenticity and integrity can be verified by the consumer. This SHOULD be through a digital signature from a private key accessible only to the service generating the provenance.

**c)  Service generated:** The data in the provenance MUST be obtained from the build service.

**d)  Non-falsifiable:** Provenance cannot be falsified by the build service's users.

**e)  Dependencies complete:** Provenance records all build dependencies that were available while running the build steps. This includes the initial state of the machine, VM, or container of the build worker.

**Solution:**

slsa-github-generator, SLSA's official GitHub build project, is integrated into the artifacts (including binary files and container images) released in KubeEdge to achieve SLSA 3.

The KubeEdge version release process (*.github/workflows/release.yml*) integrates *slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml* and *slsa-framework/slsa-github-generator/.github/workflows/generator_container_slsa3.yml* to ensure that the process of building and releasing artifacts (including binary files and container images) meets the SLSA 3 requirements.

For details about slsa-github-generator, see https://github.com/slsa-framework/slsa-github-generator.

### About Provenance

Provenance is the build metadata, a proof of the software build and release execution process and can be verified. It includes build steps, build sources, and dependencies, such as source code repositories, code branches, and configuration files. Provenance content of SLSA 3 is authentic, tamper-proof, and will not be changed by project maintainers. The provenance file (named [multiple.intoto.jsonl](https://github.com/kubeedge/kubeedge/releases/download/v1.13.0/multiple.intoto.jsonl)) of binary artifacts is released with the release software package. The provenance file of a container image is uploaded to the KubeEdge Docker Hub public repository together with the image. For details about the Provenance formats, see https://github.com/slsa-framework/slsa-github-generator/blob/main/internal/builders/generic/README.md#provenance-format.

 

### How to Verify That KubeEdge Release Artifacts Meet SLSA 3

For details, see https://github.com/kubeedge/kubeedge/pull/4285.

The following is an example:

```
$ COSIGN_EXPERIMENTAL=1 cosign verify-attestation --type slsaprovenance --policy policy.cue kubeedge/cloudcore:v1.13.0
```

```
{
	"_type": "https://in-toto.io/Statement/v0.1",
	"predicateType": "https://slsa.dev/provenance/v0.2",
	"subject": [{
			"name": "index.docker.io/kubeedge/cloudcore",
			"digest": {
				"sha256": "825642e63ab5b924e2fa0661cd14d544d0be151c4bdba6f3f42796c977fbe211"
			}
		}
	],
	"predicate": {
		"builder": {
			"id": "https://github.com/slsa-framework/slsa-github-generator/.github/workflows/generator_container_slsa3.yml@refs/tags/v1.4.0"
		},
		"buildType": "https://github.com/slsa-framework/slsa-github-generator/container@v1",
		"invocation": {
			"configSource": {
				"uri": "git+https://github.com/kubeedge/kubeedge@refs/tags/v1.13.0",
				"digest": {
					"sha1": "ee357a0d5341241143e88d45df99fde865c987de"
				},
				"entryPoint": ".github/workflows/release.yml"
			},
...
```

## Signing and Working Principles of slsa-github-generator

OpenID Connect (OIDC) is used to prove the identity of a workflow to an external service (Sigstore). OIDC is a standard used across the web by identity providers to authenticate users for third parties. GitHub now supports OIDC in its workflows. Each time a workflow runs, a runner can create a unique JWT token from GitHub's OIDC provider. The token contains verifiable information about the workflow identity, including the caller repository, commit hash, trigger, and current (reusable) workflow path and reference.

With OIDC, the workflow proves its identity to Sigstore's Fulcio root certificate authority (an external authentication service). Fulcio signs a short-term certificate to prove the temporary signature key (generated in the runner) and binds the key to the workload identity. The signing record is stored in Sigstore's transparency log Rekor. A user can use the signed certificate as a trusted anchor to verify that the provenance was authenticated and cannot be falsified; it must have been created in a trusted builder. The process is as follows:

![img](images/sigstore.png)

In 2022, slsa-github-generator was honored by the Sigstore community as Best User Adopter.

![img](images/best-user-adopter.png)

## Summary

SLSA plays an important role in the software supply chain security of KubeEdge. Based on the Sigstore capabilities, the entire software supply chain process of KubeEdge, from source code to release artifacts, is signed and verified to improve the security.

 

 

 

**References:**

[1] KubeEdge blog: https://kubeedge.io/en/blog/reach-slsa-l3/

[2] The third-party security audit of KubeEdge: https://github.com/kubeedge/community/blob/master/sig-security/sig-security-audit/KubeEdge-security-audit-2022.pdf

[3] SLSA website: https://slsa.dev/

[4] Sigstore website: https://www.sigstore.dev/

[5] SLSA Generator: https://github.com/slsa-framework/slsa-github-generator

[6] SLSA3 generic integration guide: https://slsa.dev/blog/2022/08/slsa-github-workflows-generic-ga

[7] Improving software supply chain security with tamper-proof builds: https://security.googleblog.com/2022/04/improving-software-supply-chain.html

[8] Sigstore November Roundup: https://blog.sigstore.dev/sigstore-november-roundup-8a852cec10fc/