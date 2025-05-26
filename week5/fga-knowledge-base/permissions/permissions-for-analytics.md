# Analytics Permissions (OpenFGA)

## Type: `analytics`

### Relations:
- **viewer**: [user, group#member] – Can view reports and dashboards
- **analyst**: [user, group#member] – Can create and customize analytics views
- **admin**: [user, group#member] – Full control over data access and configuration
- **data_scientist**: [user, group#member] – Advanced access to raw data and modeling tools
- **executive**: [user, group#member] – High-level access to strategic insights and KPIs

---

## Supporting Types:

### Type: `group`
- **member**: [user]

### Type: `user`
- No additional relations
