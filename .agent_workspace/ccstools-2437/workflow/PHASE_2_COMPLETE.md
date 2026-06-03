# Phase 2 Complete: File Title Updates

## Summary

Successfully updated **11 files** to align titles with the topic map structure and JTBD plan.

## Files Modified

### 1. Topic Map Structure (1 file)
- `_topic_maps/_topic_map_ms.yml` - Main MicroShift topic map
  - Renamed section headers
  - Moved IPv6 to Networking
  - Reorganized Security subsections

### 2. Networking Files (5 files)
1. `microshift_networking/microshift-cni.adoc`
   - Old: "About the OVN-Kubernetes network plugin"
   - **New: "Understand and configure the default networking model"**

2. `microshift_networking/microshift-networking-settings.adoc`
   - Old: "Understanding networking settings"
   - **New: "Using networking settings"**

3. `microshift_networking/microshift-nw-router.adoc`
   - Old: "Understanding and configuring the router"
   - **New: "Configuring the router"**

4. `microshift_networking/microshift-firewall.adoc`
   - Old: "Using a firewall"
   - **New: "Firewall configuration"**

5. `microshift_networking/microshift-sriov.adoc`
   - Old: "Using the SR-IOV Network Operator"
   - **New: "Configuring the SR-IOV network operator"**

### 3. Storage Files (1 file)
1. `microshift_storage/index.adoc`
   - Old: "Storage overview"
   - **New: "MicroShift storage options"**

### 4. Configuring/Security Files (4 files)
1. `microshift_configuring/microshift_auth_security/microshift-custom-ca.adoc`
   - Old: "Configuring custom certificate authorities"
   - **New: "Configure custom certificate authorities"**

2. `microshift_configuring/microshift-gdp.adoc`
   - Old: "Using the Generic Device Plugin"
   - **New: "About the Generic Device Plugin"**

3. `microshift_configuring/microshift-greenboot-checking-status.adoc`
   - Old: "Checking greenboot scripts status"
   - **New: "Checking the status of greenboot health checks"**

4. `microshift_configuring/microshift-feature-gates.adoc`
   - Old: "Using feature gates to develop solutions for your applications"
   - **New: "Using feature gates to develop solutions"**

## Change Statistics

- **Total files changed**: 11
- **Total lines changed**: 56 (31 insertions, 25 deletions)
- **Sections affected**: 
  - Networking: 5 files
  - Storage: 1 file
  - Configuring: 4 files
  - Topic map: 1 file

## Title Change Patterns

### Simplification
- Removed "for your applications" (feature gates)
- Removed "Understanding and" prefix (router)
- Removed "scripts" specificity (greenboot)

### Consistency 
- "Using" → "Configuring" for active configuration topics
- "Understanding" → "Using" for procedural topics
- "About" for conceptual overviews

### User-Centric Language
- "Storage overview" → "MicroShift storage options" (focuses on user choices)
- Added "default networking model" context (helps users understand scope)

## Alignment with JTBD Framework

These title changes align with the Jobs-to-be-Done framework by:
1. **Focusing on user goals**: Titles now emphasize what users can *do* or *configure*
2. **Reducing jargon**: Simplified titles remove unnecessary technical qualifiers
3. **Improving scannability**: Shorter, clearer titles help users find relevant content faster
4. **Establishing hierarchy**: Consistent verb usage (Configure, Using, About) signals content type

## What's Still Aligned

These files already had titles matching the topic map (no changes needed):
- `microshift-tls-config.adoc` - "Configuring TLS security profiles" ✓
- `microshift-audit-logs-config.adoc` - "Configuring audit logging policies" ✓
- `microshift-verify-container-signatures.adoc` - "Verifying container signatures..." ✓
- `microshift-nw-ipv6-config.adoc` - "Configuring IPv6 networking" ✓
- `microshift-configuring-routes.adoc` - "Configuring routes" ✓
- `microshift-cni-multus.adoc` - "About using multiple networks" ✓
- Network policy files - All matched ✓
- Low latency files - All matched ✓

## Git Status

**Branch**: `CCSTOOLS-2437-jtbd-title-changes`

**Ready to commit**:
```bash
11 files modified
56 lines changed (31+, 25-)
```

## Next Steps

Phase 2 is **COMPLETE**. Ready to proceed to:

**Phase 3**: Create new section structure in topic map
- New "Expose applications externally" section
- New "Secure and control traffic" section
- Reorganize router, ingress, routes under new umbrella

**OR**

Review and commit Phase 1 + Phase 2 changes before continuing.

## Testing Recommendations

Before committing:
1. ✓ Build documentation to verify no broken links
2. ✓ Verify cross-references still work
3. ✓ Check that navigation renders correctly
4. ✓ Validate YAML syntax in topic map

## Impact Assessment

**Low Risk Changes**:
- Title updates only (no content changes)
- All files maintain same IDs
- Cross-references use IDs (not affected by title changes)
- Topic map structure is additive (moved items, not removed)

**User-Facing Impact**:
- Improved navigation clarity
- Better alignment with user mental models
- More consistent terminology across sections
