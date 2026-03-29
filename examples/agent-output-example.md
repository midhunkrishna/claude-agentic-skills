# Example Planner Output

[AGENT] planner
[STATUS] OK
[SUMMARY] Smallest viable path is Cognito Hosted UI plus token exchange

[FINDINGS]
- Backend already trusts Cognito-issued JWTs
- Reusing Cognito avoids backend auth changes
- Expo redirect handling is the main integration concern

[ACTION]
1. Confirm Hosted UI redirect flow in Expo
2. Implement sign-in callback handling
3. Persist/refresh tokens locally
4. Add login/logout/session tests

[RISKS]
- Deep link misconfiguration may break callback completion

[NEEDS]
- Confirm iOS and Android redirect URI strategy

# Example Codebase-Reader Output

[AGENT] codebase-reader
[STATUS] OK
[SUMMARY] Auth logic spans web client, API middleware, and token helper

[FINDINGS]
- src/auth/token.ts validates JWT claims
- src/middleware/auth.ts enforces authenticated routes
- src/routes/login.ts begins web login flow
- No mobile-specific auth code exists yet

[ACTION]
1. Reuse token contract assumptions from src/auth/token.ts
2. Keep mobile token shape aligned with existing API expectations

[RISKS]
- Token storage differences on mobile may affect refresh behavior

[NEEDS]
- None

# Example Reviewer Output

[AGENT] reviewer
[STATUS] OK
[SUMMARY] Plan is viable but missing failure-path coverage

[FINDINGS]
- Success path is clear
- Callback failure and refresh expiry paths are not covered
- Logout token cleanup requirements not specified

[ACTION]
1. Add callback failure handling
2. Add expired-refresh-token behavior spec
3. Define logout storage cleanup

[RISKS]
- Stale tokens may leave app in inconsistent auth state

[NEEDS]
- Expected logout UX