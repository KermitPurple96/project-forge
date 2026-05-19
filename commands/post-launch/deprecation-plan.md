# 🔀 Command: deprecation-plan

> Plans deprecation of features or API versions.

## Instructions for Claude

1. Identify what's being deprecated and why
2. Generate deprecation plan:
   - **Announcement date:** When users are notified
   - **Deprecation date:** When it starts showing warnings
   - **Removal date:** When it's actually removed (minimum 3 months from announcement)
   - **Migration path:** How users move to the replacement
   - **Communication:** Email, in-app banner, API warning headers
3. Implement deprecation warnings in code:
   - Console warnings for deprecated functions
   - `Deprecation` header in API responses
   - In-app notification for deprecated features
4. Monitor usage of deprecated feature to track migration progress
