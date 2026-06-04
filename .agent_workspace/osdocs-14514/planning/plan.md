# Documentation Plan

**Project**: OpenShift br-ex NMState install-config interface
**Date**: 2026-06-04
**Ticket**: [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514)

## What is the support status of the feature(s) being used to complete the user's JTBD (Job To Be Done)?

General Availability (OpenShift 4.19)

## Why is this content important?

System administrators deploying OpenShift clusters with virtualization workloads need a simplified method to configure the br-ex bridge at installation time. The previous approach required manual base64-encoding of NMState YAML and creation of MachineConfig manifests placed in the installer's manifests directory, which was error-prone and created a poor user experience, particularly for users migrating from VMware who expect simplified network configuration interfaces.

The new networking.hostConfig interface in install-config.yaml eliminates manual encoding and manifest manipulation, allowing administrators to specify NMState configurations declaratively. This reduces configuration errors, accelerates deployment, and provides an experience consistent with other installation customization options. For organizations deploying multiple OpenShift clusters or those with complex networking requirements for single-interface VM deployments, this simplification significantly reduces installation complexity and risk.

Without clear documentation, users may continue using the more complex MachineConfig method, miss opportunities to simplify their workflows, or struggle with verification and troubleshooting when the new method doesn't work as expected. This content enables administrators to adopt the simplified method confidently and understand when each approach is appropriate.

## Who is the target persona(s)?

* **SysAdmin**: Primary user who installs and configures OpenShift clusters with custom networking requirements for virtualization workloads
* **IT Operations Leader**: Influences installation method decisions and network architecture, concerned with reducing deployment complexity and operational risk

## What is the main JTBD? What user goal is being accomplished? What pain point is being avoided?

**Job Statement 1 (Installation - Day 1):**
When deploying an OpenShift cluster with custom bridge networking requirements for virtualization workloads, I want to configure the br-ex bridge at installation time using a declarative interface in install-config.yaml, so that I can ensure proper network connectivity for VMs without manually encoding NMState YAML or creating MachineConfig manifests, while avoiding configuration errors and reducing installation complexity.

**Job Statement 2 (Verification and Troubleshooting - Day 1):**
When an OpenShift cluster installation completes with custom br-ex networking configurations, I want to verify the bridge was created correctly and troubleshoot any configuration issues, so that I can ensure nodes are properly networked for VM workloads before moving to production, while avoiding network connectivity failures that could impact cluster stability.

## How does the JTBD(s) relate to the overall real-world workflow for the user?

**Real-world workflow context:**

1. **Planning phase**: Administrators design the cluster network topology, identifying which nodes require br-ex bridges for virtualization workloads. For single-interface deployments (common in VMware migrations), they plan to share the physical interface between br-ex and the host network.

2. **Installation preparation**: Administrators create the install-config.yaml file with cluster specifications. This is where they traditionally would have stopped and switched to a separate manual process of creating MachineConfig manifests.

3. **Network configuration (NEW SIMPLIFIED PATH)**: Using the networking.hostConfig interface, administrators add br-ex NMState configurations directly to install-config.yaml, specifying per-node networking using hostname and networkConfig fields. The installer automatically handles base64-encoding and MachineConfig generation.

4. **Installation execution**: The OpenShift installer generates machine-configs (99-network-config-master and 99-network-config-worker) from the hostConfig entries and applies them during node bootstrapping. NMState YAML files are placed in /etc/nmstate/openshift/<hostname>.yml on each node.

5. **Verification (Day 1)**: After installation completes, administrators verify br-ex bridge creation using nmstatectl and ovs-vsctl commands on nodes to ensure networking is correct before deploying VM workloads.

6. **Day 2 operations**: Administrators deploy OpenShift Virtualization and create VMs that use the br-ex bridge for external network connectivity. If network issues arise, they troubleshoot using the verification procedures.

**Relationship to broader workflow:**

The install-config interface streamlines the installation workflow by keeping all installation-time configuration in a single declarative file (install-config.yaml), rather than requiring administrators to context-switch to a separate manual process of encoding YAML and creating machine-config files. This aligns with how administrators configure other installation-time customizations (proxy settings, SSH keys, platform-specific options) and reduces the risk of errors from manual base64-encoding.

For VMware migration scenarios, this simplified interface matches the declarative networking configuration patterns VMware administrators are familiar with, reducing the learning curve and accelerating OpenShift adoption.

## What high-level steps does the user need to take to accomplish the goal?

**For Job 1 (Configure br-ex at installation time):**

1. **Prerequisites**:
   - Plan node hostnames and ensure DNS resolution is configured
   - Determine which nodes require br-ex bridges for VM workloads
   - Identify the physical interfaces to use for br-ex (especially important for single-interface configurations)
   - Create NMState YAML configurations for each node's br-ex bridge setup
   - Validate NMState YAML syntax before adding to install-config.yaml

2. **Configure install-config.yaml**:
   - Create the base install-config.yaml with cluster specifications
   - Add the networking.hostConfig array
   - For each node requiring custom br-ex configuration, add a HostConfigEntry with hostname and networkConfig fields
   - Include the NMState YAML as embedded JSON in the networkConfig field

3. **Run the installer**:
   - Execute the OpenShift installer with the customized install-config.yaml
   - The installer automatically generates MachineConfig objects with base64-encoded NMState configurations
   - Monitor installation progress to ensure machine-configs are applied successfully

**For Job 2 (Verify and troubleshoot br-ex configuration):**

