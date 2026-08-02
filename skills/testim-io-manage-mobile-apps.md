---
name: testim-io-manage-mobile-apps
description: Upload, list, fetch, and delete mobile application binaries used by Testim mobile tests.
api: Testim.io Public API
base_url: https://api.testim.io
auth: 'Authorization: Bearer PAK-<key>  (Pro-tier project; keys from Settings > API)'
operations:
  - GET /mobile/applications
  - POST /mobile/applications
  - GET /mobile/applications/{applicationId}
  - DELETE /mobile/applications/{applicationId}
---

# Manage Testim mobile applications

Manage the mobile app binaries that Testim mobile tests run against.

## Steps

1. **Authenticate** with `Authorization: Bearer PAK-<key>`.
2. **Upload an app.** `POST /mobile/applications` as `multipart/form-data`
   with a `file` field (the .apk/.ipa binary). Response returns
   `{ appId, metaData }`.
3. **List apps.** `GET /mobile/applications` returns each app's
   `applicationId`, `fileSize`, `status` (valid/invalid/parsing) and
   `appMetaData` (name/version/id/activity).
4. **Fetch one.** `GET /mobile/applications/{applicationId}` returns the same
   detail for a single app; a missing id returns `404 MobileAppDoesNotExist`.
5. **Delete.** `DELETE /mobile/applications/{applicationId}` removes it.

## Rules
- Wait until `status` is `valid` (not `parsing`) before referencing the app.
- Correlate calls via `metaData.requestId`.
- On `429`, back off — limits apply per project and per API key.
