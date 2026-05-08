# MVP Plan

## Goal

Build a small AWS deploy guard that prevents overlapping deployments for the same repository and environment.

The guard is inspired by an operational failure caused by overlapping deployments in a system with shared state / single-writer constraints.

## MVP Scope

- Provide an API to acquire a deploy lock.
- Provide an API to release a deploy lock.
- Store locks in DynamoDB.
- Use AWS Lambda behind API Gateway.
- Define infrastructure with AWS SAM.
- Write unit tests for lock behavior.

## Out of Scope

- Real production deployment.
- GitHub Actions integration.
- AWS OIDC setup.
- Multi-region locking.
- Authentication.
- UI.
- Advanced observability.
- Automatic cleanup jobs.

## API Endpoints

### POST /lock/acquire

Attempts to acquire a deploy lock for a repository and environment.

Expected request body:

```json
{
  "repo": "aws-deploy-guard",
  "environment": "prod",
  "runId": "123456",
  "commitSha": "abc123",
  "ttlSeconds": 900
}
```

Expected success response:

```json
{
  "acquired": true,
  "lockKey": "LOCK#aws-deploy-guard#prod",
  "repo": "aws-deploy-guard",
  "environment": "prod",
  "runId": "123456",
  "commitSha": "abc123",
  "expiresAt": 1710000900
}
```

### POST /lock/release

Attempts to release a deploy lock.

Expected request body:

```json
{
  "repo": "aws-deploy-guard",
  "environment": "prod",
  "runId": "123456"
}
```

Expected success response:

```json
{
  "released": true,
  "lockKey": "LOCK#aws-deploy-guard#prod"
}
```

## DynamoDB Lock Model

Use one item per repository/environment pair.

Primary key:

```text
pk = LOCK#repo#environment
```

Example item:

```json
{
  "pk": "LOCK#aws-deploy-guard#prod",
  "repo": "aws-deploy-guard",
  "environment": "prod",
  "runId": "123456",
  "commitSha": "abc123",
  "createdAt": 1710000000,
  "expiresAt": 1710000900
}
```

## Acquire Behavior

If no active lock exists for `repo + environment`, create the lock.

If an active lock already exists, reject the request.

If the existing lock is expired, allow the new request to replace it.

## Release Behavior

Only the same `runId` that acquired the lock can release it.

If another `runId` attempts to release the lock, reject the request.

## Expired Lock Behavior

Expired locks should not block new deployments.

DynamoDB TTL may be used later for cleanup, but application logic must still check `expiresAt` because TTL deletion is not immediate.

## Expected Status Codes

- `201 Created`: lock acquired
- `200 OK`: lock released
- `400 Bad Request`: invalid payload
- `404 Not Found`: no lock found to release
- `409 Conflict`: active lock already exists
- `403 Forbidden`: release attempted by a different runId

## Test Cases

- acquire creates a new lock
- acquire rejects an active existing lock
- acquire replaces an expired lock
- release removes a lock for the same runId
- release rejects a different runId
- invalid payload returns bad request

## Implementation Order

1. Initialize TypeScript tooling.
2. Add shared lock domain types.
3. Implement pure lock decision logic.
4. Add unit tests for lock behavior.
5. Add Lambda handlers.
6. Add AWS SAM template.
7. Wire handlers to DynamoDB.
8. Add local documentation and example requests.