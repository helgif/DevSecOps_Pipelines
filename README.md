# Reusable Security Pipeline - Golden Template

A comprehensive, production-ready GitHub Actions workflow template for security scanning that can be reused across multiple repositories.

## 🎯 Overview

This reusable workflow provides a complete security pipeline that addresses vulnerabilities in AI-generated code and follows DevSecOps best practices. It's designed to be a "golden template" that teams can adopt with minimal configuration.

## ✨ Features

- **🔐 Secret Scanning**: TruffleHog, GitGuardian
- **🔍 SAST**: CodeQL, Snyk Code, SonarCloud
- **📦 Dependency Scanning**: GitHub Dependency Review, Snyk, OWASP Dependency-Check
- **🐳 Container Security**: Trivy, Snyk Container
- **☁️ Infrastructure as Code**: Checkov, tfsec, Snyk IaC
- **🧪 Integration Testing**: DAST, API Security
- **📊 Security Reporting**: Aggregated SARIF, metrics, notifications

## 🚀 Quick Start

### 1. Set Up the Template Repository

Create a central repository to host your security templates:

```bash
# Create the repository
gh repo create YOUR-ORG/security-templates --public

# Clone and set up structure
git clone https://github.com/YOUR-ORG/security-templates
cd security-templates
mkdir -p .github/workflows
```

### 2. Add the Reusable Workflow

Copy `security-pipeline-template.yml` to `.github/workflows/` in your template repository:

```bash
cp security-pipeline-template.yml .github/workflows/
git add .github/workflows/security-pipeline-template.yml
git commit -m "Add reusable security pipeline template"
git push
```

### 3. Use in Your Application Repository

Create `.github/workflows/security.yml` in your application repository:

```yaml
name: Security Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  security:
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    with:
      node_version: '20'
      enable_fuzzing: true
    secrets:
      SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

## 📋 Configuration Options

### Required Inputs

None! The template works out-of-the-box with sensible defaults.

### Optional Inputs

#### Language & Runtime Versions
| Input | Default | Description |
|-------|---------|-------------|
| `node_version` | `'18'` | Node.js version |
| `python_version` | `'3.11'` | Python version |
| `java_version` | `'17'` | Java version |
| `go_version` | `'1.21'` | Go version |

#### Severity Thresholds
| Input | Default | Options |
|-------|---------|---------|
| `sast_severity_threshold` | `'high'` | `low`, `medium`, `high`, `critical` |
| `dependency_severity_threshold` | `'high'` | `low`, `medium`, `high`, `critical` |
| `container_severity_threshold` | `'critical'` | `low`, `medium`, `high`, `critical` |
| `iac_severity_threshold` | `'high'` | `low`, `medium`, `high`, `critical` |

#### Feature Flags
| Input | Default | When to Enable |
|-------|---------|----------------|
| `enable_mutation_testing` | `false` | Critical applications requiring deep testing |
| `enable_fuzzing` | `true` | Public APIs, security-critical code |
| `enable_compliance_scanning` | `true` | Regulated industries (healthcare, finance) |
| `enable_performance_testing` | `true` | Production services with SLAs |
| `enable_drift_detection` | `true` | Cloud-deployed infrastructure |

#### Stage Control
| Input | Default | Use Case |
|-------|---------|----------|
| `skip_pre_commit` | `false` | Never skip - first line of defense |
| `skip_sast` | `false` | Skip if using external SAST tools |
| `skip_dependency` | `false` | Skip if using external SCA tools |
| `skip_container` | `false` | Skip for non-containerized apps |
| `skip_iac` | `false` | Skip for apps without infrastructure code |

### Secrets Configuration

#### Secret Scanning
- `GITGUARDIAN_API_KEY` - Optional, enables GitGuardian scanning

#### SAST Tools
- `SNYK_TOKEN` - Enables Snyk Code, Container, IaC scanning
- `SONAR_TOKEN` - Enables SonarCloud/SonarQube scanning
- `SONAR_HOST_URL` - Required for self-hosted SonarQube
- `CHECKMARX_URL`, `CHECKMARX_USERNAME`, `CHECKMARX_PASSWORD` - For Checkmarx

#### Container & Cloud
- `DOCKER_USERNAME`, `DOCKER_PASSWORD` - For private registries
- `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION` - For IaC validation

#### Notifications
- `SLACK_WEBHOOK` - Enables Slack notifications
- `JIRA_URL`, `JIRA_AUTH` - Enables JIRA ticket creation
- `DEFECTDOJO_URL`, `DEFECTDOJO_TOKEN`, `DEFECTDOJO_ENGAGEMENT_ID` - For DefectDojo

## 📚 Usage Patterns

### Pattern 1: Basic Security (Getting Started)

Perfect for new teams or projects just starting with security scanning:

```yaml
jobs:
  security:
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    secrets: inherit
```

**Execution Time**: ~15-20 minutes  
**What it includes**: Secret scanning, CodeQL, dependency scanning, basic container checks

### Pattern 2: PR Fast Feedback

Optimized for fast feedback on pull requests:

```yaml
jobs:
  security-pr:
    if: github.event_name == 'pull_request'
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    with:
      skip_container: true
      skip_iac: true
      enable_fuzzing: false
      enable_compliance_scanning: false
      sast_severity_threshold: 'high'
    secrets:
      SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

