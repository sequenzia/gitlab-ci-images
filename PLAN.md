# GitLab CI Docker Build Pipeline - Implementation Plan

## Overview
Create a GitLab CI configuration that allows manual building of Docker images with flexible configuration options.

## Requirements Summary
1. **Manual trigger** - CI job triggered via GitLab web interface
2. **Multi-image support** - Build different images based on input
3. **Image name input** - Variable provided at trigger time
4. **Directory-based Dockerfiles** - Path: `<image-name>/Dockerfile`
5. **Per-image versioning** - Each image has its own version
6. **Build arguments support** - Pass additional args for some images

## Proposed Directory Structure
```
/
├── .gitlab-ci.yml
├── image-a/
│   ├── Dockerfile
│   └── VERSION
├── image-b/
│   ├── Dockerfile
│   └── VERSION
└── image-c/
    ├── Dockerfile
    └── VERSION
```

## Implementation Details

### Input Variables (at trigger time)
- `IMAGE_NAME` - Name of the image to build (required)
- `BUILD_ARGS` - Additional Docker build arguments (optional)
- `CUSTOM_TAG` - Optional custom tag override

### Versioning Strategy
- Each image directory contains a `VERSION` file
- Version is read from this file during build
- Images tagged with: version, latest (optional)

### Docker Registry
- TBD: GitLab Container Registry or external registry

### CI Job Configuration
- Use Docker-in-Docker (dind) or Kaniko for building
- Manual trigger with `when: manual`
- Variables defined with `variables:` block

## Open Questions
See clarifying questions below before implementation.
