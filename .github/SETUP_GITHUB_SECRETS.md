# GitHub Actions Setup Guide

This document explains how to configure GitHub Secrets for the A2UI CI/CD pipeline.

## Overview

The CI/CD workflow (`.github/workflows/ci.yml`) includes:

✅ **Security scanning** - Checks for accidentally committed secrets
✅ **TypeScript build** - Builds and type-checks renderers
✅ **Python testing** - Runs Python SDK tests
✅ **A2UI Evaluations** - Runs LLM evaluations (optional, requires API key)

## Required Secrets

### ANTHROPIC_API_KEY (Required for Evals)

To enable the A2UI evaluations job in CI/CD:

1. **Get your API key:**
   - Go to https://console.anthropic.com/account/keys
   - Create or copy an existing API key

2. **Add to GitHub Secrets:**
   - Go to: `https://github.com/newworldorderhere-collab/A2UI/settings/secrets/actions`
   - Click "New repository secret"
   - Name: `ANTHROPIC_API_KEY`
   - Value: Paste your API key (e.g., `sk-ant-...`)
   - Click "Add secret"

3. **Verify:**
   - The secret is now available to GitHub Actions
   - It will appear masked in logs (showing as `***`)
   - Workflows can access it via `${{ secrets.ANTHROPIC_API_KEY }}`

## Optional Secrets

### GEMINI_API_KEY (For future eval enhancements)

If you want to run evaluations with Google Gemini models:

1. Get your API key from: https://aistudio.google.com/app/apikey
2. Add as `GEMINI_API_KEY` secret (same process as above)

## Workflow Behavior

### On Push to Main

The following checks run automatically:

1. **Security Scan** (🔒 Required)
   - Checks for leaked secrets
   - Must pass before merging

2. **TypeScript Build** (✅ Required)
   - Installs dependencies
   - Builds all renderers
   - Type-checks code

3. **Python Tests** (⚠️ Optional)
   - Runs SDK and eval tests
   - Warnings don't block merge

4. **A2UI Evaluations** (⚠️ Optional)
   - Only runs on push to main
   - Requires `ANTHROPIC_API_KEY`
   - Tests 10 samples by default
   - Threshold: 50% pass rate

### On Pull Request

Same checks run, but evals are **skipped** to save API costs.

## Viewing Results

1. **Go to Actions tab:**
   - https://github.com/newworldorderhere-collab/A2UI/actions

2. **Click on a workflow run** to see detailed logs

3. **Failed steps** show error details and logs

## Troubleshooting

### Secret Not Working

If the workflow says "ANTHROPIC_API_KEY: not available":

1. Verify secret was added: Settings → Secrets and variables → Actions
2. Check the secret name matches exactly: `ANTHROPIC_API_KEY`
3. Secrets added after a workflow run won't affect that run - run it again

### Rate Limit Errors

If you see "rate limit exceeded":

1. Reduce `--max-samples` in the evals job
2. Add delays between runs
3. Or disable evals for non-main branches

### Python Version Mismatch

If Python 3.14 is not available:

1. Edit `.github/workflows/ci.yml`
2. Change `python-version: "3.14"` to `"3.13"`
3. Commit and push

## Advanced: Customizing the Workflow

Edit `.github/workflows/ci.yml` to:

- Change which Python/Node versions to test
- Add more build steps
- Adjust eval thresholds
- Add deployment steps
- Add notifications (Slack, etc.)

For GitHub Actions syntax, see: https://docs.github.com/en/actions

## Security Best Practices

🔒 **Never commit secrets** - GitHub will warn you

🔒 **Rotate keys regularly** - Especially if compromised

🔒 **Use branch protection** - Require passing checks before merge

🔒 **Review secret access** - Only workflows that need them should have access

## Next Steps

1. Add `ANTHROPIC_API_KEY` secret (instructions above)
2. Push a commit to trigger the workflow
3. Go to Actions tab to monitor the run
4. Check logs for any issues

Questions? See `.github/workflows/ci.yml` for the complete workflow configuration.
