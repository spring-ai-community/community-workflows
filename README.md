# Spring AI Community Workflows

Shared infrastructure for Spring AI Community projects including reusable GitHub Actions workflows, release scripts, and project configuration templates.

## Repository Structure

```
community-workflows/
├── .github/
│   ├── workflows/
│   │   ├── ci-build.yml              # Reusable CI workflow
│   │   ├── publish-snapshot.yml      # Reusable snapshot publishing
│   │   └── maven-central-release.yml # Reusable release workflow
│   ├── community-projects.yml        # Project registry
│   └── project.yml.template          # Template for project config
├── examples/                         # Example configurations
├── spring-ai-community-release.py      # Release script
├── RELEASE.md                        # Detailed release documentation
└── README.md                         # This file
```

## Quick Start

### For New Projects

1. **Add CI workflow** (`.github/workflows/ci.yml`):
```yaml
name: CI Build
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    uses: spring-ai-community/community-workflows/.github/workflows/ci-build.yml@main
```

2. **Add snapshot publishing** (`.github/workflows/publish-snapshot.yml`):
```yaml
name: Publish Snapshot
on:
  push:
    branches: [main]

jobs:
  publish:
    uses: spring-ai-community/community-workflows/.github/workflows/publish-snapshot.yml@main
    secrets:
      MAVEN_USERNAME: ${{ secrets.MAVEN_USERNAME }}
      MAVEN_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
```

3. **Add release workflow** (`.github/workflows/release.yml`):
```yaml
name: Release
on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Release version'
        required: true

jobs:
  release:
    uses: spring-ai-community/community-workflows/.github/workflows/maven-central-release.yml@main
    with:
      version: ${{ inputs.version }}
    secrets:
      MAVEN_USERNAME: ${{ secrets.MAVEN_USERNAME }}
      MAVEN_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
      GPG_SECRET_KEY: ${{ secrets.GPG_SECRET_KEY }}
      GPG_PASSPHRASE: ${{ secrets.GPG_PASSPHRASE }}
```

### Release Script

```bash
# Dry run
python3 spring-ai-community-release.py agent-sandbox 0.1.0 --dry-run

# Actual release
python3 spring-ai-community-release.py agent-sandbox 0.1.0
```

## Setting Up Organization Secrets

### Required Secrets

Set these at the GitHub organization level (Settings > Secrets and variables > Actions):

| Secret | Description |
|--------|-------------|
| `MAVEN_USERNAME` | Sonatype Central Portal username |
| `MAVEN_PASSWORD` | Sonatype Central Portal token |
| `GPG_SECRET_KEY` | ASCII-armored GPG private key |
| `GPG_PASSPHRASE` | GPG key passphrase |

### Verifying Maven Central Credentials

1. **Web Portal**: Log in at https://central.sonatype.com with your credentials
2. **Test Deploy**: Try publishing a SNAPSHOT version to verify credentials work

### Getting GPG Key in ASCII Armor Format

```bash
# List your keys
gpg --list-secret-keys --keyid-format LONG

# Export private key (replace KEY_ID with your key ID)
gpg --armor --export-secret-keys KEY_ID > private-key.asc

# The contents of private-key.asc go into GPG_SECRET_KEY secret
```

## Projects

| Project | Description |
|---------|-------------|
| agent-sandbox | Process execution and workspace management |
| agent-judge | Judge framework for evaluating AI outputs |
| spring-ai-agents | Agent implementations for Spring AI |
| claude-agent-sdk-java | Claude Code SDK for Java |

## Documentation

See [RELEASE.md](RELEASE.md) for detailed documentation on:
- Reusable workflow inputs and secrets
- Release script features
- Migration guides
- POM requirements
