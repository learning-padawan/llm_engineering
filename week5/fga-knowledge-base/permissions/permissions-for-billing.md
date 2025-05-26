# Billing Permissions (OpenFGA)

## Type: `billing`

### Relations:
- **viewer**: [user, group#member] – Can view invoices and financial summaries
- **approver**: [user, group#member] – Can approve charges and cost allocations
- **admin**: [user, group#member] – Full access to billing operations
- **auditor**: [user, group#member] – Read-only access for compliance checks
- **accountant**: [user, group#member] – Access to detailed transaction records and reconciliations

---

## Supporting Types:

### Type: `group`
- **member**: [user]

### Type: `user`
- No additional relations
