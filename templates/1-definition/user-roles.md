# 📝 User Roles & Permissions

## Roles

| Role | Description | Can they self-register? |
|------|-------------|------------------------|
| Admin | Full system access | No — created manually |
|       |             |                        |
|       |             |                        |

## Permission matrix

| Action | Admin | User | Guest |
|--------|-------|------|-------|
| View public content | ✅ | ✅ | ✅ |
| Create own content | ✅ | ✅ | ❌ |
| Edit others' content | ✅ | ❌ | ❌ |
| Manage users | ✅ | ❌ | ❌ |
| Access settings | ✅ | ❌ | ❌ |
| <!-- add rows --> |  |  |  |

## Authentication flows

- **Registration:** <!-- email+password, OAuth, magic link, invite-only? -->
- **Login:** <!-- same as above -->
- **Password recovery:** <!-- email reset, SMS? -->
- **MFA:** <!-- required? optional? for which roles? -->
- **Session duration:** <!-- how long before re-auth? -->
