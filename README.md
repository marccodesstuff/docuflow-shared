# docuflow-shared

**Shared Protobuf/gRPC contracts for the DocuFlow platform.**

This repository contains the canonical `.proto` definitions that define the service contracts and data models used across all DocuFlow services. It is published as a Maven artifact and consumed by:

- `docuflow-core` (Spring Boot document processing pipeline)
- `docuflow-integrations` (ERP/EDI/webhook connectors)
- `docuflow-ml-sidecar` (Python ML inference service)
- `docuflow-api-gateway` (Laravel BFF via protobuf-php)

## Structure

```
proto/
└── docuflow/
    └── v1/
        └── document.proto     # All service definitions and messages
```

## Services Defined

| Service | Purpose |
|---------|---------|
| `DocumentService` | CRUD for documents, pages, metadata |
| `ProcessingJobService` | Pipeline job orchestration, retry, cancel |
| `ExtractionService` | ML extraction, validation, export |
| `WebhookService` | Webhook registration, delivery, testing |
| `MLInferenceService` | Classification, field extraction, table detection, element detection |

## Publishing

```bash
# Requires GPR_USER and GPR_KEY environment variables (GitHub Packages)
./gradlew publish
```

Published to: `https://maven.pkg.github.com/docuflow/docuflow-shared`

Coordinate: `com.docuflow.shared:docuflow-shared:1.0.0-SNAPSHOT`

## Generating Code

### Java (Gradle)
```bash
./gradlew generateProto
# Output: build/generated/source/proto/main/grpc-java / java
```

### PHP (for Laravel)
```bash
# Using protobuf-php and spiral/roadrunner
composer require spiral/roadrunner nyholm/psr7
vendor/bin/protoc --php_out=generated --grpc_out=generated --plugin=protoc-gen-grpc=vendor/bin/grpc_php_plugin proto/docuflow/v1/document.proto
```

### Python (for ML sidecar)
```bash
pip install grpcio-tools
python -m grpc_tools.protoc -Iproto --python_out=generated --grpc_python_out=generated proto/docuflow/v1/document.proto
```

## Versioning Strategy

- **MAJOR**: Breaking changes to message fields, service signatures, or enum values
- **MINOR**: New fields (optional), new services, new enum values
- **PATCH**: Documentation, comments, build fixes

All changes must be backward/forward compatible per [Protobuf compatibility guidelines](https://protobuf.dev/programming-guides/editions/).

## CI

GitHub Actions workflow:
- Validates `.proto` syntax (`buf lint` if available)
- Runs `./gradlew build publish` on tag push (`v*`)
- Publishes to GitHub Packages

## Local Development

```bash
# Build and install locally
./gradlew publishToMavenLocal

# In consumer project, add to settings.gradle.kts:
# mavenLocal()
```