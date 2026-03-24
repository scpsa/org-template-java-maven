# Java Maven Service

> Created from the [org-template-java-maven](https://github.com/scpsa/org-template-java-maven) template.

A Java Maven backend service, pre-wired with CI via [org-cicd](https://github.com/scpsa/org-cicd)
and a Docker-based deployment layout ready for docker-compose.

---

## Repository layout

```
svc-name/
├── code/                        # Application source (Maven project root)
│   ├── .tool-versions           # asdf version pins (Java + Maven)
│   ├── pom.xml
│   └── src/
│       ├── main/java/com/example/App.java
│       └── test/java/com/example/AppTest.java
├── docker/
│   ├── Dockerfile               # Production multi-stage image
│   └── Dockerfile.dev           # Dev image with debug port (optional)
├── deploy/
│   ├── docker-compose.yml       # Base service definition
│   ├── docker-compose.prod.yml  # Production overrides
│   ├── docker-compose.dev.yml   # Local dev overrides
│   └── .env.example             # Required env vars — copy to .env, never commit .env
├── docs/                        # Service documentation
├── ai/prompts/                  # AI prompts and PRDs
├── .dockerignore
├── .gitignore
├── CODEOWNERS
└── .github/workflows/ci.yml
```

Naming convention: `svc-<your-service-name>` — see
[org-cicd repo naming convention](https://github.com/scpsa/org-cicd/blob/main/docs/repo-naming-convention.md).

---

## CI pipeline

Every push and pull request to `main` runs:

```
build ──► test ──► security scan (Trivy)
```

Powered by [`scpsa/org-cicd/.github/workflows/java-maven.yml@v1`](https://github.com/scpsa/org-cicd).
See [`org-cicd` docs](https://github.com/scpsa/org-cicd) for the full input reference.

---

## Local setup

```bash
# 1. Install runtime versions (Java + Maven) via asdf
cd code && asdf install

# 2. Build
mvn --batch-mode package

# 3. Test
mvn --batch-mode test
```

---

## Running with Docker

```bash
# Copy the env template and fill in real values
cp deploy/.env.example deploy/.env

# Production
docker compose -f deploy/docker-compose.yml -f deploy/docker-compose.prod.yml up -d

# Local dev (hot-reload + debug port 5005)
docker compose -f deploy/docker-compose.yml -f deploy/docker-compose.dev.yml up
```

---

## Adjusting the CI workflow

Edit `.github/workflows/ci.yml`:

```yaml
jobs:
  ci:
    uses: scpsa/org-cicd/.github/workflows/java-maven.yml@v1
    with:
      java-version: "temurin-25.0.2+10.0.LTS"  # override if needed
      working-directory: "code"                 # path to pom.xml directory
      run-tests: true
      run-security-scan: true
      trivy-severity: "CRITICAL,HIGH"
```

---

## Renaming the project

1. Update `groupId`, `artifactId`, and `name` in `code/pom.xml`.
2. Rename the package directory `code/src/main/java/com/example` → your package path.
3. Update the `package` declaration in `App.java` and `AppTest.java`.
4. Update `IMAGE_NAME` in `deploy/.env.example` (and your `.env`).
