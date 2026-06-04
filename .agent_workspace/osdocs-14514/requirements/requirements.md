# Documentation Requirements

**Source**: Docs for OCPSTRAT-2184 Add an install-config interface to the previously implemented feature for creating br-ex using NMState.
**Date**: 2026-06-04
**Release/Sprint**: 4.19

## Summary

- Total requirements analyzed: 4
- New modules needed: 7
- Existing modules to update: 8
- Breaking changes requiring docs: 0

## Requirements by priority

### High

#### REQ-001: Document new install-config interface for br-ex NMState configuration
- **Source**: [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514) | [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184) | [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795) | [Implementation PR #10251](https://github.com/openshift/installer/pull/10251)
- **Summary**: OpenShift 4.19 introduces a new install-config interface for configuring the br-ex bridge using NMState, replacing the manual base64-encoding and machine-config creation process. Users can now specify host network configurations directly in the networking.hostConfig section of install-config.yaml, which the installer automatically converts to MachineConfig objects with NMState configuration files placed in /etc/nmstate/openshift/<hostname>.yml. This streamlines br-ex configuration for virtualization workloads that require single-interface networking and provides a more user-friendly experience for deployers migrating from VMware or managing complex network topologies.
- **User impact**: Users installing OpenShift clusters with custom br-ex networking requirements can now provide NMState configurations directly in install-config.yaml instead of manually creating base64-encoded MachineConfig manifests. This significantly simplifies deployment for virtualization clusters where VMs share the same interface as br-ex, reduces configuration errors from manual encoding, and provides a more intuitive interface similar to other installation customization options.
- **Documentation action**:
  - [ ] Create `modules/installation-config-hostconfig-parameter.adoc` (REFERENCE) - Add hostConfig parameter to networking section reference documentation
  - [ ] Create `modules/configuring-br-ex-install-config.adoc` (PROCEDURE) - Procedure for configuring br-ex using install-config.yaml networking.hostConfig
  - [ ] Update `modules/installation-configuration-parameters.adoc` (REFERENCE) - Add networking.hostConfig parameter to network parameters table
  - [ ] Update `modules/installation-bare-metal-config-yaml.adoc` (REFERENCE) - Add hostConfig example to sample install-config.yaml
  - [ ] Create `modules/comparison-br-ex-configuration-methods.adoc` (CONCEPT) - Explain differences between install-config hostConfig vs MachineConfig method
  - [ ] Update `modules/creating-manifest-file-customized-br-ex-bridge.adoc` (PROCEDURE) - Add note about new install-config method and when to use each approach
- **Acceptance criteria**:
  - [ ] Users can configure br-ex at installation time using networking.hostConfig in install-config.yaml
  - [ ] Documentation explains the structure of HostConfigEntry objects with hostname and networkConfig fields
  - [ ] Users understand when to use install-config.yaml method vs manual MachineConfig manifests
  - [ ] Documentation provides example NMState YAML for common br-ex configurations (single interface, bonding)
  - [ ] Reference documentation lists networking.hostConfig parameter with field descriptions and data types
  - [ ] Users can verify br-ex configuration by checking /etc/nmstate/openshift/<hostname>.yml files on nodes
  - [ ] Documentation clarifies that configurations are applied per-node by hostname and not merged with cluster.yml
  - [ ] Users understand platform compatibility (platform-agnostic for clusters deployed with installer)
  - [ ] Documentation includes prerequisites such as proper hostname planning and NMState syntax validation
- **References**:
  - [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514): Documentation epic with JTBD statements and content outline
  - [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184): Parent feature tracking install-config interface for NMState br-ex
  - [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795): Enhancement proposal defining install-config interface and API structure
  - [Implementation PR #10251](https://github.com/openshift/installer/pull/10251): Installer implementation adding networking.hostConfig field and MachineConfig generation
  - Enhancement doc lines 130-156: Install-config YAML structure: networking.hostConfig array with name and networkConfig fields
  - pkg/types/installconfig.go:413-420: HostConfig field definition in Networking struct
  - pkg/types/installconfig.go:277-285: HostConfigEntry struct with Hostname and NetworkConfig fields
  - pkg/asset/machines/machineconfig/networkconfig.go:56-70: MachineConfig generation logic placing configs in /etc/nmstate/openshift/<hostname>.yml
  - modules/creating-manifest-file-customized-br-ex-bridge.adoc: Existing procedure documenting MachineConfig method for br-ex customization

### Medium

#### REQ-002: Update existing br-ex docs to reference new install-config method
- **Source**: [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249)
- **Summary**: OpenShift 4.19 introduces a new install-config interface for configuring br-ex bridges using NMState, simplifying the previous machine-config based approach. Users can now specify NMState configurations directly in the networking.hostConfig section of install-config.yaml, eliminating the need for manual base64 encoding and MachineConfig manifest creation. The installer automatically generates the necessary machine-configs from the hostConfig entries, writing NMState YAML files to /etc/nmstate/openshift/<hostname>.yml on each node. This enhancement improves the user experience for OpenShift Virtualization and bare-metal deployments where custom br-ex configurations are required.
- **User impact**: Users installing OpenShift 4.19+ on bare-metal platforms with custom br-ex networking requirements gain a significantly simpler configuration workflow. Instead of manually base64-encoding NMState YAML and creating MachineConfig manifests, users can now provide NMState configurations directly in install-config.yaml under networking.hostConfig with hostname and networkConfig pairs. Existing users with machine-config based br-ex configurations can continue using that method, but should be aware of the new simplified approach for future installations. Documentation must guide users on when to use each method and provide clear migration guidance.
- **Documentation action**:
  - [ ] Update `modules/creating-manifest-file-customized-br-ex-bridge.adoc` (PROCEDURE) - Add new section describing the install-config hostConfig method as the recommended approach, keep machine-config method as alternative
  - [ ] Update `modules/enabling-OVS-balance-slb-mode.adoc` (PROCEDURE) - Update to reference the new install-config hostConfig method alongside the machine-config approach
  - [ ] Create `modules/install-config-networking-hostconfig-reference.adoc` (REFERENCE) - Document the networking.hostConfig parameter structure including hostname and networkConfig fields
  - [ ] Create `modules/configuring-br-ex-install-config-hostconfig.adoc` (PROCEDURE) - New procedure showing how to configure br-ex using the install-config hostConfig method
  - [ ] Update `modules/migrating-br-ex-bridge-nmstate.adoc` (PROCEDURE) - Add note about the new install-config method for future installations
  - [ ] Update `installing/installing_bare_metal/upi/installing-bare-metal.adoc` (ASSEMBLY) - Include new hostConfig procedure module before the machine-config approach
- **Acceptance criteria**:
  - [ ] Users can configure br-ex bridges at install time using the networking.hostConfig parameter in install-config.yaml
  - [ ] Documentation clearly identifies the install-config hostConfig method as the recommended approach for OpenShift 4.19+
  - [ ] Documentation provides complete examples of the networking.hostConfig parameter structure with hostname and networkConfig fields
  - [ ] Users understand the relationship between install-config hostConfig entries and the generated /etc/nmstate/openshift/<hostname>.yml files
  - [ ] Documentation clarifies that the machine-config approach remains supported for specific use cases
  - [ ] Users can determine which method (install-config hostConfig vs machine-config) is appropriate for their deployment scenario
  - [ ] Examples demonstrate both single-node and multi-node hostConfig configurations
  - [ ] Documentation explains the automatic machine-config generation performed by the installer from hostConfig entries
- **References**:
  - [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249): Documentation tracking ticket
  - [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184): Parent feature: Add install-config interface to NMState br-ex
  - [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592): Engineering epic for install-config interface
  - [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795): Enhancement proposal: nmstate-network-install-config
  - [Installer PR #10251](https://github.com/openshift/installer/pull/10251): Implementation: install.openshift.io networking.hostConfig API and ForNetworkConfig function
  - pkg/asset/machines/machineconfig/networkconfig.go: Installer code that converts hostConfig entries to MachineConfig manifests with base64-encoded NMState YAML
  - data/data/install.openshift.io_installconfigs.yaml:4430-4459: API schema definition for networking.hostConfig array with hostname and networkConfig fields
  - modules/creating-manifest-file-customized-br-ex-bridge.adoc: Existing install-time br-ex documentation using machine-config approach
  - modules/enabling-OVS-balance-slb-mode.adoc: Existing documentation showing machine-config method for OVS bonding

#### REQ-003: Document verification and troubleshooting for br-ex install-config
- **Source**: [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514)
- **Summary**: This requirement addresses documentation for verifying successful configuration and troubleshooting common issues when using the new install-config interface for br-ex bridge creation with NMState. The feature simplifies the previous machine-config approach by allowing users to specify NMState configurations directly in install-config.yaml under networking.hostConfig, which the installer then automatically base64-encodes and places in machine-config manifests. Users need clear guidance on how to verify the br-ex bridge was created correctly and how to resolve configuration failures.
- **User impact**: Users deploying OpenShift clusters with virtualization workloads, particularly those migrating from VMware who expect simplified network configuration, need to verify that their br-ex bridge configurations applied correctly at install time. Without proper verification and troubleshooting guidance, users may experience network connectivity issues, failed installations, or inability to run VM workloads on nodes with single interfaces. This is especially critical since the new install-config method applies at day 0, unlike the previous post-installation machine-config approach.
- **Documentation action**:
  - [ ] Create `proc-verifying-br-ex-install-config.adoc` (PROCEDURE) - Verification steps for confirming br-ex bridge was created successfully from install-config hostConfig
  - [ ] Create `proc-troubleshooting-br-ex-install-config.adoc` (PROCEDURE) - Troubleshooting common issues with install-config hostConfig br-ex creation
  - [ ] Update `modules/installation-configuration-parameters.adoc` (REFERENCE) - Add networking.hostConfig parameter documentation with hostname and networkConfig fields
  - [ ] Update `modules/enabling-OVS-balance-slb-mode.adoc` (PROCEDURE) - Update to reference new install-config hostConfig method as preferred approach
  - [ ] Create `con-install-config-vs-machineconfig-br-ex.adoc` (CONCEPT) - Explain differences between install-config hostConfig method vs machine-config method, when to use each
- **Acceptance criteria**:
  - [ ] Users can verify br-ex bridge was created correctly using nmstatectl show br-ex command
  - [ ] Users can verify br-ex bridge configuration using ovs-vsctl show command
  - [ ] Users understand how to check machine-config application status during installation
  - [ ] Users can identify and resolve common configuration errors such as missing interfaces, invalid NMState syntax, or incorrect hostname references
  - [ ] Users understand automatic rollback behavior when br-ex configuration fails
  - [ ] Users can access examples showing proper install-config.yaml hostConfig syntax with hostname and networkConfig fields
  - [ ] Documentation clarifies that hostConfig applies to both master and worker nodes via separate machine-configs (99-network-config-master and 99-network-config-worker)
  - [ ] Users understand the base64 encoding is handled automatically by the installer, unlike manual machine-config approach
  - [ ] Documentation includes troubleshooting for DNS connectivity issues in disconnected environments specific to install-time configuration
- **References**:
  - [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514): Main documentation epic with mini content journey defining verification and troubleshooting as key user steps
  - [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184): Parent feature ticket describing install-config interface to simplify br-ex creation vs machine-config method
  - [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592): Engineering epic confirming need to modify existing NMState br-ex docs for new simplified method
  - [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249): Child story for modifying NMState br-ex docs to use new install-config method
  - [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795): Enhancement proposal defining networking.hostConfig array with hostname and networkConfig fields
  - [Implementation PR #10251](https://github.com/openshift/installer/pull/10251): Installer implementation showing ForNetworkConfig creating machine-configs with base64-encoded NMState YAML in /etc/nmstate/openshift/<hostname>.yml
  - pkg/asset/machines/machineconfig/networkconfig.go: Code creating 99-network-config-{master|worker} machine-configs with NMState files in /etc/nmstate/openshift/<hostname>.yml
  - data/data/install.openshift.io_installconfigs.yaml:9-29: Schema defining hostConfig array with hostname (string) and networkConfig (x-kubernetes-preserve-unknown-fields) properties

### Low

#### REQ-004: Document comparison between machine-config and install-config methods
- **Source**: [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514) | [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184)
- **Summary**: OpenShift 4.19 introduces a new install-config interface (networking.hostConfig) that simplifies NMState br-ex bridge configuration by automatically handling base64-encoding and machine-config generation. This eliminates the manual process of encoding NMState YAML and placing machine-config manifests in the installer's manifests directory. Users need guidance on when to use each method and how the new approach improves the user experience, particularly for virtualization workloads.
- **User impact**: Users installing OpenShift clusters with custom br-ex bridge configurations (common for virtualization workloads) will have a simpler, more declarative workflow using install-config.yaml instead of manually creating machine-config manifests. VMware users transitioning to OpenShift will find the new interface more familiar. Existing users need to understand the benefits of migrating to the new method.
- **Documentation action**:
  - [ ] Create `con-nmstate-br-ex-configuration-methods.adoc` (CONCEPT) - Explain the two methods for configuring br-ex with NMState: install-config interface (new in 4.19) vs machine-config manifests
  - [ ] Create `ref-comparison-install-config-vs-machine-config-nmstate.adoc` (REFERENCE) - Comparison table showing differences: simplicity, when to use each, limitations, day-1 vs day-2 configuration
  - [ ] Create `proc-configuring-br-ex-install-config-nmstate.adoc` (PROCEDURE) - New procedure for using networking.hostConfig in install-config.yaml
  - [ ] Update `migrating-br-ex-bridge-nmstate.adoc` (PROCEDURE) - Add note distinguishing day-1 install-config method from day-2 post-installation migration
  - [ ] Update `creating-manifest-file-customized-br-ex-bridge-post.adoc` (PROCEDURE) - Add context about when to use machine-config method (post-installation) vs install-config method (during installation)
  - [ ] Update `installation-configuration-parameters.adoc` (REFERENCE) - Add documentation for networking.hostConfig parameter with hostname and networkConfig fields
- **Acceptance criteria**:
  - [ ] Users can understand the key differences between the install-config interface and machine-config manifest approach
  - [ ] Users can identify when to use install-config method (day-1 installation) vs machine-config method (day-2 post-installation)
  - [ ] Users recognize that the install-config method eliminates manual base64-encoding and manifest directory manipulation
  - [ ] Documentation includes examples showing both methods side-by-side to highlight simplification benefits
  - [ ] VMware users and virtualization cluster deployers understand why the new method is recommended for their use cases
  - [ ] Users understand that both methods result in the same NMState configuration files in /etc/nmstate/openshift/ on nodes
  - [ ] Comparison table clearly shows benefits: simpler workflow, no manual encoding, better integration with installer
- **References**:
  - [OSDOCS-14514 Description](https://redhat.atlassian.net/browse/OSDOCS-14514): Mini content journey specifically calls out comparison module showing benefits of new install-config interface
  - [OCPSTRAT-2184 Description](https://redhat.atlassian.net/browse/OCPSTRAT-2184): Feature goal states machine-config method 'requires manual base64 encoding and creation of the machine-config, which is then passed to the installer via the manifests directory' and is 'not a great user experience'
  - [OSDOCS-18249 Comment](https://redhat.atlassian.net/browse/OSDOCS-18249): Engineering confirms 'we need to modify the NMState br-ex docs to use the new method. It shouldn't require a ton of work since we're simplifying the process'
  - [OPNET-592 Comment](https://redhat.atlassian.net/browse/OPNET-592): Engineering epic describes motivation: 'VMWare users are accustomed to simplified network configuration interfaces and won't want to deal with the additional complexity we currently have'
  - [Enhancement Proposal: nmstate-network-install-config.md](https://github.com/openshift/enhancements/pull/1795): Lines 36-54: Describes install-config interface that does base64-encoding in installer to improve user experience. Lines 159-161: Shows networking.hostConfig structure with hostname and networkConfig fields
  - [Installer Implementation: pkg/types/installconfig.go](https://github.com/openshift/installer/pull/10251): Lines 263-268: Defines HostConfig field with HostConfigEntry type. Lines 277-286: Shows HostConfigEntry struct with Hostname string and NetworkConfig JSON
  - [Installer Implementation: pkg/asset/machines/machineconfig/networkconfig.go](https://github.com/openshift/installer/pull/10251): Lines 56-77: ForNetworkConfig function creates machine-config from HostConfig entries, base64-encodes NMState YAML, writes to /etc/nmstate/openshift/<hostname>.yml
  - migrating-br-ex-bridge-nmstate.adoc: Existing procedure showing manual base64-encoding workflow: 'Use the cat command to base64-encode the contents of the NMState configuration file' then create MachineConfig manifest
  - creating-manifest-file-customized-br-ex-bridge-post.adoc: Existing post-installation procedure using NodeNetworkConfigurationPolicy CR with NMState config

## Documentation scope

### New documentation needed

| Requirement | Scope | References |
|-------------|-------|------------|
| REQ-001 | Create modules/installation-config-hostconfig-parameter.adoc (REFERENCE), modules/configuring-br-ex-install-config.adoc (PROCEDURE), modules/comparison-br-ex-configuration-methods.adoc (CONCEPT) | OSDOCS-14514, OCPSTRAT-2184, Enhancement PR #1795, Implementation PR #10251 |
| REQ-002 | Create modules/install-config-networking-hostconfig-reference.adoc (REFERENCE), modules/configuring-br-ex-install-config-hostconfig.adoc (PROCEDURE) | OSDOCS-18249 |
| REQ-003 | Create proc-verifying-br-ex-install-config.adoc (PROCEDURE), proc-troubleshooting-br-ex-install-config.adoc (PROCEDURE), con-install-config-vs-machineconfig-br-ex.adoc (CONCEPT) | OSDOCS-14514 |
| REQ-004 | Create con-nmstate-br-ex-configuration-methods.adoc (CONCEPT), ref-comparison-install-config-vs-machine-config-nmstate.adoc (REFERENCE), proc-configuring-br-ex-install-config-nmstate.adoc (PROCEDURE) | OSDOCS-14514, OCPSTRAT-2184 |

### Existing documentation to update

| Requirement | What changed | References |
|-------------|-------------|------------|
| REQ-001 | Update modules/installation-configuration-parameters.adoc, modules/installation-bare-metal-config-yaml.adoc, modules/creating-manifest-file-customized-br-ex-bridge.adoc to reference new install-config hostConfig method | OSDOCS-14514, OCPSTRAT-2184 |
| REQ-002 | Update modules/creating-manifest-file-customized-br-ex-bridge.adoc, modules/enabling-OVS-balance-slb-mode.adoc, modules/migrating-br-ex-bridge-nmstate.adoc, installing/installing_bare_metal/upi/installing-bare-metal.adoc to incorporate new hostConfig approach | OSDOCS-18249 |
| REQ-003 | Update modules/installation-configuration-parameters.adoc, modules/enabling-OVS-balance-slb-mode.adoc to reference new method | OSDOCS-14514 |
| REQ-004 | Update migrating-br-ex-bridge-nmstate.adoc, creating-manifest-file-customized-br-ex-bridge-post.adoc, installation-configuration-parameters.adoc to distinguish day-1 vs day-2 methods | OSDOCS-14514, OCPSTRAT-2184 |

## Notes

**REQ-003**: This requirement specifically focuses on verification and troubleshooting for the NEW install-config interface (networking.hostConfig), not the existing post-installation NodeNetworkConfigurationPolicy (NNCP) method. The install-config method applies at day 0 via machine-configs, while NNCP is day 2. The installer automatically base64-encodes the NMState YAML and creates machine-configs named 99-network-config-master and 99-network-config-worker that place files in /etc/nmstate/openshift/<hostname>.yml. Users need to understand this differs from manually creating base64-encoded machine-configs. Verification must happen after nodes boot, using node debug sessions with nmstatectl and ovs-vsctl commands. The sibling requirement OSDOCS-18249 covers the main procedural documentation for using the install-config interface; this requirement focuses specifically on the verification and troubleshooting aspects identified in the mini content journey as critical user pain points.

**REQ-004**: This requirement is specifically about creating comparison/guidance documentation, not implementing the feature itself. The feature (networking.hostConfig) is already implemented in the installer for 4.19. The documentation work involves updating existing br-ex configuration procedures to explain both methods and guide users on when to use each approach. The machine-config method remains valid for post-installation scenarios, while the install-config method is recommended for day-1 installation.

**REQ-002**: The enhancement introduces a new install-config interface (networking.hostConfig) that wraps the existing machine-config based NMState functionality. The machine-config approach remains fully supported and is complementary - some complex scenarios may still require it. The installer's ForNetworkConfig function (networkconfig.go) performs the conversion from hostConfig entries to MachineConfig manifests, automating the base64 encoding and file path generation that users previously had to do manually. Each hostConfig entry specifies a hostname and networkConfig (NMState YAML as JSON), which the installer writes to /etc/nmstate/openshift/<hostname>.yml. Documentation should position the install-config method as the recommended approach for standard deployments while preserving machine-config documentation for advanced use cases.

## Related tickets

- **Parent**: [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184) - Add an install-config interface to the previously implemented feature for creating br-ex using NMState.
- **Children**: [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249) - [OPNET-592] Install-config interface for NMState br-ex
- **Siblings**: [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592) - Install-config interface for NMState br-ex

## Sources consulted

### JIRA tickets
- [OSDOCS-14514](https://redhat.atlassian.net/browse/OSDOCS-14514) - Docs for OCPSTRAT-2184 Add an install-config interface to the previously implemented feature for creating br-ex using NMState.
- [OCPSTRAT-2184](https://redhat.atlassian.net/browse/OCPSTRAT-2184) - Add an install-config interface to the previously implemented feature for creating br-ex using NMState.
- [OPNET-592](https://redhat.atlassian.net/browse/OPNET-592) - Install-config interface for NMState br-ex
- [OSDOCS-18249](https://redhat.atlassian.net/browse/OSDOCS-18249) - [OPNET-592] Install-config interface for NMState br-ex

### Pull requests / Merge requests
- [Enhancement PR #1795](https://github.com/openshift/enhancements/pull/1795) - OPNET-592: Install-config interface for NMState configuration
- [Implementation PR #10251](https://github.com/openshift/installer/pull/10251) - OPNET-592: Add interface for NMState br-ex configuration

### Code files
- pkg/types/installconfig.go:413-420 - HostConfig field definition in Networking struct
- pkg/types/installconfig.go:277-285 - HostConfigEntry struct with Hostname and NetworkConfig fields
- pkg/asset/machines/machineconfig/networkconfig.go:56-70 - MachineConfig generation logic placing configs in /etc/nmstate/openshift/<hostname>.yml
- data/data/install.openshift.io_installconfigs.yaml:4430-4459 - API schema definition for networking.hostConfig array
- data/data/install.openshift.io_installconfigs.yaml:9-29 - Schema defining hostConfig array with hostname and networkConfig properties

### Existing documentation
- modules/creating-manifest-file-customized-br-ex-bridge.adoc - Documents the current machine-config method for br-ex creation
- modules/installation-two-node-creating-manifest-custom-br-ex.adoc - Documents br-ex configuration for two-node clusters
- modules/migrating-br-ex-bridge-nmstate.adoc - Documents migration of br-ex bridge using NMState
- modules/enabling-OVS-balance-slb-mode.adoc - Existing documentation showing machine-config method for OVS bonding

### Web search findings
- [Day One Networking in OpenShift](http://blog.nemebean.com/content/day-one-networking-openshift) - Referenced in enhancement as preliminary documentation for day-1 network configuration
- [Advanced br-ex Configuration with Bonding on OpenShift](https://blog.stderr.at/openshift-platform/networking/2026-02-05-advanced-br-ex-with-bonding/) - 2026 article demonstrating advanced br-ex configurations
- [Red Hat OpenShift Virtualization: Configuring virtual machines to use external networks](https://www.redhat.com/en/blog/access-external-networks-with-openshift-virtualization) - Use case context for br-ex with single interface
- [OpenShift Virtualization with Localnet Configuration](https://blog.epheo.eu/articles/openshift-localnet/index.html) - Demonstrates reusing br-ex for VM traffic
- [NMState Operator and OpenShift Container Platform](https://xphyr.net/post/nmstate/) - Background on NMState integration
- [Kubernetes NMState documentation](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/kubernetes_nmstate/k8s-nmstate-updating-node-network-config) - Day-2 NMState management context
- [Troubleshooting node network configuration - OpenShift 4.17](https://docs.redhat.com/en/documentation/openshift_container_platform/4.17/html/kubernetes_nmstate/k8s-nmstate-troubleshooting-node-network) - Official troubleshooting patterns
- [Customize br-ex interface using NMState in OpenShift 4.16+](https://access.redhat.com/solutions/7111563) - Red Hat Knowledge Base article on NMState br-ex customization
