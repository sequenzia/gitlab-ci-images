# GitLab CI Docker Build Pipeline - Implementation Plan

## Overview
Create a GitLab CI configuration that allows manual building of Docker images with flexible configuration options.

## Requirements Summary
1. **Manual trigger** - CI job triggered via GitLab web interface
2. **Multi-image support** - Build different images based on input
3. **Image name input** - Variable provided at trigger time
4. **Directory-based Dockerfiles** - Path: `<image-name>/Dockerfile`
5. **Per-image versioning** - Each image has its own version with bump support
6. **Build arguments support** - Pass additional args for some images

## Directory Structure
```
/
├── .gitlab-ci.yml
├── example-app/
│   ├── Dockerfile
│   └── VERSION
└── example-base/
    ├── Dockerfile
    └── VERSION
```

## Implementation Details

### Input Variables (at trigger time)
| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `IMAGE_NAME` | Yes | - | Name of the image to build (must match directory name) |
| `VERSION_BUMP` | No | `none` | Version bump type: `none`, `patch`, `minor`, `major` |
| `BUILD_ARGS` | No | - | Additional build args in comma-separated format: `KEY1=val1,KEY2=val2` |

### Versioning Strategy
- Each image directory contains a `VERSION` file with semantic version (e.g., `1.0.0`)
- Version bump options:
  - `none` - Use current version as-is
  - `patch` - Bump patch version (1.0.0 → 1.0.1)
  - `minor` - Bump minor version (1.0.0 → 1.1.0)
  - `major` - Bump major version (1.0.0 → 2.0.0)
- VERSION file is updated and committed after successful build (if bumped)

### Tagging Strategy
Each built image will be tagged with:
1. Version tag: `<image-name>:<version>` (e.g., `myapp:1.2.3`)
2. Latest tag: `<image-name>:latest`
3. Git SHA tag: `<image-name>:<commit-sha>` (short SHA)

### Docker Registry (Harbor)
- Registry URL: CI/CD variable `HARBOR_REGISTRY` (e.g., `harbor.example.com`)
- Project name: CI/CD variable `HARBOR_PROJECT`
- Credentials: CI/CD variables `HARBOR_USERNAME` and `HARBOR_PASSWORD`
- Full image path: `$HARBOR_REGISTRY/$HARBOR_PROJECT/$IMAGE_NAME:<tag>`

### CI Job Configuration
- **Runner**: Docker-in-Docker (dind) with privileged mode
- **Trigger**: Manual (`when: manual`)
- **Docker version**: 24.0 (stable)

## CI/CD Variables to Configure in GitLab
The following variables must be set in GitLab CI/CD settings:

| Variable | Type | Description |
|----------|------|-------------|
| `HARBOR_REGISTRY` | Variable | Harbor registry URL (e.g., `harbor.example.com`) |
| `HARBOR_PROJECT` | Variable | Harbor project name |
| `HARBOR_USERNAME` | Variable | Harbor username |
| `HARBOR_PASSWORD` | Variable (masked) | Harbor password |

## Usage Examples

### Build image without version bump
```
IMAGE_NAME: my-app
VERSION_BUMP: none
```

### Build image with patch version bump
```
IMAGE_NAME: my-app
VERSION_BUMP: patch
```

### Build image with additional build arguments
```
IMAGE_NAME: my-app
VERSION_BUMP: minor
BUILD_ARGS: NODE_VERSION=18,ENVIRONMENT=production
```
