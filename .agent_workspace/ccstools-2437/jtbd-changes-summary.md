# JTBD Plan Implementation Summary

## Major Changes from JTBD Configure Plan

### 1. Title Changes / Renames

- **Row 4**: "About the OVN-Kubernetes network plugin" → "Understand and configure the default networking model"
- **Row 7**: Router content → "Suggest that this job mentions default router and firewall capabilities"
- **Row 15**: "Bridge mappings" → "How traffic flows through the system"
- **Row 16**: "Customize networking configuration" → Keep name, update description
- **Row 17**: Possibly retitle if only MTU config
- **Row 24**: Combine LoadBalancer topics
- **Row 33**: Combine router and ingress sections - rename 'ingress control' to 'network ingress'
- **Row 43**: Routes section - verify if "easiest/recommended method"
- **Row 45**: "HTTP Strict Transport Security" - combine 7.2 and 7.3
- **Row 58**: Combine firewall topics
- **Row 119**: "Device size limitations in LVM Storage" (LVMS)
- **Row 120**: "Customize LVMS configuration"
- **Row 124**: "Reduce runtime resources by removing LVMS and CSI driver"
- **Row 150**: Nav title: "Configure audit logging"
- **Row 181**: "Configure kubelet parameters"

### 2. Content Combinations

- **Row 6**: Combine 1.3 Network features with 1.2 MicroShift networking configuration matrix
- **Row 17-18**: MTU configuration - assess if should be retitled
- **Row 24**: Combine LoadBalancer topics (2.7 and 2.8)
- **Row 32-38**: **MAJOR** - Combine router and network ingress sections into single section
- **Row 45**: Combine HTTP Strict Transport Security (7.2 + 7.3)
- **Row 58-64**: Combine firewall topics (8 + 8.1)
- **Row 64**: Combine "Allow network traffic" with "Applying firewall settings" (8.6 + 8.6.1)
- **Row 69-81**: Network policies - combine various subtopics
- **Row 82-95**: Multus networking - combine with "About using multiple networks"
- **Row 96-98**: SR-IOV - combine with understanding topic
- **Row 99-104**: IPv6 - combine with understanding topic
- **Row 113**: Storage overview - combine child topics into bulleted list
- **Row 124-128**: LVMS uninstall/disable topics - consolidate
- **Row 201**: Greenboot update checking - combine 3.6 and 3.7

### 3. New Topics to Create

- **Row 129**: [New] Security in MicroShift - overview with links
- **Row 130**: [New] MicroShift security for RHEL users
- **Row 131**: [New] Security Differences in MicroShift and OpenShift
- **Row 132**: Secure the MicroShift API and Control Plane (group topic)
- **Row 142**: Configure Audit Logging for Compliance (group topic)
- **Row 155**: Verify Trusted Container Images Before Deployment

### 4. Structural / Organizational Changes

- **Row 19**: Sections 2.3-2.5 (proxy configs) - consider if should be small jobs or nested procedures
- **Row 22**: OVS interfaces snapshot - question if belongs in TOC (troubleshooting?)
- **Row 27**: Auditing exposed ports - clarify relationship to other auditing sections
- **Row 31**: **NEW SECTION** - "Expose applications externally" (NodePort, ingress, routes)
- **Row 34**: Router ingress config sections should appear on same page
- **Row 57**: **NEW SECTION** - "Secure and control traffic"
- **Row 105-111**: **NEW SECTION** - "Configuring network settings for fully disconnected hosts"
- **Row 112-128**: **REORGANIZE** - Configure storage section
- **Row 123**: Move "Disabling/Uninstalling" topics AFTER "Using..." topics
- **Row 125**: IMPORTANT - CSI snapshot disable must be done BEFORE install, move to Install section
- **Row 178**: Low latency - question if needs top-level job grouping
- **Row 193**: Workload partitioning - clarify if part of low latency

### 5. Content Issues / Notes

- **Row 5**: "Are they presented in an intentional order?" - review flow
- **Row 27**: "??? Not sure what to do with this" - needs clarification
- **Row 31**: Explain when to choose NodePort vs ingress vs routes
- **Row 37**: Per Daniel - rename terminology
- **Row 43**: AI recommendation - NEED TO VERIFY routes are easiest method
- **Row 167-169**: Generic Device Plugin - rewrite suggestions provided
- **Row 197**: BUG - "How greenboot uses directories" appears twice

## Implementation Priority

1. **High Priority** - Structural changes (new sections, major reorganizations)
2. **Medium Priority** - Content combinations and renames
3. **Low Priority** - Minor title tweaks and clarifications

## Files Likely Affected

Based on directory structure:
- `microshift_networking/` - networking topics
- `microshift_configuring/` - configuration topics  
- `microshift_storage/` - storage topics (LVMS)
- Topic maps - structural organization
