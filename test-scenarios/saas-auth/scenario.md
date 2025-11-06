# Test Scenario 2: SaaS Authentication System

## Scenario Description

**Project:** Multi-tenant authentication and authorization system for SaaS applications

**User Request (Initial):**
"We need a robust auth system for our SaaS product. It should support multiple organizations (tenants), role-based access control, SSO, and API key management. We're expecting to scale to thousands of organizations."

**Complexity Level:** High
- Multi-tenancy (complex data isolation)
- Multiple auth methods (password, SSO, API keys, MFA)
- Fine-grained permissions (RBAC)
- Security-critical (session management, token refresh, revocation)
- Scale considerations

## Expected Skill Behavior

### Phase 1: Requirements
- Should ask about tenant isolation strategy (database per tenant vs shared schema)
- Should ask about SSO providers (SAML, OAuth, OIDC)
- Should ask about MFA requirements
- Should ask about compliance needs (GDPR, SOC2)
- Should ask about scale constraints (concurrent users, API rate limits)

### Phase 2: Big Picture
- Should identify events: UserRegistered, UserLoggedIn, UserLoggedOut, SessionExpired, RoleAssigned, PermissionGranted, APIKeyCreated, MFAEnabled, SSOConfigured
- Should identify commands: Register, Login, Logout, AssignRole, GrantPermission, CreateAPIKey, EnableMFA, ConfigureSSO
- Should identify actors: User, Admin, SSO Provider, External API Client
- Should identify systems: SSO Provider (Okta, Auth0), Email Service, SMS Provider (for MFA)
- Should mark hotspots: Token refresh strategy, session storage, tenant data isolation, permission inheritance

### Phase 3: Process EventStorming
- Should zoom into critical processes:
  - User authentication flow (password + MFA)
  - SSO authentication flow
  - API key authentication flow
  - Permission check flow
  - Session refresh flow
- Should identify aggregates: User, Organization (Tenant), Role, Permission, Session, APIKey, SSOConfig
- Should show complex state transitions and security checks

### Phase 4: System Design
- **ERD**: Should include entities: Organization, User, Role, Permission, RolePermission, UserRole, Session, APIKey, SSOConfig, AuditLog, MFAConfig
- **State Charts**: Should create for:
  - User authentication state (logged out → authenticating → MFA required → authenticated → session expired)
  - API key lifecycle (active → expired → revoked)
  - SSO config state (draft → configured → verified → active)
- **Sequence Diagrams**: Should create for:
  - Password + MFA login flow
  - SSO authentication flow
  - API key validation flow
  - Permission check flow
  - Token refresh flow

### Phase 5: Integration
- Should generate catalog README
- Should ask about backend implementation (likely pumped-fn candidate)
- Should ask about implementation planning

## Pressure Points to Test

1. **Security depth**: Will agent consider security implications (token storage, refresh, revocation)?
2. **Multi-tenancy complexity**: Will agent address data isolation concerns?
3. **Multiple auth flows**: Will agent handle different authentication methods distinctly?
4. **State complexity**: Will agent model complex state transitions properly?
5. **Hotspots**: Will agent mark unclear/risky areas for discussion?

## Expected Artifacts

```
docs/design-catalog/
  README.md
  requirements.md
  big-picture.mmd
  processes/
    process-password-auth.mmd
    process-sso-auth.mmd
    process-apikey-auth.mmd
    process-permission-check.mmd
    process-session-refresh.mmd
  data/
    erd.mmd
    state-user-auth.mmd
    state-apikey.mmd
    state-sso-config.mmd
  flows/
    sequence-password-mfa-login.mmd
    sequence-sso-auth.mmd
    sequence-apikey-validation.mmd
    sequence-permission-check.mmd
    sequence-token-refresh.mmd
```

## Test Questions During Execution

- Mid-Phase 1: "Actually, we also need to support social login (Google, GitHub)"
- Mid-Phase 2: "What about password reset and account recovery?"
- Phase 3: "How do we handle permission inheritance from parent roles?"
- Phase 4: "Should we add rate limiting for failed login attempts?"
- Mid-Phase 4: "We need to add audit logging for compliance"

## Success Criteria

- Handles complex security requirements
- Models multiple authentication flows distinctly
- Addresses multi-tenancy concerns
- Creates detailed state charts for critical entities
- Marks security hotspots appropriately
- Handles iterative additions (social login, audit log)
- Token-efficient despite high complexity
