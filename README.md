<!-- README.md -->
# pr-comment

<div align="center">

# 💬 PR Comment Template

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-PR%20Comment%20Template-6f42c1?style=for-the-badge&logo=github)](https://github.com/marketplace/actions/pr-comment-template)
[![Version](https://img.shields.io/github/v/release/Malnati/pr-comment?style=for-the-badge&color=purple)](https://github.com/Malnati/pr-comment/releases)
[![License](https://img.shields.io/github/license/Malnati/pr-comment?style=for-the-badge&color=blue)](LICENSE)

**Standardize, Automate, and Beautify your Pull Request feedback.**

<p align="center">
  <a href="#-features">Features</a> •
  <a href="#-basic-usage">Basic Usage</a> •
  <a href="#-advanced-usage-custom-templates">Custom Templates</a> •
  <a href="#-usage-examples">Usage Examples</a> •
  <a href="#-inputs">Inputs</a>
</p>

</div>

---

## 🚀 About

**PR Comment Template** is a GitHub Action designed for teams that value clear communication. It allows you to automatically post structured comments (Header, Body, Footer) in your PRs, ensuring that crucial information about deployments, tests, and validations doesn't get lost.

### ✨ Key Features

* **Standardization:** Ensure every PR has the same feedback format.
* **Flexibility:** Use the modern default template or bring your own **Markdown**.
* **Validation:** The system checks if your custom template contains the required variables.
* **Context Separation:** Dedicated areas for Message, Scope (changes), and TODOs.

---

## 📦 Basic Usage

Ideal for those who want to get started quickly using the default design (purple/clean theme).

```yaml
steps:
  - name: Post Standard Comment
    uses: Malnati/pr-comment@v4.0.2
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      pr_number: ${{ github.event.pull_request.number }}
      header_actor: ${{ github.actor }}
      header_title: "🧪 Testing default PR message"
      header_subject: "Branch Synchronization"
      body_message: "The synchronization was successfully performed by the bot."
      body_scope: |
        - Base: `main`
        - Head: `develop`
      footer_result: "Success"
```

---

## 🎨 Advanced Usage: Custom Templates

You can inject your own Markdown file to have full control over layout, emojis, and structure.

### 1. Create Your Template

Add a file to your repository (e.g., `.github/templates/custom.md`). You **must** include the variables below:

| Variable | Description |
| :--- | :--- |
| `$ACTOR` | The user who triggered the action (e.g., github.actor) |
| `$SUBJECT` | The comment subject |
| `$BODY_MESSAGE` | The main message |
| `$BODY_SCOPE_BLOCK` | Formatted scope list |
| `$BODY_TODO_BLOCK` | Formatted todo list |
| `$FOOTER_BLOCK` | The footer with results |

**Example `custom.md` file:**

```markdown
# 🎨 Deployment Report
**Author:** @$ACTOR | **Action:** $SUBJECT

> $BODY_MESSAGE

<details open>
<summary>📂 Change Scope</summary>
$BODY_SCOPE_BLOCK
</details>

---
$FOOTER_BLOCK
```

### 2. Configure the Workflow

You need to use `actions/checkout` so the Action can read your local template file.

```yaml
steps:
  - name: Checkout Repository
    uses: actions/checkout@v4

  - name: Post Custom Comment
    uses: Malnati/pr-comment@v4.0.2
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      pr_number: ${{ github.event.pull_request.number }}
      header_actor: ${{ github.actor }}
      header_title: "🚀 Staging Deploy"
      header_subject: "Automatic Deployment"
      body_message: "The staging environment has been updated."
      body_scope: "- Version: v1.2.0"
      
      # Relative path to your file
      template_path: ".github/templates/custom.md"
```

---

## 📚 Usage Examples

### Example 1: Code Review Template

```yaml
name: Automated Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  post-review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Post Review Checklist
        uses: Malnati/pr-comment@v4.0.2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.pull_request.number }}
          header_actor: ${{ github.actor }}
          header_title: "📋 Code Review Checklist"
          header_subject: "Automated Quality Check"
          body_message: "Please review the following items before merging:"
          body_scope: |
            - **PR Title:** ${{ github.event.pull_request.title }}
            - **Author:** @${{ github.event.pull_request.user.login }}
            - **Branch:** ${{ github.event.pull_request.head.ref }}
          body_todo: |
            - [ ] Unit tests passing
            - [ ] No breaking changes
            - [ ] Documentation updated
            - [ ] Code style consistent
          footer_result: "Review Required"
          footer_advise: "Please assign a reviewer and address all checkboxes."
```

### Example 2: Deployment Status Notification

```yaml
name: Deployment Status
on:
  workflow_run:
    workflows: ["Deploy"]
    types:
      - completed

jobs:
  deployment-notification:
    runs-on: ubuntu-latest
    steps:
      - name: Post Deployment Status
        uses: Malnati/pr-comment@v4.0.2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: 42  # Your PR number here
          header_actor: "Deployment Bot"
          header_title: "🚀 Deployment Completed"
          header_subject: "Production Environment"
          body_message: "The deployment to production has finished."
          body_scope: |
            - **Environment:** Production
            - **Status:** ${{ github.event.workflow_run.conclusion }}
            - **Workflow:** ${{ github.event.workflow_run.name }}
            - **Duration:** ${{ github.event.workflow_run.updated_at - github.event.workflow_run.run_started_at }}
          footer_result: "✅ Deployment Successful"
          footer_advise: "Monitor application metrics for any issues."
```

### Example 3: Security Scan Results with Custom Template

```yaml
name: Security Scan
on:
  pull_request:
    types: [synchronize]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Post Security Report
        uses: Malnati/pr-comment@v4.0.2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.pull_request.number }}
          header_actor: "Security Scanner"
          header_title: "🔒 Security Scan Results"
          header_subject: "Vulnerability Assessment"
          body_message: "Security scan completed for the latest changes."
          body_scope: |
            - **Scan Date:** $(date -u +"%Y-%m-%d %H:%M:%S UTC")
            - **Target Branch:** ${{ github.event.pull_request.head.ref }}
            - **Files Changed:** ${{ github.event.pull_request.changed_files }}
          body_todo: |
            - [ ] **Critical:** 0 vulnerabilities
            - [ ] **High:** 2 vulnerabilities
            - [ ] **Medium:** 5 vulnerabilities
            - [ ] **Low:** 3 vulnerabilities
          footer_result: "⚠️ Review Required"
          footer_advise: "Address high and medium severity issues before merging."
          template_path: ".github/templates/security-report.md"
```

**Custom Template `.github/templates/security-report.md`:**
```markdown
# 🔒 Security Assessment
**Scanner:** @$ACTOR | **Scope:** $SUBJECT

## Executive Summary
$BODY_MESSAGE

## Scan Details
$BODY_SCOPE_BLOCK

## Vulnerability Breakdown
$BODY_TODO_BLOCK

## Recommendations
1. Fix high severity issues immediately
2. Review medium severity within 48 hours
3. Low severity can be addressed in future sprints

---
**Status:** $FOOTER_BLOCK | **Next Steps:** Please review security findings
```

### Example 4: Test Coverage Report

```yaml
name: Test Coverage
on:
  pull_request:
    types: [closed]

jobs:
  coverage-report:
    runs-on: ubuntu-latest
    steps:
      - name: Generate Coverage Report
        id: coverage
        run: |
          echo "coverage=92" >> $GITHUB_OUTPUT
          echo "tests=150" >> $GITHUB_OUTPUT
          echo "passed=145" >> $GITHUB_OUTPUT
          echo "failed=5" >> $GITHUB_OUTPUT

      - name: Post Coverage Summary
        uses: Malnati/pr-comment@v4.0.2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.pull_request.number }}
          header_actor: "Test Runner"
          header_title: "📊 Test Coverage Report"
          header_subject: "PR #${{ github.event.pull_request.number }}"
          body_message: "Test execution completed for this pull request."
          body_scope: |
            - **Overall Coverage:** ${{ steps.coverage.outputs.coverage }}%
            - **Total Tests:** ${{ steps.coverage.outputs.tests }}
            - **Passed:** ${{ steps.coverage.outputs.passed }}
            - **Failed:** ${{ steps.coverage.outputs.failed }}
            - **Success Rate:** ${{ format('{0:.1f}', steps.coverage.outputs.passed / steps.coverage.outputs.tests * 100) }}%
          footer_result: "${{ steps.coverage.outputs.coverage >= 80 && '✅ Coverage Target Met' || '❌ Coverage Below Target' }}"
          footer_advise: "${{ steps.coverage.outputs.coverage >= 80 && 'Ready for merge' || 'Please improve test coverage before merging' }}"
```

### Example 5: Combined Workflow with Multiple Steps

```yaml
name: Complete PR Quality Gate
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  quality-checks:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run Linters
        id: lint
        run: |
          echo "lint_score=95" >> $GITHUB_OUTPUT
          echo "lint_issues=3" >> $GITHUB_OUTPUT

      - name: Run Tests
        id: test
        run: |
          echo "test_coverage=88" >> $GITHUB_OUTPUT
          echo "tests_passed=200" >> $GITHUB_OUTPUT
          echo "tests_failed=2" >> $GITHUB_OUTPUT

      - name: Run Security Scan
        id: security
        run: |
          echo "vulnerabilities=0" >> $GITHUB_OUTPUT
          echo "security_score=100" >> $GITHUB_OUTPUT

      - name: Post Quality Gate Summary
        uses: Malnati/pr-comment@v4.0.2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.pull_request.number }}
          header_actor: "Quality Gate Bot"
          header_title: "🏗️ Quality Gate Results"
          header_subject: "PR #${{ github.event.pull_request.number }} - ${{ github.event.pull_request.title }}"
          body_message: "All quality checks have been completed for this pull request."
          body_scope: |
            - **Author:** @${{ github.event.pull_request.user.login }}
            - **Branch:** ${{ github.event.pull_request.head.ref }}
            - **Changed Files:** ${{ github.event.pull_request.changed_files }}
          body_todo: |
            - [ ] **Linting:** ${{ steps.lint.outputs.lint_score }}/100 (${{ steps.lint.outputs.lint_issues }} issues)
            - [ ] **Test Coverage:** ${{ steps.test.outputs.test_coverage }}% (target: 80%)
            - [ ] **Tests:** ${{ steps.test.outputs.tests_passed }}/${{ steps.test.outputs.tests_passed + steps.test.outputs.tests_failed }} passed
            - [ ] **Security:** ${{ steps.security.outputs.security_score }}/100 (${{ steps.security.outputs.vulnerabilities }} vulnerabilities)
          footer_result: "${{ steps.lint.outputs.lint_score >= 90 && steps.test.outputs.test_coverage >= 80 && steps.security.outputs.vulnerabilities == 0 && '✅ Quality Gate Passed' || '❌ Quality Gate Failed' }}"
          footer_advise: "${{ steps.lint.outputs.lint_score >= 90 && steps.test.outputs.test_coverage >= 80 && steps.security.outputs.vulnerabilities == 0 && 'Ready for human review' || 'Please address the issues above before merging' }}"
```

---

## ⚙️ Inputs

All available configuration options.

| Input | Required | Type | Default | Description |
| :--- | :---: | :---: | :---: | :--- |
| `token` | **Yes** | Secret | - | GitHub token (e.g., `secrets.GITHUB_TOKEN`). |
| `pr_number` | **Yes** | Int | - | PR number where the comment will be posted. |
| `header_actor` | **Yes** | String | - | Name of the user or bot authoring the message. |
| `header_title` | **Yes** | String | - | Large title of the comment. |
| `header_subject` | **Yes** | String | - | Subtitle or specific subject. |
| `body_message` | **Yes** | Markdown | - | Main body of the message. |
| `body_scope` | No | Markdown | `""` | List of affected items or scope. |
| `body_todo` | No | Markdown | `""` | List of pending actions (checkboxes/bullets). |
| `footer_result` | No | String | `""` | Final summary (e.g., "Success", "Failure"). |
| `footer_advise` | No | String | `""` | Advice or next step. |
| `template_path` | No | String | `""` | Relative path to a custom `.md` file. |

## 📤 Outputs

| Output | Description |
| :--- | :--- |
| `comment_body` | The final processed Markdown content. Useful if you want to send the same text to Slack/Teams in subsequent steps. |

---

<div align="center">

<sub>Built with 🤍 by <a href="https://github.com/Malnati">Ricardo Malnati</a>.</sub>

</div>