**Execution Time**: ~5-10 minutes  
**What it includes**: Essential security checks for quick PR validation

### Pattern 3: Comprehensive Scheduled Scan

Full-depth security scanning for nightly runs:

```yaml
jobs:
  security-scheduled:
    if: github.event_name == 'schedule'
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    with:
      enable_mutation_testing: true
      enable_fuzzing: true
      enable_compliance_scanning: true
      sast_severity_threshold: 'low'
      dependency_severity_threshold: 'low'
      send_slack_notifications: true
      create_jira_tickets: true
    secrets: inherit
```

**Execution Time**: ~45-60 minutes  
**What it includes**: All security stages with strictest settings

### Pattern 4: Multi-Stage Strategy

Different configurations for different events:

```yaml
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'

jobs:
  # Fast PR checks
  security-pr:
    if: github.event_name == 'pull_request'
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    with:
      skip_container: true
      enable_fuzzing: false
    secrets: inherit

  # Standard main branch checks
  security-main:
    if: github.event_name == 'push'
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    secrets: inherit

  # Deep nightly scans
  security-scheduled:
    if: github.event_name == 'schedule'
    uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
    with:
      enable_mutation_testing: true
      sast_severity_threshold: 'low'
    secrets: inherit
```

## 🔧 Implementation Guide

### Phase 1: Foundation (Week 1-2)

1. **Set up template repository**
   - Create central templates repo
   - Add reusable workflow
   - Configure basic secrets

2. **Enable in pilot project**
   - Choose low-risk project
   - Use basic configuration
   - Monitor execution time

3. **Configure free tools**
   - TruffleHog (secret scanning)
   - CodeQL (SAST)
   - GitHub Dependency Review
   - Trivy (container scanning)
   - Checkov (IaC scanning)

**Expected Results**: 15-20 minute pipeline, basic security coverage

### Phase 2: Commercial Tools (Week 3-6)

1. **Add Snyk**
   - Sign up for Snyk account
   - Generate API token
   - Add to GitHub secrets
   - Enable Snyk Code, Container, IaC

2. **Add SonarCloud**
   - Connect GitHub organization
   - Generate token
   - Configure quality gates

3. **Tune thresholds**
   - Review initial findings
   - Adjust severity thresholds
   - Document exceptions

**Expected Results**: 25-35 minute pipeline, comprehensive coverage

### Phase 3: Advanced Features (Week 7-12)

1. **Enable notifications**
   - Configure Slack webhook
   - Set up JIRA integration
   - Configure DefectDojo (optional)

2. **Add compliance scanning**
   - Enable PCI-DSS checks
   - Configure HIPAA scanning (if applicable)
   - Set up audit logging

3. **Optimize performance**
   - Enable caching
   - Parallelize independent jobs
   - Implement conditional execution

**Expected Results**: 35-45 minute pipeline, full DevSecOps

### Phase 4: Enterprise Scale (Month 4+)

1. **Roll out organization-wide**
   - Document best practices
   - Train development teams
   - Create runbooks

2. **Advanced integrations**
   - DefectDojo for vulnerability management
   - Custom compliance frameworks
   - Advanced DAST with authenticated scans

3. **Continuous improvement**
   - Regular security metrics review
   - Tool effectiveness analysis
   - Pipeline optimization

