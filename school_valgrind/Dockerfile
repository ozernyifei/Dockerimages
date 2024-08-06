# Stage 1: Build
FROM ubuntu:22.04 AS build

# Set environment variables to avoid user interaction during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install build tools and dependencies
RUN apt-get update && \
    apt-get install -y \
    build-essential \
    libgtest-dev \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create a directory for the app
WORKDIR /usr/src/app

# Copy the source code and Makefile into the container
COPY src/ .

# Build the project
RUN make test

# Stage 2: Runtime
FROM ubuntu:22.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

# Install only runtime dependencies
RUN apt-get update && \
    apt-get install -y \
    valgrind \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /usr/src/app

# Copy the built executable from the build stage
COPY --from=build /usr/src/app/test .

# Set the entry point to run the tests and Valgrind
ENTRYPOINT ["sh", "-c", "valgrind --leak-check=yes -s ./test > /var/log/valgrind.log 2>&1 && cat /var/log/valgrind.log"]
