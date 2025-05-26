# Guide to OpenFGA and Cross-Component Relationships

## Overview of OpenFGA

**OpenFGA** is an open-source authorization system inspired by Google's Zanzibar paper. It implements a relationship-based access control (ReBAC) model, enabling fine-grained, scalable, and flexible authorization logic. OpenFGA is schema-driven and operates based on a type system with defined relationships.

At its core, OpenFGA answers the question:
> Can user *X* perform action *Y* on object *Z*?

This is determined by:
- An **authorization model** (schema) defining types and relations.
- A set of **authorization tuples**, which are facts like: “user:123 is editor of messaging:campaign-789”.

---

## Core Concepts in OpenFGA

### 1. **Types**
These represent entities in your system (e.g., `user`, `document`, `group`, `campaign`).

### 2. **Relations**
Each type can define relationships with other types. For instance:
```fga
type document
  relations
    define viewer: [user, group#member]
```

### 3. **Tuples**
Tuples instantiate these relationships. For example:
```json
{
  "user": "user:123",
  "relation": "viewer",
  "object": "document:report-2024"
}
```

### 4. **Tuple-to-Userset**
This powerful construct allows delegation or inheritance of permissions. For example:
```fga
define viewer: [group#member, messaging#editor]
```

Here, any user who is an `editor` of a `messaging` object will automatically be a `viewer` of this object.

---

## Defining Cross-Component Relationships in OpenFGA

Let’s say you want users with the `editor` role on a `messaging` object to have `viewer` access to an `analytics` object. Instead of assigning permissions per user, you define role-based inheritance.

### Step-by-Step

#### 1. Define Your Types

```fga
type messaging
  relations
    define editor: [user, group#member]

type analytics
  relations
    define viewer: [user, messaging#editor]
```

#### 2. Create Authorization Tuples

Assign a user as editor on a messaging object:

```json
{
  "user": "user:123",
  "relation": "editor",
  "object": "messaging:campaign-789"
}
```

Then, delegate editor access from messaging to viewer access on analytics:

```json
{
  "user": "messaging:campaign-789#editor",
  "relation": "viewer",
  "object": "analytics:report-456"
}
```

#### 3. Evaluate Permissions

You can now ask:
> Can user:123 view analytics:report-456?

OpenFGA will resolve:
- user:123 is editor of messaging:campaign-789
- campaign-789#editor is viewer of analytics:report-456

✅ So, access is granted.

---

## Advantages of Tuple-to-Userset

- No need to duplicate permissions per user
- Flexible role delegation
- Promotes separation of concerns between domains (messaging, billing, analytics)
- Enables cross-domain access control

---

## Best Practices

- Use **clear type boundaries** per domain.
- Define **standard roles** (e.g., viewer, editor, admin) consistently across types.
- Prefer tuple-to-userset over user-level tuples for scalability.
- Test with the OpenFGA Playground or CLI to validate relationships.

---

## Example Use Case

**Goal:** Any `editor` of `messaging:project-x` is automatically a `viewer` of `analytics:report-y`.

**Steps:**
1. Define editor on messaging:
```json
{
  "user": "user:alice",
  "relation": "editor",
  "object": "messaging:project-x"
}
```

2. Connect messaging editor to analytics viewer:
```json
{
  "user": "messaging:project-x#editor",
  "relation": "viewer",
  "object": "analytics:report-y"
}
```

3. Ask FGA: Is Alice a viewer of analytics:report-y?

✅ Yes, through delegation.

---

## Conclusion

OpenFGA is ideal for modern, distributed, fine-grained authorization needs. Through its tuple-to-userset capability, it allows powerful cross-domain permission modeling—such as enabling users to inherit access rights across independent system components. This promotes scalability, modularity, and maintainability of your access control layer.
