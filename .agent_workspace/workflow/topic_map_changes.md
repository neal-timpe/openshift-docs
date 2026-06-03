# Topic Map Changes for JTBD Configure Plan

## Current Structure vs. Proposed Structure

### NETWORKING SECTION - Major Reorganization

#### Current (_topic_map_ms.yml lines 163-200):
```yaml
---
Name: Networking
Dir: microshift_networking
Topics:
- Name: About the networking plugin
  File: microshift-cni
- Name: Using networking settings
  File: microshift-networking-settings
- Name: Configuring the router
  File: microshift-nw-router
- Name: Configuring the SR-IOV network operator
  File: microshift-sriov
- Name: Network policies
  Dir: microshift_network_policy
  Topics: [creating, editing, deleting, viewing]
- Name: Multiple networks
  Dir: microshift_multiple_networks
  Topics: [about, configuring]
- Name: Configuring routes
  File: microshift-configuring-routes
- Name: Firewall configuration
  File: microshift-firewall
- Name: Networking settings for fully disconnected hosts
  File: microshift-disconnected-network-config
```

#### Proposed Structure (based on JTBD plan):
```yaml
---
Name: Configure networking
Dir: microshift_networking
Topics:
- Name: Understand and configure the default networking model
  File: microshift-cni  # RENAMED from "About the networking plugin"
  Topics:
    - Name: Network features
      File: microshift-network-features  # COMBINED with networking matrix
    - Name: Default router and firewall capabilities
      File: microshift-default-router-firewall  # NEW - mentions both
    - Name: Network performance optimizations
      File: microshift-network-performance
    - Name: MicroShift networking components and services
      File: microshift-networking-components
    - Name: Network topology
      File: microshift-network-topology
    - Name: How traffic flows through the system
      File: microshift-traffic-flow  # RENAMED from "bridge mappings"

- Name: Customize networking configuration
  File: microshift-networking-settings  # Keep existing
  Topics:
    - Name: Creating an OVN-Kubernetes configuration file
      File: microshift-ovn-config
    - Name: Restarting the ovnkube-master pod
      File: microshift-restart-ovnkube
    - Name: Deploy MicroShift behind an HTTP or HTTPS proxy
      File: microshift-proxy-config
    - Name: Getting a snapshot of OVS interfaces
      File: microshift-ovs-snapshot  # Question: move to troubleshooting?
    - Name: Deploy the MicroShift LoadBalancer service
      File: microshift-loadbalancer  # COMBINED 2.7 + 2.8
    - Name: Block external access to NodePort service
      File: microshift-block-nodeport
    - Name: The multicast DNS protocol
      File: microshift-mdns
    - Name: Auditing exposed network ports
      File: microshift-audit-ports

- Name: Expose applications externally  # NEW SECTION
  Topics:
    - Name: Configure the router and network ingress  # COMBINED router + ingress
      File: microshift-router-ingress-config
      Topics:
        - Name: Router settings and valid values
          File: microshift-router-settings
        - Name: Configuring router ingress
          File: microshift-router-ingress
        - Name: Configuring router ports and IP addresses
          File: microshift-router-ports-ips  # COMBINED 3.3.1 + 3.3.2
        - Name: Configuring the route admission policy
          File: microshift-route-admission
        - Name: Disabling the router
          File: microshift-disable-router
        - Name: Using ingress control in MicroShift
          File: microshift-ingress-control
        - Name: Configuring ingress control in MicroShift
          File: microshift-ingress-config
        - Name: Creating a secret for the ingress controller certificate
          File: microshift-ingress-cert-secret
        - Name: Configuring the TLS security profile for ingress
          File: microshift-ingress-tls
    
    - Name: Configure routes to expose your applications  # MOVED to be first?
      File: microshift-configuring-routes
      Topics:
        - Name: Create an HTTP-based route
          File: microshift-route-http
        - Name: HTTP Strict Transport Security  # COMBINED 7.2 + 7.3
          File: microshift-route-hsts
        - Name: Enforcing HSTS per-domain
          File: microshift-route-hsts-domain
        - Name: Disabling HSTS per-route
          File: microshift-route-hsts-disable
        - Name: Throughput issue troubleshooting
          File: microshift-route-throughput
        - Name: Using cookies to keep route statefulness
          File: microshift-route-cookies
        - Name: Path-based routes
          File: microshift-route-path
        - Name: HTTP header configuration
          File: microshift-route-headers
        - Name: Creating a route through an Ingress object
          File: microshift-route-ingress-object

- Name: Secure and control traffic  # NEW SECTION
  Topics:
    - Name: Configure the firewall  # COMBINED with 8.1
      File: microshift-firewall
      Topics:
        - Name: About network traffic through the firewall
          File: microshift-firewall-about  # merged into parent
        - Name: Install the firewalld service
          File: microshift-firewall-install
        - Name: Required firewall settings
          File: microshift-firewall-required
        - Name: Configure optional port settings
          File: microshift-firewall-optional
        - Name: Add services to open ports
          File: microshift-firewall-services
        - Name: Allow network traffic through the firewall  # COMBINED with 8.6.1
          File: microshift-firewall-allow-traffic
        - Name: Verifying firewall settings
          File: microshift-firewall-verify
    
    - Name: Restrict communication with network policies  # COMBINED
      File: microshift-network-policy-index
      Topics:
        - Name: How network policy works in MicroShift
          File: microshift-network-policy-how
        - Name: Creating network policies
          File: microshift-creating-network-policy
        - Name: Editing network policies  # COMBINED with examples
          File: microshift-editing-network-policy
        - Name: Viewing network policies  # COMBINED
          File: microshift-viewing-network-policy
        - Name: Deleting network policies  # COMBINED
          File: microshift-deleting-network-policy
    
    - Name: Connect pods to multiple networks with Multus  # COMBINED
      File: microshift-cni-multus
      Topics:
        - Name: Use case: Network isolation with secondary networks
          File: microshift-multus-use-case
        - Name: Installing the Multus CNI plugin
          File: microshift-multus-install
        - Name: Configuring and using multiple networks
          File: microshift-cni-multus-using
        - Name: Troubleshooting Multus networking
          File: microshift-multus-troubleshoot

- Name: Improve performance with SR-IOV Network Operator  # COMBINED
  File: microshift-sriov
  Topics:
    - Name: Understanding the SR-IOV Network Operator
      File: microshift-sriov-understanding
    - Name: Installing the SR-IOV Network Operator
      File: microshift-sriov-install
    - Name: SR-IOV Network Operator supported devices
      File: microshift-sriov-devices

- Name: Configure IPv6 networking  # COMBINED - moved from Configuring
  File: microshift-nw-ipv6-config
  Topics:
    - Name: IPv6 networking with MicroShift
      File: microshift-ipv6-about
    - Name: Configuring IPv6 single-stack networking
      File: microshift-ipv6-single
    - Name: Configuring IPv6 dual-stack networking
      File: microshift-ipv6-dual
    - Name: Migrating to IPv6 dual-stack networking
      File: microshift-ipv6-migrate
    - Name: Resetting IP family policy
      File: microshift-ipv6-reset-policy
    - Name: OVN-Kubernetes IPv6 limitations
      File: microshift-ipv6-limitations

- Name: Configuring network settings for fully disconnected hosts
  File: microshift-disconnected-network-config
  Topics:
    - Name: Preparing networking for fully disconnected hosts
      File: microshift-disconnected-prep
    - Name: Restoring MicroShift networking settings to default
      File: microshift-networking-restore-default
    - Name: Configuring the networking settings
      File: microshift-disconnected-network-settings
    - Name: Configuring custom hostnames
      File: microshift-custom-hostnames
```