## 🎯 Best Practices

### Security Best Practices

1. **Never commit secrets**
   - Always use GitHub Secrets
   - Use `.gitignore` for sensitive files
   - Enable secret scanning

2. **Enable branch protection**
   - Require security checks to pass
   - Require code review
   - Restrict who can push to main

3. **Regular reviews**
   - Review security findings weekly
   - Update dependencies monthly
   - Audit secrets quarterly

4. **Least privilege**
   - Use minimal permissions
   - Rotate credentials regularly
   - Use OIDC when possible

### Performance Best Practices

1. **Use caching**
   - Cache dependencies (npm, pip, Maven)
   - Cache build artifacts
   - Cache Docker layers

2. **Conditional execution**
   - Run container scans only when Dockerfile changes
   - Run IaC scans only when terraform files change
   - Skip optional tests on PRs

3. **Parallel execution**
   - Independent stages run in parallel
   - Use job dependencies wisely
   - Don't block on optional stages

4. **Progressive scanning**
   - Fast checks on every commit
   - Comprehensive checks on main
   - Deep scans on schedule

### Operational Best Practices

1. **Start small**
   - Begin with basic configuration
   - Add tools incrementally
   - Validate each addition

2. **Document everything**
   - Create runbooks for failures
   - Document exceptions and waivers
   - Maintain security changelog

3. **Monitor and improve**
   - Track false positive rate
   - Measure time to remediation
   - Optimize pipeline performance

4. **Train your teams**
   - Security awareness training
   - Tool-specific workshops
   - Share lessons learned

## 🔍 Troubleshooting

### Common Issues

#### "Workflow not found" error
- Ensure template repo is public or caller has access
- Verify workflow file path is correct
- Check that you're using correct branch/tag

#### Secrets not passing through
- Use `secrets: inherit` to pass all secrets
- Explicitly pass required secrets
- Verify secrets exist in repository settings

#### Pipeline too slow
- Use conditional stage execution
- Enable caching
- Skip optional stages
- Run deep scans on schedule only

#### Too many false positives
- Tune severity thresholds
- Configure tool-specific ignore files
- Create exception management process

## 📊 Metrics and Reporting

### Key Metrics to Track

1. **Pipeline Performance**
   - Average execution time
   - Success rate
   - Failure causes

2. **Security Posture**
   - Vulnerabilities by severity
   - Time to remediation
   - Open vs closed findings

3. **Coverage**
   - Code coverage percentage
   - Scan coverage by stage
   - Tool effectiveness

### Viewing Results

- **GitHub Security Tab**: `https://github.com/ORG/REPO/security`
- **Pull Request Comments**: Automated feedback on PRs
- **Artifacts**: Download SARIF files for detailed analysis
- **DefectDojo**: Centralized vulnerability management (if configured)

## 🔄 Versioning Strategy

### Template Versioning

Use semantic versioning for the template:

```yaml
# Use specific version (recommended for production)
uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@v1.0.0

# Use major version (get patches and minor updates)
uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@v1

# Use main branch (always latest, less stable)
uses: YOUR-ORG/security-templates/.github/workflows/security-pipeline-template.yml@main
```

### Updating the Template

1. Make changes in feature branch
2. Test in pilot project
3. Create PR to main
4. After merge, create release tag
5. Update consuming repositories

## 📚 Additional Resources

- [GitHub Actions Reusable Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- [SARIF Format Specification](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html)
- [OWASP DevSecOps Guideline](https://owasp.org/www-project-devsecops-guideline/)
- [NIST Secure Software Development Framework](https://csrc.nist.gov/projects/ssdf)

## 🤝 Contributing

To improve this template:

1. Fork the security-templates repository
2. Create a feature branch
3. Test changes thoroughly
4. Submit a pull request with:
   - Description of changes
   - Test results from pilot project
   - Performance impact analysis

## 📝 License

This template is provided as-is for use within your organization. Adjust as needed for your security requirements and compliance obligations.

## 🆘 Support

- **Internal**: Contact DevSecOps team or #security-pipeline Slack channel
- **Issues**: Create issue in security-templates repository
- **Questions**: Use Discussions in security-templates repository

---

**Last Updated**: December 2025  
**Version**: 1.0.0  
**Maintained by**: DevSecOps Team
