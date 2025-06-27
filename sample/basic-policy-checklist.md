# Basic Firewall Policy Checklist

> 🔥 **Essential firewall security checks** - Start with these 3 critical items

## Quick Assessment Items

### ✅ FW01: Overly Broad Access Allowance (ANY ANY Permission)

**Risk**: Policies allowing "ANY to ANY" effectively neutralize firewall security

**Check for**:
- Source or destination set to 'ANY'  
- Policies allowing all IP addresses to all destinations
- Service fields set to 'ALL' or equivalent

**Example Issues**:
```bash
# ❌ BAD - Allows everything
access-list OUTSIDE_IN permit ip any any

# ✅ GOOD - Specific permissions only  
access-list OUTSIDE_IN permit tcp host 203.0.113.10 host 10.1.1.2 eq 443
access-list OUTSIDE_IN deny ip any any log
```

---

### ✅ FW02: Network Segment-level Allowances

**Risk**: Broad network allowances (like /16, /8) create unnecessary attack surfaces

**Check for**:
- CIDR blocks broader than needed (e.g., 192.168.0.0/16 when only specific servers required)
- Subnet-wide permissions instead of host-specific rules
- Usage of 0.0.0.0/0 in source/destination

**Example Issues**:
```bash
# ❌ BAD - Too broad
access-list OUTSIDE_IN permit tcp 192.168.0.0 255.255.0.0 any eq 80

# ✅ GOOD - Specific hosts
access-list OUTSIDE_IN permit tcp host 192.168.1.100 host 10.10.10.10 eq 80
```

---

### ✅ FW03: Unnecessary Bidirectional Policy Existence

**Risk**: Bidirectional policies provide unnecessary return paths for attackers

**Check for**:
- Policies allowing both directions when only one is needed
- Web server access configured as bidirectional instead of unidirectional
- Missing stateful inspection configuration

**Example Issues**:
```bash
# ❌ BAD - Unnecessary bidirectional
# Two policies for same traffic flow
policy 1: Internal → External, port 80, ALLOW
policy 2: External → Internal, port 80, ALLOW  

# ✅ GOOD - Unidirectional with stateful inspection
# Only initial connection allowed, return traffic handled by stateful inspection
policy 1: External → Internal, port 443, ALLOW (with stateful return traffic)
```

---

## 🚀 Quick Start

1. **Scan your current rules** for ANY-ANY policies
2. **Review network ranges** - are they as specific as possible?
3. **Check traffic flows** - do you have unnecessary bidirectional rules?

## 📚 Full Guide

This checklist covers 3 of 30 total assessment items. For the complete security assessment framework including:
- 20 Firewall Policy (FW) items  
- 10 Management Control (MC) items
- International standards compliance (ISO 27001, PCI DSS, NIST)
- Vendor-specific configuration examples

See the full documentation in this repository.

---

## Standards Compliance

| Item | ISO 27001 | PCI DSS | NIST SP 800-41 |
|------|-----------|---------|----------------|
| FW01 | A.13.1.1 | 1.2.1 | 3.1 / 4.2 |
| FW02 | A.13.1.3 | 1.2.1 | 4.1 |
| FW03 | A.9.4.4 | 1.3.5 | 4.2 |
