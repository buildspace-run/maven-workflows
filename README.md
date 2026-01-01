# Maven Workflows

This repository contains reusable GitHub Actions workflows designed to ensure the quality and consistency of Maven-based Java projects within the organization.

## Overview

The primary workflow provided is `maven_microservice.yml`, which orchestrates a complete CI/CD pipeline for Java applications. It handles building, testing, code quality analysis, and Docker image publication.

## Features

The `Build Maven microservice` workflow includes the following steps:

1.  **Environment Setup**:
    *   Sets up JDK 21 (Temurin distribution).
    *   Configures Maven caching to speed up builds.

2.  **Build & Test**:
    *   **Build**: Compiles the project using `mvn -B verify`.
    *   **Unit Tests**: Executes standard unit tests via `mvn -B test`.
    *   **Smoke Tests**: Runs a specific profile for smoke tests (`-Psmoke-tests`).

3.  **Code Quality**:
    *   **SonarCloud Scan**: Performs static code analysis using SonarCloud.
        *   Requires `SONAR_TOKEN` secret.
        *   Automatically derives organization and project keys from the GitHub repository context.
    *   **Dependency Graph**: Submits the Maven dependency graph to GitHub for security alerts (runs on push or internal PRs).

4.  **Containerization & Publishing**:
    *   **Multi-platform Build**: Supports `linux/amd64` and `linux/arm64` using QEMU and Docker Buildx.
    *   **Registry Login**: Authenticates with GitHub Container Registry (ghcr.io).
    *   **Metadata Extraction**: Generates Docker tags based on:
        *   `latest` (default branch).
        *   Semantic versioning tags.
        *   Branch refs with timestamps and SHAs.
        *   PR refs.
    *   **Build & Push**: Builds the Docker image and pushes it to the registry (push is skipped for PRs).
    *   **Caching**: Utilizes GitHub Actions cache (`type=gha`) for Docker layers to improve performance.

## Usage

To use this workflow in your repository, you can reference it or copy the configuration. Ensure your repository has the following secrets configured if you plan to use the SonarCloud integration:

*   `SONAR_TOKEN`: Token for authenticating with SonarCloud.

## Triggers

The workflow is triggered on:
*   `push` to the `main` branch.
*   `pull_request` targeting the `main` branch.

## Permissions

The workflow requires the following permissions:
*   `contents: write`: To submit dependency graphs.
*   `packages: write`: To push images to the GitHub Container Registry.