1. **Verify br-ex bridge creation**:
   - Access nodes via debug session or SSH
   - Run `nmstatectl show br-ex` to verify bridge exists and has correct configuration
   - Run `ovs-vsctl show` to verify OVS bridge structure and port attachments

2. **Check machine-config application status**:
   - Verify machine-configs (99-network-config-master, 99-network-config-worker) were created
   - Check that NMState files exist at /etc/nmstate/openshift/<hostname>.yml on each node
   - Review machine-config-daemon logs for application failures

3. **Troubleshoot common issues**:
   - Resolve missing interface errors (interface name mismatches between NMState config and actual hardware)
   - Fix invalid NMState syntax errors caught during machine-config application
   - Correct hostname mismatches between install-config hostConfig entries and actual node hostnames
   - Understand automatic rollback behavior when configurations fail

## Is there a demo available or can one be created?

No demo currently available. A demo environment would require a bare-metal or virtualization platform with nodes configured for single-interface networking to demonstrate the before/after comparison of manual MachineConfig creation vs install-config hostConfig method.

## Are there special considerations for disconnected environments?

No special considerations specific to this feature. The install-config interface works identically in connected and disconnected environments. However, general disconnected environment requirements apply:
- DNS resolution for node hostnames must be configured in the disconnected network
- NMState configurations must reference interfaces and network settings appropriate for the disconnected network topology
- Mirror registry configuration in install-config.yaml is independent of the networking.hostConfig settings

## Who can provide information and answer questions?

- **PM**: Determined from JIRA assignee/reporter fields in [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184)
- **Technical SME**: OpenShift Networking team (OPNET) - contact via [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592)
- **UX**: Not applicable for this installer API enhancement

## Release Note needed?

Yes

**Draft release note:**
OpenShift 4.19 introduces a new install-config interface for configuring the br-ex bridge using NMState at installation time. Administrators can now specify NMState configurations directly in the networking.hostConfig section of install-config.yaml with per-node hostname and networkConfig entries. The installer automatically generates the necessary MachineConfig objects and places NMState YAML files in /etc/nmstate/openshift/<hostname>.yml on each node. This eliminates the need for manual base64-encoding and MachineConfig manifest creation, simplifying deployment for clusters with virtualization workloads that require custom bridge networking. The previous MachineConfig-based method remains supported for advanced use cases and post-installation configuration changes.

## Links to existing content

