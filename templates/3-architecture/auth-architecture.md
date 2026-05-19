# 🔀 Auth Architecture

## Authentication method

- [ ] Email + password (bcrypt/argon2)
- [ ] OAuth2 (Google, GitHub, etc.)
- [ ] Magic link (passwordless email)
- [ ] SSO (SAML, OpenID Connect)
- [ ] API key (for machine-to-machine)

## Token strategy

- **Type:** <!-- JWT, opaque token, session cookie -->
- **Access token lifetime:** <!-- e.g. 15 minutes -->
- **Refresh token lifetime:** <!-- e.g. 7 days -->
- **Storage:** <!-- httpOnly cookie, memory, localStorage (not recommended) -->

## Authorization model

- [ ] RBAC (Role-Based Access Control) — roles with fixed permissions
- [ ] ABAC (Attribute-Based Access Control) — dynamic rules based on attributes
- [ ] Simple owner-based — users can only access their own data

## MFA

- [ ] Not needed
- [ ] Optional for users
- [ ] Required for admins
- [ ] Required for all
- **Method:** <!-- TOTP (authenticator app), SMS, email -->

## OAuth providers (if applicable)

| Provider | Scopes needed | Why |
|----------|--------------|-----|
| Google | email, profile | Login + avatar |
|         |               |     |
