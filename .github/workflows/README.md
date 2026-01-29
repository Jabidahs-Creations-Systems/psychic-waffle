# GitHub Workflows

This directory contains GitHub Actions workflows for the TruffleHog repository.

## PR Approval Check (`pr-approval-check.yml`)

This workflow enforces that at least one PR approver must be an **active** member of the `@trufflesecurity/product-eng` team or any of its child teams.

### How it works:

1. **Triggers**: The workflow runs on:
   - `pull_request_review` events when a review is submitted (`submitted` type)
   - `pull_request` events when a PR is opened, reopened, or synchronized (`opened`, `reopened`, `synchronize` types)

2. **Approval Check Process**: The workflow:
   - Fetches all reviews for the PR using the GitHub API
   - Filters for reviews with state `APPROVED`
   - Gets all child teams of `@trufflesecurity/product-eng` using `listChildInOrg` API
   - Checks if any approver is an **active** member (not pending) of either:
     - The parent `@trufflesecurity/product-eng` team, OR
     - Any of its child teams
   - Sets a commit status accordingly

3. **Status Check**: Creates a commit status named `pr-approval-check` with:
   - ✅ **Success**: When at least one approver is an active member of `@trufflesecurity/product-eng` or any child team
   - ❌ **Failure**: When there are no approvals or there are approvals but none from active `@trufflesecurity/product-eng` members

### Error Handling

If there are errors listing reviews or checking team membership, the workflow reports a failure status and also fails itself.

### Branch Protection

To make this check required:

1. Go to Settings → Branches
2. Add or edit a branch protection rule for your main branch
3. Enable "Require status checks to pass before merging"
4. Add `pr-approval-check` to the required status checks

### Configuration

The workflow can be customized by modifying the environment variables in the workflow file:

- `APPROVAL_ORG`: The GitHub organization name (default: `trufflesecurity`)
- `APPROVAL_TEAM`: The team slug to check for approvals (default: `product-eng`)
- `STATUS_CHECK_NAME`: The name of the status check (default: `pr-approval-check`)

To customize for your repository, edit the `env` section in `.github/workflows/pr-approval-check.yml`.

### Permissions

The workflow uses the default `GITHUB_TOKEN` which requires the following permissions:
- Read PR reviews (`pull-requests: read`)
- List child teams and check team membership (`members: read`)
- Read repository contents (`contents: read`)
- Create commit statuses (`statuses: write`)

**Note**: If the `product-eng` team or its child teams are private, you may need to use a personal access token with appropriate permissions. The GitHub API returns 404 for non-members and for lack of permissions.