### Existing OpenShift documentation
- [modules/creating-manifest-file-customized-br-ex-bridge.adoc](https://github.com/openshift/openshift-docs/blob/main/modules/creating-manifest-file-customized-br-ex-bridge.adoc) - Current procedure using MachineConfig manifests
- [modules/migrating-br-ex-bridge-nmstate.adoc](https://github.com/openshift/openshift-docs/blob/main/modules/migrating-br-ex-bridge-nmstate.adoc) - Post-installation br-ex migration
- [modules/enabling-OVS-balance-slb-mode.adoc](https://github.com/openshift/openshift-docs/blob/main/modules/enabling-OVS-balance-slb-mode.adoc) - OVS bonding configuration
- [modules/installation-configuration-parameters.adoc](https://github.com/openshift/openshift-docs/blob/main/modules/installation-configuration-parameters.adoc) - Install-config parameter reference

### Enhancement proposals and implementation
- [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795) - API design for networking.hostConfig interface
- [Implementation PR #10251](https://github.com/openshift/installer/pull/10251) - Installer code adding hostConfig support

### External references
- [Day One Networking in OpenShift](http://blog.nemebean.com/content/day-one-networking-openshift) - Background on day-1 network configuration
- [Red Hat Knowledge Base: Customize br-ex interface using NMState](https://access.redhat.com/solutions/7111563) - Official br-ex customization guidance
- [Troubleshooting node network configuration - OpenShift 4.17](https://docs.redhat.com/en/documentation/openshift_container_platform/4.17/html/kubernetes_nmstate/k8s-nmstate-troubleshooting-node-network) - Troubleshooting patterns

## New Docs

### Main Job: Configure installation networking for virtualization workloads

**Parent Topic**:
* **Configure network bridges for virtualization at installation time** (CONCEPT)
   - JTBD category: Configure
   - Content journey phase: Discover
   - Description: Parent topic explaining why custom br-ex configurations are needed for virtualization workloads and overview of configuration methods available
   - Content outline:
     - What: Explains that virtualization workloads require br-ex bridges for external network connectivity
     - Why: Single-interface deployments need br-ex to share the physical interface with host networking
     - How the product helps: OpenShift provides two methods - install-config interface (day 1) and MachineConfig/NNCP (day 2)
     - High-level steps: Plan node networking → Configure in install-config.yaml → Verify post-installation
     - Links to child modules: Comparison of methods, install-config procedure, verification procedure

**User Story: Configure br-ex using install-config interface**

* **Understanding br-ex configuration methods** (CONCEPT)
   - JTBD category: Configure
   - Content journey phase: Expand
   - Audience: SysAdmin, IT Operations Leader
   - Content outline:
     - Comparison of two approaches: install-config hostConfig (day 1) vs MachineConfig manifests (day 2)
     - When to use install-config method: New installations, standardized configurations, avoiding manual encoding
     - When to use MachineConfig method: Post-installation changes, complex multi-stage configurations
     - How the installer processes hostConfig entries: Automatic base64-encoding, MachineConfig generation, file placement in /etc/nmstate/openshift/
     - Relationship to OpenShift Virtualization networking model
   - Prerequisites: Understanding of NMState YAML syntax, familiarity with install-config.yaml structure
   - Dependencies: Must be read before attempting either configuration method

* **install-config networking.hostConfig parameter reference** (REFERENCE)
   - JTBD category: Configure
   - Content journey phase: Discover
   - Audience: SysAdmin
   - Content outline:
     - Parameter location: install-config.yaml networking.hostConfig array
     - Field structure: Array of HostConfigEntry objects
     - HostConfigEntry fields:
       - hostname (string, required): Node hostname matching DNS and installer expectations
       - networkConfig (object, required): NMState YAML configuration as embedded JSON
     - Data types and validation rules
     - Examples: Single-node entry, multi-node entries with different configurations
     - API schema reference: install.openshift.io v1 InstallConfig networking field
     - Automatic processing: How installer generates 99-network-config-master and 99-network-config-worker MachineConfigs
     - File placement: /etc/nmstate/openshift/<hostname>.yml on each node
   - Prerequisites: Understanding of install-config.yaml structure, NMState YAML syntax
   - Dependencies: Referenced by configuration procedures

* **Configure br-ex bridge at installation using install-config.yaml** (PROCEDURE)
   - JTBD category: Configure
   - Content journey phase: Learn
   - Audience: SysAdmin
   - Content outline:
     - Prerequisites:
       - Planned node hostnames with DNS resolution configured
       - Physical interface names for br-ex bridge
       - NMState YAML configurations validated for syntax
     - Procedure:
       1. Create base install-config.yaml
       2. Add networking.hostConfig array
       3. For each node requiring br-ex, create HostConfigEntry with hostname and networkConfig
       4. Embed NMState YAML as JSON in networkConfig field
       5. Validate install-config.yaml syntax
       6. Run OpenShift installer
     - Example: Single-interface configuration with br-ex sharing eno1
     - Example: Bonded interface configuration for high availability
     - Verification: Link to verification procedure
   - Prerequisites: install-config.yaml created, NMState configurations planned, DNS configured for node hostnames
   - Dependencies: Requires reference documentation for parameter structure

* **Comparison: install-config vs MachineConfig methods for br-ex** (REFERENCE)
   - JTBD category: Configure
   - Content journey phase: Evaluate
   - Audience: SysAdmin, IT Operations Leader
   - Content outline:
     - Comparison table with columns: install-config method, MachineConfig method
     - Rows:
       - When to use: Day 1 installation | Post-installation or complex staged configs
       - Complexity: Declarative in install-config.yaml | Requires manual base64-encoding and manifest creation
       - User experience: Simplified, consistent with other install-config options | More steps, prone to encoding errors
       - File placement: Automatic by installer in /etc/nmstate/openshift/ | Manual placement in installer manifests directory
       - Use cases: Standard deployments, VMware migrations | Day 2 changes, advanced configurations requiring runtime modification
       - Result: Identical - both create NMState files on nodes | Identical - both create NMState files on nodes
     - Recommendation: Use install-config method for day 1 deployments
     - Migration note: Existing MachineConfig-based configurations continue to work, no migration required
   - Prerequisites: Understanding of both methods from concept and procedure modules
   - Dependencies: Supports decision-making in parent topic

**User Story: Verify br-ex configuration after installation**

* **Verify br-ex bridge configuration after installation** (PROCEDURE)
   - JTBD category: Configure
   - Content journey phase: Evaluate
   - Audience: SysAdmin
   - Content outline:
     - Prerequisites:
       - OpenShift installation completed
       - Access to nodes via oc debug or SSH
     - Procedure:
       1. Access node using `oc debug node/<nodename>`
       2. Verify NMState file exists: `ls /etc/nmstate/openshift/<hostname>.yml`
       3. Check br-ex bridge status: `nmstatectl show br-ex`
       4. Verify OVS bridge structure: `ovs-vsctl show`
       5. Check machine-config application: `oc get machineconfigs | grep network-config`
       6. Review machine-config-daemon logs for errors
     - Expected results: br-ex bridge exists, has correct ports attached, interface is UP
     - Troubleshooting: Link to troubleshooting procedure if verification fails
   - Prerequisites: OpenShift cluster installed with hostConfig entries, cluster-admin access
   - Dependencies: Must follow installation procedure

* **Troubleshoot br-ex install-config configuration issues** (PROCEDURE)
   - JTBD category: Troubleshoot
   - Content journey phase: Adopt
   - Audience: SysAdmin
   - Content outline:
     - Common issues and resolutions:
       - **Issue**: br-ex bridge not created on node
         - Cause: Hostname mismatch between hostConfig and actual node hostname
         - Resolution: Verify node hostname matches install-config hostConfig entry exactly, check DNS resolution
       - **Issue**: Invalid NMState configuration errors in machine-config-daemon logs
         - Cause: NMState YAML syntax errors or invalid network interface references
         - Resolution: Validate NMState YAML syntax, verify interface names match hardware
       - **Issue**: Machine-config not applied to nodes
         - Cause: Machine-config-daemon failed to process NMState configuration
         - Resolution: Check machine-config-daemon logs, verify NMState files in /etc/nmstate/openshift/
       - **Issue**: br-ex bridge missing expected ports
         - Cause: Interface name in NMState config doesn't match physical interface
         - Resolution: Use `ip link` to list actual interfaces, correct networkConfig in install-config.yaml
       - **Issue**: Node networking loss after br-ex configuration
         - Cause: Single-interface configuration errors causing host connectivity loss
         - Resolution: Understand automatic rollback behavior, use console access to recover
     - Debugging techniques: Using nmstatectl, ovs-vsctl, machine-config-daemon logs
     - Recovery procedures: Re-running installer with corrected install-config.yaml, manual MachineConfig fixes for day 2
   - Prerequisites: Attempted installation with hostConfig, verification procedure completed
   - Dependencies: Requires understanding of verification procedure and expected results

## Updated Docs

* **modules/installation-configuration-parameters.adoc** (REFERENCE)
   - **Updates required**:
     - Add networking.hostConfig parameter to the networking parameters table
     - Field: `hostConfig`
     - Type: Array of HostConfigEntry objects
     - Description: Array of per-node NMState network configurations for customizing br-ex and other network interfaces at installation time. Each entry specifies a hostname and networkConfig with NMState YAML as embedded JSON. The installer automatically generates MachineConfig objects that place NMState files in /etc/nmstate/openshift/<hostname>.yml.
     - Example: Reference to new install-config hostConfig procedure module
   - **Justification**: This is the primary install-config parameter reference; users look here to discover available configuration options

* **modules/installation-bare-metal-config-yaml.adoc** (REFERENCE)
   - **Updates required**:
     - Add example networking.hostConfig entry to the sample install-config.yaml
     - Show minimal example: one hostConfig entry with hostname and networkConfig for br-ex
     - Add comment explaining this is for custom br-ex bridge configuration
   - **Justification**: Sample install-config files demonstrate usage patterns; users copy-paste from these examples

* **modules/creating-manifest-file-customized-br-ex-bridge.adoc** (PROCEDURE)
   - **Updates required**:
     - Add introductory note at top of module:
       - "For OpenShift 4.19 and later, you can configure br-ex at installation time using the networking.hostConfig parameter in install-config.yaml, which is simpler than this MachineConfig approach. See [link to new install-config procedure]."
       - "Use this MachineConfig method for post-installation configuration changes or advanced scenarios requiring runtime modification."
     - Keep existing procedure unchanged (remains valid for day 2 operations)
   - **Justification**: Users searching for br-ex configuration will find this existing module; we need to direct them to the simpler method while preserving the MachineConfig approach for day 2 use cases

* **modules/enabling-OVS-balance-slb-mode.adoc** (PROCEDURE)
   - **Updates required**:
     - Add note in prerequisites or introduction:
       - "For installation-time configuration, you can use the networking.hostConfig parameter in install-config.yaml. See [link to install-config procedure]."
       - "This procedure uses MachineConfig manifests, which is appropriate for post-installation configuration changes."
     - Update examples to show both methods as alternatives
   - **Justification**: OVS bonding is a common use case for br-ex; users need to know they can configure it at install time

* **modules/migrating-br-ex-bridge-nmstate.adoc** (PROCEDURE)
   - **Updates required**:
     - Add clarification note distinguishing day 1 vs day 2 approaches:
       - "This procedure is for migrating br-ex on existing clusters (day 2 operation) using NodeNetworkConfigurationPolicy."
       - "For new cluster installations (day 1), use the networking.hostConfig parameter in install-config.yaml. See [link to install-config procedure]."
   - **Justification**: Title contains "migrating" which implies day 2, but users may confuse this with initial installation; clarification prevents confusion

* **modules/installation-two-node-creating-manifest-custom-br-ex.adoc** (PROCEDURE)
   - **Updates required**:
     - Add note recommending install-config method for OpenShift 4.19+:
       - "For OpenShift 4.19 and later, you can configure br-ex using the networking.hostConfig parameter in install-config.yaml. See [link to install-config procedure]."
       - "This MachineConfig approach remains valid for OpenShift 4.18 and earlier, or for post-installation changes."
   - **Justification**: Two-node clusters are a specific deployment scenario; users need to know the simplified method applies to them

* **installing/installing_bare_metal/upi/installing-bare-metal.adoc** (ASSEMBLY)
   - **Updates required**:
     - Include new install-config hostConfig procedure module in the "Customizing the cluster" or "Network customization" section
     - Position before the MachineConfig-based br-ex module to indicate preference
     - Add assembly-level introduction explaining both methods are available with recommendation for install-config approach
   - **Justification**: Assembly structure guides users through installation workflow; new method should appear in logical sequence before manual alternatives

* **modules/creating-manifest-file-customized-br-ex-bridge-post.adoc** (PROCEDURE)
   - **Updates required**:
     - Add clarification that this is a post-installation procedure:
       - "This procedure uses NodeNetworkConfigurationPolicy for post-installation br-ex changes (day 2 operations)."
       - "For installation-time configuration (day 1), use the networking.hostConfig parameter in install-config.yaml. See [link to install-config procedure]."
   - **Justification**: Filename includes "post" but users may not notice; explicit clarification prevents confusion about when to use each method

## Gap Analysis

### Coverage gaps

**Gaps in existing content:**
1. **No install-config interface documentation**: Existing docs only cover MachineConfig manifests and post-installation NNCP approaches
2. **No comparison guidance**: Users have no way to understand when to use install-config vs MachineConfig methods
3. **No verification procedures**: Existing docs don't explain how to verify br-ex was created correctly from install-config
4. **No troubleshooting for install-config method**: Existing troubleshooting is for NNCP (day 2), not for install-time configuration failures

**User journey gaps identified:**
- **Expand phase**: Missing concept explaining the two methods and their relationship
- **Learn phase**: Missing procedure for using install-config interface
- **Evaluate phase**: Missing verification procedure and comparison reference
- **Adopt phase**: Missing troubleshooting for install-config method

### Currency issues

**Outdated content:**
1. **modules/creating-manifest-file-customized-br-ex-bridge.adoc**: Presents MachineConfig method as the only option, unaware of install-config interface
2. **modules/installation-configuration-parameters.adoc**: Missing networking.hostConfig parameter
3. **modules/installation-bare-metal-config-yaml.adoc**: Sample install-config.yaml doesn't show hostConfig option

### Completeness issues

**Incomplete procedures:**
1. Existing br-ex procedures lack verification steps
2. No end-to-end workflow from planning → configuration → verification → troubleshooting for the new install-config method
3. Missing prerequisites (hostname planning, DNS configuration, NMState syntax validation)

### Structural issues

**Module typing problems:**
1. Some existing br-ex modules mix concept and procedure content
2. No clear parent topic organizing the br-ex configuration job

**Missing assemblies:**
1. No assembly grouping br-ex configuration methods under a single JTBD-based parent topic

### User story completeness

**Main Job: Configure installation networking for virtualization workloads**
- ✅ User story planned: Configure br-ex using install-config interface
- ✅ User story planned: Verify br-ex configuration after installation
- ⚠️ Partial gap: Advanced configurations (bonding, VLANs) have examples in existing docs but need install-config interface versions

**Missing user stories:**
- None identified - requirements focus on installation-time configuration which is fully covered by planned modules

## Content Journey Phase Distribution

| Phase | Planned Modules | Gap Assessment |
|-------|----------------|----------------|
| **Expand** | 1 (Understanding br-ex configuration methods concept) | ✅ Adequate - introduces users to both methods |
| **Discover** | 2 (install-config parameter reference, parent topic) | ✅ Adequate - provides reference and overview |
| **Learn** | 1 (Configure br-ex procedure) | ✅ Adequate - core hands-on procedure |
| **Evaluate** | 2 (Verify procedure, comparison reference) | ✅ Adequate - supports decision-making and validation |
| **Adopt** | 1 (Troubleshooting procedure) | ✅ Adequate - addresses operational issues |

**Analysis**: Phase distribution is balanced. Learn phase is intentionally narrower (1 module) because the procedure is straightforward. Evaluate phase has more weight to support method selection and post-installation validation, which aligns with the user workflow.

## Relationship Analysis

**Requirements relationship classification:**

| Requirement Pair | Relationship Type | Notes |
|-----------------|------------------|-------|
| REQ-001 ↔ REQ-002 | Overlapping | Both address documenting the install-config interface; REQ-001 focuses on new modules, REQ-002 on updating existing content |
| REQ-001 ↔ REQ-003 | Sequential | REQ-003 (verification/troubleshooting) depends on REQ-001 (configuration) being documented first |
| REQ-001 ↔ REQ-004 | Complementary | REQ-004 provides comparison guidance that helps users understand when to use the interface documented in REQ-001 |
| REQ-002 ↔ REQ-003 | Parallel/Sibling | Both update existing content for different purposes (method awareness vs verification) |
| REQ-002 ↔ REQ-004 | Overlapping | Both involve updating existing docs to incorporate method comparison guidance |
| REQ-003 ↔ REQ-004 | Parallel/Sibling | Verification/troubleshooting is distinct from method comparison |

**Theme clustering:**

| Theme | Issues Included | Overlap Risk | Recommended Ownership |
|-------|----------------|-------------|----------------------|
| **Install-config interface core documentation** | REQ-001, REQ-002 | High - both document the same interface from different angles | Main job assembly: "Configure installation networking for virtualization workloads" |
| **Verification and validation** | REQ-003 | Low - distinct troubleshooting focus | User story: "Verify br-ex configuration after installation" |
| **Method comparison and guidance** | REQ-004, parts of REQ-001 | Medium - comparison appears in multiple requirements | Concept module: "Understanding br-ex configuration methods" and reference module: "Comparison: install-config vs MachineConfig" |

**Consolidation recommendations:**
1. **Merge REQ-001 and REQ-002 procedure modules**: Both requirements describe configuring br-ex using install-config; consolidate into single procedure module
2. **Create single comparison module**: REQ-001, REQ-002, and REQ-004 all mention comparison guidance; consolidate into one reference module and one concept module
3. **Keep verification and troubleshooting separate**: REQ-003 is distinct enough to warrant separate procedure modules

## Module and Assembly Specifications

### Assembly Structure

**Assembly: Configure network bridges for virtualization workloads**
- JTBD category: Configure
- Main job: Configure installation networking for virtualization workloads
- Content journey phases covered: Expand → Discover → Learn → Evaluate → Adopt
- Reading order:
  1. Parent topic: Configure network bridges for virtualization at installation time (CONCEPT)
  2. Understanding br-ex configuration methods (CONCEPT) - introduces both methods
  3. install-config networking.hostConfig parameter reference (REFERENCE) - API details
  4. Configure br-ex bridge at installation using install-config.yaml (PROCEDURE) - hands-on configuration
  5. Comparison: install-config vs MachineConfig methods for br-ex (REFERENCE) - decision support
  6. Verify br-ex bridge configuration after installation (PROCEDURE) - validation
  7. Troubleshoot br-ex install-config configuration issues (PROCEDURE) - problem resolution

**Shared prerequisites:**
- Understanding of Kubernetes networking concepts (pods, services, external connectivity)
- Familiarity with NMState YAML syntax for network configuration
- Access to DNS infrastructure for node hostname resolution
- Cluster-admin access for verification and troubleshooting procedures

**Assembly integration:**
- Integrates into existing "Installing on bare metal" and "Installing on platform X" guides in the "Network customization" sections
- Replaces or supplements existing MachineConfig-based br-ex modules with parent topic approach
- Cross-references to OpenShift Virtualization documentation for VM networking use cases

### Implementation Order

**Phase 1: Core interface documentation (Highest priority)**
1. install-config networking.hostConfig parameter reference (REFERENCE) - Foundation for all other modules
2. Configure br-ex bridge at installation using install-config.yaml (PROCEDURE) - Core user task
3. Update modules/installation-configuration-parameters.adoc - Enables discovery via existing reference

**Phase 2: Guidance and comparison (High priority)**
4. Understanding br-ex configuration methods (CONCEPT) - Helps users choose the right method
5. Comparison: install-config vs MachineConfig methods for br-ex (REFERENCE) - Decision support
6. Update modules/creating-manifest-file-customized-br-ex-bridge.adoc - Directs existing users to new method

**Phase 3: Validation and operations (Medium priority)**
7. Verify br-ex bridge configuration after installation (PROCEDURE) - Post-installation validation
8. Troubleshoot br-ex install-config configuration issues (PROCEDURE) - Problem resolution
9. Update modules/enabling-OVS-balance-slb-mode.adoc - Advanced use case coverage

**Phase 4: Assembly and remaining updates (Medium priority)**
10. Parent topic: Configure network bridges for virtualization at installation time (CONCEPT) - JTBD parent topic
11. Update remaining modules: installation-bare-metal-config-yaml.adoc, migrating-br-ex-bridge-nmstate.adoc, etc.
12. Update assemblies to incorporate new parent topic structure

**Rationale for ordering:**
- Reference documentation first enables writers to understand the API structure
- Core procedure immediately provides user value
- Updates to existing high-traffic modules spread awareness
- Comparison and concept modules support informed decision-making
- Verification and troubleshooting come after configuration is documented
- Parent topic and assembly restructuring comes last to avoid blocking individual module delivery

## Module Dependency Map

```
Parent Topic: Configure network bridges for virtualization at installation time
├── Depends on: None (entry point)
└── Enables: All child modules in assembly

Understanding br-ex configuration methods (CONCEPT)
├── Depends on: Parent topic
└── Enables: Informed reading of procedure and reference modules

install-config networking.hostConfig parameter reference (REFERENCE)
├── Depends on: None (pure reference, can be read independently)
└── Enables: Configure br-ex procedure, comparison reference

Configure br-ex bridge at installation using install-config.yaml (PROCEDURE)
├── Depends on: install-config parameter reference, understanding br-ex methods concept
└── Enables: Verify procedure, troubleshooting procedure

Comparison: install-config vs MachineConfig methods for br-ex (REFERENCE)
├── Depends on: Understanding br-ex methods concept, install-config parameter reference
└── Enables: Informed method selection

Verify br-ex bridge configuration after installation (PROCEDURE)
├── Depends on: Configure br-ex procedure (must configure before verifying)
└── Enables: Troubleshooting procedure

Troubleshoot br-ex install-config configuration issues (PROCEDURE)
├── Depends on: Configure br-ex procedure, Verify procedure
└── Enables: Problem resolution
```

## Content Sources and Evidence

### JIRA tickets analyzed
- [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514): Primary documentation epic with JTBD statements and acceptance criteria
- [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184): Parent feature describing install-config interface goals and user experience improvements
- [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592): Engineering epic with implementation context
- [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249): Child story for modifying existing NMState br-ex docs

### Pull requests analyzed
- [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795): API design for networking.hostConfig, includes user stories and motivation
- [Implementation PR #10251](https://github.com/openshift/installer/pull/10251): Installer code implementing hostConfig field and MachineConfig generation

### Code files reviewed
- `pkg/types/installconfig.go:413-420`: HostConfig field definition in Networking struct
- `pkg/types/installconfig.go:277-285`: HostConfigEntry struct with Hostname and NetworkConfig fields
- `pkg/asset/machines/machineconfig/networkconfig.go:56-70`: MachineConfig generation logic, file placement in /etc/nmstate/openshift/<hostname>.yml
- `data/data/install.openshift.io_installconfigs.yaml:4430-4459`: API schema for networking.hostConfig array

### Existing documentation reviewed
- `modules/creating-manifest-file-customized-br-ex-bridge.adoc`: Current MachineConfig-based procedure
- `modules/installation-configuration-parameters.adoc`: Install-config parameter reference
- `modules/migrating-br-ex-bridge-nmstate.adoc`: Post-installation br-ex migration
- `modules/enabling-OVS-balance-slb-mode.adoc`: OVS bonding configuration

## Prioritization Rationale

**Critical priority:**
- None - feature is not blocking core functionality

**High priority:**
1. **REQ-001: Core interface documentation**: Users cannot adopt the new method without procedure and reference documentation. This is the primary user-facing value.
2. **install-config parameter reference**: Foundation for understanding the API structure; enables all other content.
3. **Configure br-ex procedure**: Core hands-on task users need to perform.

**Medium priority:**
4. **REQ-002: Update existing docs**: Prevents users from missing the new simplified method when they find existing MachineConfig procedures.
5. **REQ-003: Verification and troubleshooting**: Important for operational success but users can proceed without it (though with higher risk of undetected failures).
6. **Comparison and concept modules**: Helpful for decision-making but users can infer benefits from procedure alone.

**Low priority:**
7. **REQ-004: Detailed comparison**: Nice-to-have for comprehensive understanding but core value delivered by updates in REQ-002.
8. **Assembly restructuring**: Organizational improvement but doesn't block individual module delivery.

**Impact consistency check:**
- REQ-001 (High impact) → High priority modules ✅
- REQ-002 (Medium impact) → Medium priority updates ✅
- REQ-003 (Medium impact) → Medium priority procedures ✅
- REQ-004 (Low impact) → Low priority comparison ✅

## JTBD Validation

**Job statement validation:**

✅ **Job 1** clearly identifies situation (deploying OpenShift with custom bridge networking), motivation (configure br-ex declaratively), and outcome (ensure VM connectivity without manual manifest manipulation).

✅ **Job 2** clearly identifies situation (installation complete with custom br-ex), motivation (verify correctness), and outcome (ensure proper networking before production).

**Hierarchy validation:**

✅ **Category**: Configure - Correct, users are configuring installation-time networking.

✅ **Main Jobs**:
- "Configure installation networking for virtualization workloads" - Outcome-focused, avoids feature names (br-ex mentioned descriptively but focus is on the user goal)
- "Verify and troubleshoot network configurations" - Outcome-focused, describes what user wants to accomplish

✅ **User Stories**:
- "Configure br-ex using install-config interface" - Specific implementation path
- "Verify br-ex configuration after installation" - Specific implementation path

**Title validation:**

✅ Outcome-driven titles:
- "Configure network bridges for virtualization at installation time" (not "br-ex bridge API")
- "Understanding br-ex configuration methods" (not "NMState install-config interface")
- "Verify br-ex bridge configuration after installation" (not "br-ex validation commands")

✅ Active phrasing:
- "Configure," "Verify," "Troubleshoot" - all imperative verbs

✅ Natural language:
- Avoids product-specific jargon like "hostConfig API" in favor of "install-config interface"
- Uses industry-standard terms (NMState, br-ex) appropriately

**Persona separation validation:**

✅ **Single persona focus**: All modules target SysAdmin (with IT Operations Leader as secondary influencer). No persona mixing - the job is clearly owned by installation/operations roles, not developers.

No multi-persona capability detected - this is an installation-time configuration interface used exclusively by cluster administrators, not a capability consumed by different personas at different lifecycle stages.

## Self-Review Verification

| Check | Status | Notes |
|-------|--------|-------|
| **No placeholder syntax** | ✅ | All [REPLACE], [TODO], [TBD] markers removed and replaced with actual content |
| **No hallucinated content** | ✅ | All recommendations traceable to requirements, JIRA tickets, PRs, or code files |
| **Source traceability** | ✅ | Every module links to JIRA tickets, PRs, or existing docs |
| **No sensitive information** | ✅ | No hostnames, passwords, IPs, internal URLs, or tokens in output |
| **Persona limit** | ✅ | 2 personas identified (SysAdmin, IT Operations Leader) - within 3-persona limit |
| **Template completeness** | ✅ | All required sections populated: support status, importance, personas, JTBD, workflow, steps, demo, disconnected, contacts, release note, links, new docs, updated docs |
| **Impact consistency** | ✅ | High-impact REQ-001 → high priority modules; Medium-impact REQ-002/003 → medium priority; Low-impact REQ-004 → low priority |
| **Journey coverage** | ✅ | All 5 phases mapped: Expand (concept), Discover (reference + parent topic), Learn (procedure), Evaluate (comparison + verify), Adopt (troubleshooting) |
| **JIRA description ready** | ✅ | 5 required sections fully populated with no [REPLACE] markers: JTBD statement, workflow, contacts, new docs, updated docs |
| **Persona separation** | ✅ | Single-persona capability (SysAdmin) - no admin/user mixing, no separate job statements needed |
| **DITA structure** | ⚠️ | Output format is AsciiDoc (adoc) - DITA structure section intentionally omitted per instructions |

**Format-specific note**: Output format is AsciiDoc (.adoc), not DITA, so DITA structure planning (topic map, keydefs, conref, profiling) is not included per the framework instructions.

## JIRA Ticket Description (5 sections only)

**Note**: Copy these five sections to the JIRA ticket description. The full plan attachment contains remaining detail.

---

## What is the main JTBD? What user goal is being accomplished? What pain point is being avoided?

**Job Statement 1 (Installation - Day 1):**
When deploying an OpenShift cluster with custom bridge networking requirements for virtualization workloads, I want to configure the br-ex bridge at installation time using a declarative interface in install-config.yaml, so that I can ensure proper network connectivity for VMs without manually encoding NMState YAML or creating MachineConfig manifests, while avoiding configuration errors and reducing installation complexity.

**Job Statement 2 (Verification and Troubleshooting - Day 1):**
When an OpenShift cluster installation completes with custom br-ex networking configurations, I want to verify the bridge was created correctly and troubleshoot any configuration issues, so that I can ensure nodes are properly networked for VM workloads before moving to production, while avoiding network connectivity failures that could impact cluster stability.

## How does the JTBD(s) relate to the overall real-world workflow for the user?

**Real-world workflow context:**

1. **Planning phase**: Administrators design the cluster network topology, identifying which nodes require br-ex bridges for virtualization workloads. For single-interface deployments (common in VMware migrations), they plan to share the physical interface between br-ex and the host network.

2. **Installation preparation**: Administrators create the install-config.yaml file with cluster specifications. This is where they traditionally would have stopped and switched to a separate manual process of creating MachineConfig manifests.

3. **Network configuration (NEW SIMPLIFIED PATH)**: Using the networking.hostConfig interface, administrators add br-ex NMState configurations directly to install-config.yaml, specifying per-node networking using hostname and networkConfig fields. The installer automatically handles base64-encoding and MachineConfig generation.

4. **Installation execution**: The OpenShift installer generates machine-configs (99-network-config-master and 99-network-config-worker) from the hostConfig entries and applies them during node bootstrapping. NMState YAML files are placed in /etc/nmstate/openshift/<hostname>.yml on each node.

5. **Verification (Day 1)**: After installation completes, administrators verify br-ex bridge creation using nmstatectl and ovs-vsctl commands on nodes to ensure networking is correct before deploying VM workloads.

6. **Day 2 operations**: Administrators deploy OpenShift Virtualization and create VMs that use the br-ex bridge for external network connectivity. If network issues arise, they troubleshoot using the verification procedures.

**Relationship to broader workflow:**

The install-config interface streamlines the installation workflow by keeping all installation-time configuration in a single declarative file (install-config.yaml), rather than requiring administrators to context-switch to a separate manual process of encoding YAML and creating machine-config files. This aligns with how administrators configure other installation-time customizations (proxy settings, SSH keys, platform-specific options) and reduces the risk of errors from manual base64-encoding.

For VMware migration scenarios, this simplified interface matches the declarative networking configuration patterns VMware administrators are familiar with, reducing the learning curve and accelerating OpenShift adoption.

## Who can provide information and answer questions?

- **PM**: Determined from JIRA assignee/reporter fields in [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184)
- **Technical SME**: OpenShift Networking team (OPNET) - contact via [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592)
- **UX**: Not applicable for this installer API enhancement

## New Docs

* **Configure network bridges for virtualization at installation time** (CONCEPT)
   - Parent topic explaining why custom br-ex configurations are needed for virtualization workloads and overview of configuration methods available
   - Content: What (br-ex for VM connectivity), Why (single-interface VM deployments), How the product helps (install-config vs MachineConfig methods), High-level steps (Plan → Configure → Verify)

* **Understanding br-ex configuration methods** (CONCEPT)
   - Comparison of install-config hostConfig (day 1) vs MachineConfig manifests (day 2)
   - When to use each method, installer processing of hostConfig entries, relationship to OpenShift Virtualization

* **install-config networking.hostConfig parameter reference** (REFERENCE)
   - Parameter structure: Array of HostConfigEntry objects with hostname (string) and networkConfig (object) fields
   - Data types, validation rules, examples (single-node and multi-node)
   - Automatic processing: installer generates 99-network-config-{master|worker} MachineConfigs, places files in /etc/nmstate/openshift/<hostname>.yml

* **Configure br-ex bridge at installation using install-config.yaml** (PROCEDURE)
   - Prerequisites: Planned hostnames with DNS, interface names, validated NMState YAML
   - Steps: Create install-config.yaml → Add networking.hostConfig array → Create HostConfigEntry per node → Embed NMState as JSON → Run installer
   - Examples: Single-interface config, bonded interfaces

* **Comparison: install-config vs MachineConfig methods for br-ex** (REFERENCE)
   - Comparison table: When to use, Complexity, User experience, File placement, Use cases, Result
   - Recommendation: Use install-config for day 1 deployments

* **Verify br-ex bridge configuration after installation** (PROCEDURE)
   - Steps: Access node → Verify NMState file exists → Check br-ex status (nmstatectl show br-ex) → Verify OVS structure (ovs-vsctl show) → Check machine-config application
   - Expected results, link to troubleshooting

* **Troubleshoot br-ex install-config configuration issues** (PROCEDURE)
   - Common issues: br-ex not created (hostname mismatch), invalid NMState syntax, machine-config not applied, missing ports, networking loss
   - Resolutions: Verify hostname/DNS, validate NMState YAML, check logs, correct interface names, understand rollback behavior

## Updated Docs

* **modules/installation-configuration-parameters.adoc** (REFERENCE)
   - Add networking.hostConfig parameter to networking parameters table with field description, type (Array of HostConfigEntry), usage notes

* **modules/installation-bare-metal-config-yaml.adoc** (REFERENCE)
   - Add example networking.hostConfig entry to sample install-config.yaml with comment explaining br-ex customization use case

* **modules/creating-manifest-file-customized-br-ex-bridge.adoc** (PROCEDURE)
   - Add introductory note: "For OpenShift 4.19+, use networking.hostConfig in install-config.yaml (simpler). Use this MachineConfig method for post-installation changes or advanced scenarios."

* **modules/enabling-OVS-balance-slb-mode.adoc** (PROCEDURE)
   - Add note in prerequisites: "For installation-time configuration, use networking.hostConfig in install-config.yaml. This procedure uses MachineConfig for post-installation changes."

* **modules/migrating-br-ex-bridge-nmstate.adoc** (PROCEDURE)
   - Add clarification: "This procedure is for day 2 migration using NNCP. For day 1 installation, use networking.hostConfig in install-config.yaml."

* **modules/installation-two-node-creating-manifest-custom-br-ex.adoc** (PROCEDURE)
   - Add note: "For OpenShift 4.19+, use networking.hostConfig in install-config.yaml. This MachineConfig approach remains valid for 4.18 and earlier, or post-installation changes."

* **installing/installing_bare_metal/upi/installing-bare-metal.adoc** (ASSEMBLY)
   - Include new install-config hostConfig procedure in "Network customization" section, positioned before MachineConfig-based module

* **modules/creating-manifest-file-customized-br-ex-bridge-post.adoc** (PROCEDURE)
   - Add clarification: "This uses NNCP for post-installation changes (day 2). For installation-time configuration (day 1), use networking.hostConfig in install-config.yaml."
