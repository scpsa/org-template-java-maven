# Java Maven Service

> Created from the [org-template-java-maven](https://github.com/scpsa/org-template-java-maven) template.

A minimal Java Maven service with CI pre-wired via [org-cicd](https://github.com/scpsa/org-cicd).

## What's included

| File | Purpose |
|------|---------|
| `pom.xml` | Maven project descriptor (Java 25, JUnit 5) |
| `.tool-versions` | [asdf](https://asdf-vm.com/) version pins (Java + Maven) |
| `.github/workflows/ci.yml` | CI via `org-cicd` — build, test, security scan |
| `src/main/java/com/example/App.java` | Starter application class |
| `src/test/java/com/example/AppTest.java` | Starter unit test |

## CI pipeline

Every push and pull request to `main` runs:

```
build ──► test ──► security scan (Trivy)
```

Powered by [`scpsa/org-cicd/.github/workflows/java-maven.yml@v1`](https://github.com/scpsa/org-cicd).

See [`org-cicd` docs](https://github.com/scpsa/org-cicd) for the full list of available inputs.

## Local setup

```bash
# Install asdf plugins + runtime versions
asdf install

# Build
mvn --batch-mode package

# Test
mvn --batch-mode test
```

## Customising the CI workflow

Edit `.github/workflows/ci.yml`:

```yaml
jobs:
  ci:
    uses: scpsa/org-cicd/.github/workflows/java-maven.yml@v1
    with:
      java-version: "temurin-25.0.2+10.0.LTS"  # override if needed
      working-directory: "."                    # path to pom.xml directory
      run-tests: true
      run-security-scan: true
      trivy-severity: "CRITICAL,HIGH"
```

## Renaming the project

1. Update `groupId`, `artifactId`, and `name` in `pom.xml`.
2. Rename the package directory `src/main/java/com/example` → your package.
3. Update the `package` declaration in `App.java` and `AppTest.java`.
