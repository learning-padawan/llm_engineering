# Messaging Permissions (OpenFGA)

## Type: `messaging`

### Relations:
- **viewer**: [user, group#member] – Can view messages
- **editor**: [user, group#member] – Can create and edit messages
- **admin**: [user, group#member] – Full access to messaging settings and users
- **moderator**: [user, group#member] – Can manage message threads and moderate content

---

## Supporting Types:

### Type: `group`
- **member**: [user]

### Type: `user`
- No additional relations
