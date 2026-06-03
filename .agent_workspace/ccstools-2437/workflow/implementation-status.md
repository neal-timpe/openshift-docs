# JTBD Implementation Status

## Completed Changes

### Topic Map Updates (_topic_maps/_topic_map_ms.yml)

#### 1. Title Changes ✓
- **Networking section**: "About the networking plugin" → "Understand and configure the default networking model"
- **Networking section header**: "Networking" → "Configure networking"
- **Storage section header**: "Storage" → "Configure storage"
- **Storage index**: "Storage" → "MicroShift storage options"
- **Security section**: "Configuring MicroShift authentication and security" → "Securing MicroShift"

#### 2. Structural Reorganizations ✓
- **IPv6 networking**: Moved from Configuring section to Networking section (now appears after Firewall)
- **Security topics**: Reorganized into three subsections:
  - "Secure the MicroShift API and Control Plane" (custom CAs, TLS)
  - "Configure audit logging" (audit policies)
  - "Verify trusted container images" (container signatures)

## Remaining Work

### High Priority - Still Needed

#### 1. File Title Updates
Many `.adoc` files need their internal titles updated to match the new topic map names:
- `microshift_networking/microshift-cni.adoc` - update title to "Understand and configure the default networking model"
- `microshift_storage/index.adoc` - update title to "MicroShift storage options"
- Security-related files in `microshift_configuring/microshift_auth_security/` - update to match new groupings

#### 2. Major Content Combinations (Not Yet Implemented)
These require merging content from multiple files:

**Networking:**
- Combine router + network ingress sections (rows 32-38 in JTBD plan)
- Combine LoadBalancer topics 2.7 + 2.8 (row 24)
- Combine HTTP Strict Transport Security 7.2 + 7.3 (row 45)
- Combine firewall + "About network traffic" 8 + 8.1 (row 58)
- Combine network policy topics (rows 69-81)
- Combine Multus topics (rows 82-95)
- Combine SR-IOV topics (rows 96-98)
- Combine IPv6 topics (rows 99-104)

**Storage:**
- Combine LVMS disable/uninstall topics (rows 124-128)
- Move LVMS disable topics AFTER "Using LVMS" (row 123)
- Add note that CSI snapshot disable must be BEFORE install (row 125)

**Configuring/Security:**
- Create new security overview topics (rows 129-131)
- Expand audit logging section with additional topics (row 142)
- Create container verification topics (row 155)

#### 3. New Sections to Create
- "Expose applications externally" in Networking (row 31)
  - Reorganize router, ingress, and routes under this
- "Secure and control traffic" in Networking (row 57)
  - Move firewall and network policies here

#### 4. Minor Adjustments
- Greenboot topics - combine 3.6 and 3.7 (row 201)
- Generic Device Plugin - rewrite suggestions (rows 167-169)
- Low latency - assess if needs top-level grouping (row 178)

### Content Issues to Address
- Row 22: OVS snapshot - assess if belongs in troubleshooting instead of configuration
- Row 27: Auditing exposed ports - clarify relationship to other auditing
- Row 43: Routes - verify if "easiest/recommended method" (NEED TO VERIFY)
- Row 197: BUG - "How greenboot uses directories" appears twice in content

## Next Steps

### Recommended Approach

1. **Phase 1 (Current)**: Title updates only in topic map ✓ DONE
2. **Phase 2**: Update `.adoc` file titles to match topic map
3. **Phase 3**: Create new section structure in topic map (Expose apps, Secure traffic)
4. **Phase 4**: Implement content combinations (router+ingress, firewall, etc.)
5. **Phase 5**: Create new security overview content
6. **Phase 6**: Address content issues and verify recommendations

### Files That Will Need Changes

**Networking:**
- `microshift_networking/microshift-cni.adoc`
- `microshift_networking/microshift-nw-router.adoc`
- `microshift_configuring/microshift-ingress-controller.adoc` (move to networking)
- `microshift_networking/microshift-configuring-routes.adoc`
- `microshift_networking/microshift-firewall.adoc`
- Multiple network policy files
- Multiple multus files
- SR-IOV files
- `microshift_configuring/microshift-nw-ipv6-config.adoc` (already moved in topic map)

**Storage:**
- `microshift_storage/index.adoc`
- `microshift_storage/microshift-storage-plugin-overview.adoc`
- `microshift_configuring/microshift-disable-lvms-csi-provider-csi-snapshot.adoc`

**Security/Configuring:**
- `microshift_configuring/microshift_auth_security/` - all files
- New files needed for security overview content
- Audit logging files

### Git Workflow
- Current branch: `CCSTOOLS-2437-jtbd-title-changes`
- Changes staged so far: topic map structure only
- Recommendation: Commit in phases to make review easier

## Summary

**Done**: 
- 5 major title changes in topic map
- 2 structural reorganizations (IPv6 move, Security grouping)

**Remaining**:
- ~20 file title updates
- ~15 major content combinations
- ~5 new sections to create
- ~3 content issue resolutions
- ~40-50 files affected

**Estimated Effort**: This is a major documentation reorganization that will take multiple commits and careful review of each section.
