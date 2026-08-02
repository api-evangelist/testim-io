---
name: testim-io-manage-branches
description: Fork, list, merge, and delete Testim branches to run tests against isolated versions of your test assets.
api: Testim.io Public API
base_url: https://api.testim.io
auth: 'Authorization: Bearer PAK-<key>  (Pro-tier project; keys from Settings > API)'
operations:
  - GET /branches
  - POST /branches
  - PUT /branches/merge
  - DELETE /branches/{branch}
---

# Manage Testim branches

Testim branches isolate test-asset changes the way Git branches isolate code.

## Steps

1. **Authenticate** with `Authorization: Bearer PAK-<key>`.
2. **List branches.** `GET /branches` returns `branches[]` with
   `name`, `created_at`, `modified_at`.
3. **Create a branch.** `POST /branches` with
   `{ "sourceBranch": "master", "newBranch": "feature-234_login_form" }`.
   A `409` means the branch name already exists.
4. **Run tests on the branch.** Pass `branch` when triggering runs
   (see the run-and-read-tests skill).
5. **Merge back.** `PUT /branches/merge` with
   `{ "sourceBranch": "...", "targetBranch": "master", "forceMerge": true }`.
   Without `forceMerge`, conflicts return `405 BranchMergeError`.
6. **Clean up.** `DELETE /branches/{branch}` removes a branch by name.

## Rules
- A missing branch returns `404 BranchDoesNotExist`.
- Validation issues return `400 InvalidOrMissingProperty`.
- Every response includes `metaData.requestId` for tracing.
