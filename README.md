# DAITICS CI/CD Pipeline Templates

Reusable GitLab CI pipeline templates for standardized build, test, and deployment workflows.

## Overview

These templates provide:
- **Vault Integration**: Secure secrets management via HashiCorp Vault
- **SonarQube Analysis**: Code quality scanning with quality gate validation
- **Kaniko Builds**: Secure Docker builds without privileged mode
- **GitOps Deployment**: Kubernetes deployments via ArgoCD

## Directory Structure

```
templates/
├── base/
│   ├── stages.yml          # Standard pipeline stages
│   └── vault-auth.yml      # Vault AppRole authentication
│
├── jobs/
│   ├── build-kaniko.yml    # Docker build with Kaniko
│   ├── test-unit.yml       # Unit testing template
│   ├── sonarqube-scan.yml  # SonarQube analysis
│   ├── quality-gate.yml    # Quality gate validation
│   └── deploy-gitops.yml   # GitOps deployment (ArgoCD)
│
├── pipelines/
│   ├── java-springboot.yml # Complete Java/Spring Boot pipeline
│   ├── nodejs-react.yml    # Complete Node.js/React pipeline
│   └── python.yml          # Complete Python pipeline
│
└── utils/
    └── github-status-reporter.yml  # GitHub PR status updates
```

## Quick Start

### For Java/Spring Boot Applications

```yaml
# .gitlab-ci.yml
include:
  - project: 'daitics-com/ci-cd-templates'
    file: '/templates/pipelines/java-springboot.yml'

variables:
  APP_NAME: my-java-app
  JAVA_VERSION: "21"
```

### For Node.js/React Applications

```yaml
# .gitlab-ci.yml
include:
  - project: 'daitics-com/ci-cd-templates'
    file: '/templates/pipelines/nodejs-react.yml'

variables:
  APP_NAME: my-react-app
  NODE_VERSION: "22"
```

### For Python Applications

```yaml
# .gitlab-ci.yml
include:
  - project: 'daitics-com/ci-cd-templates'
    file: '/templates/pipelines/python.yml'

variables:
  APP_NAME: my-python-app
  PYTHON_VERSION: "3.12"
```

## Pipeline Stages

```
┌─────────┐   ┌─────────┐   ┌──────────────┐   ┌─────────┐   ┌─────────┐
│  Build  │──►│  Test   │──►│ Code Quality │──►│ Package │──►│ Deploy  │
└─────────┘   └─────────┘   └──────────────┘   └─────────┘   └─────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
              sonarqube-scan              quality-gate
```

## Deployment Strategy

| Branch | Environment | Approval Required |
|--------|-------------|-------------------|
| `develop` | Development | No (automatic) |
| `main/master` | Production | Yes (manual click) |

## Prerequisites

### GitLab CI/CD Variables (Project or Group Level)

| Variable | Description | Protected |
|----------|-------------|-----------|
| `VAULT_ADDR` | Vault server URL | No |
| `VAULT_ROLE_ID` | AppRole Role ID | Yes |
| `VAULT_SECRET_ID` | AppRole Secret ID | Yes |
| `SONAR_HOST_URL` | SonarQube server URL | No |

### Vault Secrets Required

| Path | Keys |
|------|------|
| `secret/cicd/sonarqube` | `ci_token` |
| `secret/cicd/gitlab` | `deploy_token` |
| `secret/cicd/github` | `access_token` (optional) |

## Branch Protection (Configure in GitHub)

Since code is mirrored from GitHub to GitLab:

1. Go to GitHub → Repository → Settings → Branches
2. Add protection rule for `main` and `develop`
3. Enable:
   - Require pull request before merging
   - Require status checks to pass:
     - `ci/gitlab/build/build`
     - `ci/gitlab/test/test`
     - `ci/gitlab/code-quality/sonarqube-scan`
     - `ci/gitlab/code-quality/quality-gate`
   - Require approvals (1 or more)

## Support

For issues or questions, contact the Platform Team.