### CONFIGURING SECTION - Security Additions

#### Additions to lines 120-161:
```yaml
---
Name: Configuring
Dir: microshift_configuring
Topics:
# ... existing topics ...

- Name: Security in MicroShift  # NEW OVERVIEW
  Dir: microshift_security
  Topics:
    - Name: Security overview
      File: microshift-security-overview
    - Name: MicroShift security for RHEL users
      File: microshift-security-rhel-users
    - Name: Security differences in MicroShift and OpenShift
      File: microshift-security-differences

- Name: Secure the MicroShift API and Control Plane  # NEW SECTION
  Dir: microshift_auth_security  # merge into existing
  Topics:
    - Name: Configure custom certificate authorities
      File: microshift-custom-ca
    - Name: Configuring TLS security profiles
      File: microshift-tls-config

- Name: Configure audit logging  # RENAMED nav title
  Dir: microshift_audit
  Topics:
    - Name: Configuring audit logging policies
      File: microshift-audit-logs-config
    - Name: Audit log file storage limits
      File: microshift-audit-storage
    - Name: Policy profiles for log levels
      File: microshift-audit-profiles
    - Name: Configure audit log values
      File: microshift-audit-values
    - Name: Troubleshoot audit log configuration
      File: microshift-audit-troubleshoot

- Name: Verify trusted container images  # NEW
  Dir: microshift_container_verification
  Topics:
    - Name: Understand sigstore container verification
      File: microshift-sigstore-understanding
    - Name: Verify container signatures using sigstore
      File: microshift-verify-container-signatures
    - Name: Enable sigstore attachments for mirror registries
      File: microshift-sigstore-mirror
    - Name: Wipe local container storage clean
      File: microshift-container-storage-wipe
```

