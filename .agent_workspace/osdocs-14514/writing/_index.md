# Documentation Modules: OSDOCS-14514

**Ticket:** OSDOCS-14514
**Generated:** 2026-06-04
**Placement mode:** DRAFT (staging area)
**Output format:** AsciiDoc

## Files Written

| Path | Type | Description |
|------|------|-------------|
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/configure-network-bridges-virtualization-installation.adoc | CONCEPT | Parent topic explaining why custom br-ex configurations are needed and overview of configuration methods |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/understanding-br-ex-configuration-methods.adoc | CONCEPT | Comparison of install-config hostConfig vs MachineConfig methods, when to use each |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/install-config-networking-hostconfig-reference.adoc | REFERENCE | Parameter structure, field definitions, validation rules, examples |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/configure-br-ex-install-config.adoc | PROCEDURE | Steps to configure br-ex at installation using install-config.yaml |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/comparison-br-ex-configuration-methods.adoc | REFERENCE | Detailed comparison table of the two configuration methods |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/verify-br-ex-install-config.adoc | PROCEDURE | Verification steps after installation |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/modules/troubleshoot-br-ex-install-config.adoc | PROCEDURE | Troubleshooting common configuration issues |
| /Users/ntimpe/Documents/Github/openshift-docs-neal-fork/.agent_workspace/osdocs-14514/writing/assembly_configure-network-bridges-virtualization.adoc | ASSEMBLY | Main assembly combining all modules into complete user story |

## Module Structure

### Assembly: Configure network bridges for virtualization workloads

Reading order:
1. Configure network bridges for virtualization at installation time (CONCEPT) - Parent topic
2. Understanding br-ex configuration methods (CONCEPT) - Introduces both methods
3. install-config networking.hostConfig parameter reference (REFERENCE) - API details
4. Configure br-ex bridge at installation using install-config.yaml (PROCEDURE) - Core hands-on task
5. Comparison: install-config vs MachineConfig methods for br-ex (REFERENCE) - Decision support
6. Verify br-ex bridge configuration after installation (PROCEDURE) - Validation
7. Troubleshoot br-ex install-config configuration issues (PROCEDURE) - Problem resolution

## Shared Prerequisites

- Understanding of Kubernetes networking concepts (pods, services, external connectivity)
- Familiarity with NMState YAML syntax for network configuration
- Access to DNS infrastructure for node hostname resolution
- Cluster-admin access for verification and troubleshooting procedures

## Integration Points

- Integrates into existing "Installing on bare metal" and platform-specific installation guides in "Network customization" sections
- Cross-references to OpenShift Virtualization documentation for VM networking use cases
- Updates required to existing modules:
  - `modules/installation-configuration-parameters.adoc` - Add networking.hostConfig parameter
  - `modules/installation-bare-metal-config-yaml.adoc` - Add hostConfig example
  - `modules/creating-manifest-file-customized-br-ex-bridge.adoc` - Add note about new method
  - `modules/enabling-OVS-balance-slb-mode.adoc` - Reference new method
  - `modules/migrating-br-ex-bridge-nmstate.adoc` - Clarify day 1 vs day 2
  - `modules/installation-two-node-creating-manifest-custom-br-ex.adoc` - Add install-config note
  - `installing/installing_bare_metal/upi/installing-bare-metal.adoc` - Include new procedure
  - `modules/creating-manifest-file-customized-br-ex-bridge-post.adoc` - Clarify day 2 focus

## JTBD Alignment

**Main Job**: Configure installation networking for virtualization workloads

**Job Statement 1 (Installation - Day 1):**
When deploying an OpenShift cluster with custom bridge networking requirements for virtualization workloads, I want to configure the br-ex bridge at installation time using a declarative interface in install-config.yaml, so that I can ensure proper network connectivity for VMs without manually encoding NMState YAML or creating MachineConfig manifests, while avoiding configuration errors and reducing installation complexity.

**Job Statement 2 (Verification and Troubleshooting - Day 1):**
When an OpenShift cluster installation completes with custom br-ex networking configurations, I want to verify the bridge was created correctly and troubleshoot any configuration issues, so that I can ensure nodes are properly networked for VM workloads before moving to production, while avoiding network connectivity failures that could impact cluster stability.

## Content Journey Phase Distribution

| Phase | Modules | Coverage |
|-------|---------|----------|
| **Expand** | Understanding br-ex configuration methods (CONCEPT) | Introduces users to both methods and their relationship |
| **Discover** | install-config parameter reference (REFERENCE), Parent topic (CONCEPT) | Provides reference and overview for exploration |
| **Learn** | Configure br-ex procedure (PROCEDURE) | Core hands-on configuration task |
| **Evaluate** | Verify procedure (PROCEDURE), Comparison reference (REFERENCE) | Supports validation and decision-making |
| **Adopt** | Troubleshooting procedure (PROCEDURE) | Addresses operational issues |

## Quality Checks Completed

- [x] All modules have `:_mod-docs-content-type:` attribute set
- [x] Module anchor IDs include `_{context}` suffix
- [x] Assembly anchor ID does NOT include `_{context}`
- [x] Short descriptions with `[role="_abstract"]` present in all modules
- [x] Titles follow JTBD outcome-focused conventions
- [x] Ventilated prose used (one sentence per line)
- [x] Symlinks created to `_attributes/` and `snippets/`
- [x] Assembly includes `_attributes/common-attributes.adoc`
- [x] No parent-context constructions
- [x] Code blocks specify source language
- [x] No callouts in code blocks (using definition lists instead)
- [x] User-replaced values marked with angle brackets
- [x] Cross-references use `xref:` syntax with context suffix

## Next Steps

1. Run Vale linting against all modules to check for style violations
2. Technical review by OpenShift Networking SME
3. Update existing modules listed in "Integration Points"
4. Move files to repository locations when ready for publication
