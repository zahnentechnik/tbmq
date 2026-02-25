# Changes to Original TBMQ Code

This document lists significant modifications made to the original TBMQ (ThingsBoard MQTT Broker) codebase, as required by the Apache License 2.0.

## Modified by
- Zahnen Technik GmbH
- Copyright © 2026

## Modifications

### 1. HTTP Authentication Strategy Feature

**Date:** 2026-02-25

**Description:** Added configurable HTTP authentication strategy to allow disabling web interface authentication while maintaining MQTT authentication.

**Modified Files:**

#### New Files Created:
- `application/src/main/java/org/thingsboard/mqtt/broker/config/HttpAuthStrategyProperties.java`
  - Created new configuration class to manage HTTP authentication strategies
  - Supports `BASIC` (JWT-based authentication) and `NONE` (no authentication) modes

#### Modified Files:
- `application/src/main/java/org/thingsboard/mqtt/broker/config/SecurityConfiguration.java`
  - Added conditional authentication filter chain based on `HttpAuthStrategyProperties`
  - Modified `filterChain()` method to support both authenticated and non-authenticated modes
  - Added `@Autowired HttpAuthStrategyProperties httpAuthStrategyProperties` field

- `application/src/main/resources/thingsboard-mqtt-broker.yml`
  - Added new `security.http.auth-strategy` configuration property
  - Added `SECURITY_HTTP_AUTH_STRATEGY` environment variable mapping
  - Default value: `BASIC`

- `docker/tbmq.env`
  - Added `SECURITY_HTTP_AUTH_STRATEGY=BASIC` environment variable

- `k8s/minikube/tbmq-configmap.yml`
  - Added `SECURITY_HTTP_AUTH_STRATEGY: "BASIC"` to ConfigMap data

- `k8s/aws/tbmq-db-configmap.yml`
  - Added `SECURITY_HTTP_AUTH_STRATEGY: "BASIC"` to ConfigMap data

- `k8s/azure/tbmq-db-configmap.yml`
  - Added `SECURITY_HTTP_AUTH_STRATEGY: "BASIC"` to ConfigMap data

- `k8s/gcp/tbmq-db-configmap.yml`
  - Added `SECURITY_HTTP_AUTH_STRATEGY: "BASIC"` to ConfigMap data

**Purpose:** 
- Allow deployment scenarios where external authentication proxies are used
- Provide easier development/testing environment without login requirements
- Maintain backward compatibility with default `BASIC` authentication strategy
- IMPORTANT: MQTT client authentication remains unchanged and independent

**Impact:**
- No breaking changes - default behavior remains identical to original
- New optional configuration allows disabling web UI authentication when needed
- All MQTT authentication mechanisms remain fully functional and unchanged

### 2. GitHub Actions Workflow for GHCR

**Date:** 2026-02-25

**Description:** Added GitHub Actions workflow to automatically build and publish Docker images to GitHub Container Registry (GHCR).

**New Files Created:**
- `.github/workflows/docker-publish.yml`
  - Builds TBMQ from source using Maven
  - Creates Docker image from `msa/mqtt-broker/`
  - Publishes to GitHub Container Registry
  - Triggers on releases, version tags, and manual dispatch

**Purpose:**
- Automated Docker image building and publishing
- Simplified deployment process for custom forks
- Automatic versioning based on Git tags and releases

**Impact:**
- No modifications to existing build process
- Adds additional CI/CD capability
- Does not affect existing Docker build configurations

---

## License Compliance

These modifications are made under the terms of the Apache License 2.0, the same license as the original TBMQ project. All modified files retain the original copyright notices and license headers where applicable. New files include appropriate license headers referencing Zahnen Technik GmbH as the modifier while maintaining Apache 2.0 licensing.

Original TBMQ Project: https://github.com/thingsboard/tbmq
Original Copyright: © 2016-2026 The Thingsboard Authors

---

## Building Modified Version

To build this modified version:

```bash
mvn clean install -DskipTests
```

To use the modified Docker image:

```bash
# Set authentication strategy to disable web login
export SECURITY_HTTP_AUTH_STRATEGY=NONE

# Start with Docker Compose
docker-compose up
```

## Notes

- These modifications maintain full API compatibility with original TBMQ
- The default configuration (`BASIC` authentication) preserves original behavior
- Users can opt-in to new features by explicitly configuring the new environment variable
- All original functionality remains intact and unchanged when using defaults