### STORAGE SECTION - Reorganization

#### Current (lines 202-219):
```yaml
---
Name: Storage
Dir: microshift_storage
Topics:
- Name: Storage
  File: index
- Name: Using dynamic storage with the LVMS plugin
  File: microshift-storage-plugin-overview
- Name: Using ephemeral storage
  File: using-ephemeral-storage-microshift
- Name: Generic ephemeral volumes
  File: generic-ephemeral-volumes-microshift
- Name: Using persistent storage
  File: using-persistent-storage-microshift
- Name: Expanding persistent volumes
  File: expanding-persistent-volumes-microshift
- Name: Working with volume snapshots
  File: volume-snapshots-microshift
```

#### Proposed:
```yaml
---
Name: Configure storage
Dir: microshift_storage
Topics:
- Name: MicroShift storage options  # RENAMED, COMBINED overview
  File: index
  # Combine into bulleted list: Dynamic provisioning with LVMS, Persistent, Ephemeral, Dynamic provisioning

- Name: Using dynamic storage with the LVMS plugin
  File: microshift-storage-plugin-overview
  Topics:
    - Name: LVMS system requirements
      File: microshift-lvms-requirements
      Topics:
        - Name: Volume group name
          File: microshift-lvms-volume-group
        - Name: Volume size increments
          File: microshift-lvms-size-increments
    - Name: LVMS deployment
      File: microshift-lvms-deployment
    - Name: Device size limitations in LVM Storage  # RENAMED
      File: microshift-lvms-device-size-limits
    - Name: Customize LVMS configuration  # RENAMED
      File: microshift-lvms-config
      Topics:
        - Name: Basic LVMS configuration example  # MOVED under parent
          File: microshift-lvms-config-example
    - Name: Using the LVMS
      File: microshift-lvms-using
      Topics:
        - Name: Device classes
          File: microshift-lvms-device-classes
    # Disabling/Uninstalling MOVED to AFTER "Using"
    - Name: Reduce runtime resources by removing LVMS and CSI  # RENAMED, COMBINED
      File: microshift-lvms-disable-uninstall
      Topics:
        - Name: Disabling CSI snapshot implementations  # IMPORTANT: Note about BEFORE install
          File: microshift-lvms-disable-snapshot
        - Name: Disabling CSI driver implementations
          File: microshift-lvms-disable-driver
        - Name: Uninstalling CSI snapshot implementation
          File: microshift-lvms-uninstall-snapshot
        - Name: Uninstalling CSI driver implementation
          File: microshift-lvms-uninstall-driver

- Name: Using ephemeral storage
  File: using-ephemeral-storage-microshift
- Name: Generic ephemeral volumes
  File: generic-ephemeral-volumes-microshift
- Name: Using persistent storage
  File: using-persistent-storage-microshift
- Name: Expanding persistent volumes
  File: expanding-persistent-volumes-microshift
- Name: Working with volume snapshots
  File: volume-snapshots-microshift
```

## Summary of Changes

### Title Renames
1. "About the networking plugin" → "Understand and configure the default networking model"
2. "Bridge mappings" → "How traffic flows through the system"
3. "Configuring audit logging policies" → nav title "Configure audit logging"
4. "Device size limitations in LVM Storage"
5. "Customize LVMS configuration"
6. "Reduce runtime resources by removing LVMS and CSI"

### New Sections Created
1. "Expose applications externally" (networking)
2. "Secure and control traffic" (networking)
3. "Security in MicroShift" (configuring)
4. "Secure the MicroShift API and Control Plane" (configuring)
5. "Verify trusted container images" (configuring)
6. "Configuring network settings for fully disconnected hosts" (networking - reorganized)

### Major Combinations
1. Router + Network ingress → single "Configure the router and network ingress"
2. Firewall + "About network traffic" → single topic
3. Network policies - combine editing/viewing/deleting with examples
4. Multus - combine with "About using multiple networks"
5. SR-IOV - combine with understanding topic
6. IPv6 - combine with understanding topic
7. HTTP Strict Transport Security - combine 7.2 + 7.3
8. LVMS disable/uninstall topics - consolidate

### Structural Moves
1. IPv6 configuration: Configuring → Networking
2. Ingress controller: Configuring → Networking (under Expose applications)
3. LVMS disable/uninstall: BEFORE → AFTER "Using LVMS"
4. CSI snapshot disable: Must note it's BEFORE install

## Next Steps

1. Update `_topic_map_ms.yml` with new structure
2. Rename/create files as needed
3. Update cross-references
4. Verify all links and includes
