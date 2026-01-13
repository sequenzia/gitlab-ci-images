# Example base image
# This is a sample Dockerfile for a base image that other images can build upon

FROM alpine:3.19

LABEL maintainer="your-team@example.com"
LABEL description="Example base image with common tools"

# Install common utilities
RUN apk add --no-cache \
    bash \
    curl \
    ca-certificates \
    tzdata

# Set default timezone
ENV TZ=UTC

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

USER appuser

CMD ["/bin/bash"]
