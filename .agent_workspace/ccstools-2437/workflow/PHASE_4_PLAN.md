# Phase 4 Implementation Plan: Content Combinations

## Priority Order

### High Priority (Most Impactful)
1. **Router + Network Ingress** (rows 32-38) - Major combination, per Daniel
2. **Firewall + "About network traffic"** (row 58, 64) - Combine opening topics
3. **HTTP Strict Transport Security** (row 45) - Combine 7.2 + 7.3

### Medium Priority  
4. **Network Policy topics** (rows 69-81) - Combine editing/viewing/deleting with examples
5. **LoadBalancer topics** (row 24) - Combine 2.7 + 2.8
6. **LVMS disable/uninstall** (rows 124-128) - Consolidate multiple topics

### Lower Priority (Less visible)
7. **Multus topics** (rows 82-95) - Combine with "About using multiple networks"
8. **SR-IOV topics** (rows 96-98) - Combine with understanding topic
9. **IPv6 topics** (rows 99-104) - Combine with understanding topic
10. **Greenboot topics** (row 201) - Combine 3.6 and 3.7

## Starting with #1: Router + Network Ingress

### Current State:
- `microshift-nw-router.adoc` - "Configuring the router"
- `microshift-ingress-controller.adoc` - "Using ingress control for a MicroShift node"

### Target State:
- Single topic: "Configure the router and network ingress"
- Per Daniel: Rename 'ingress control' to 'network ingress'
- Combine settings, configuration steps

### Files to Read:
1. microshift_networking/microshift-nw-router.adoc
2. microshift_configuring/microshift-ingress-controller.adoc

### Approach:
- Read both files
- Identify overlapping content
- Merge into primary file (router)
- Update cross-references
- Update topic map
