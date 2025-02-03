# CSI Action

> CSI = Checkout code, Setup node, Install dependencies

This GitHub Action automates the essential setup process for Node.js projects in your workflows. It intelligently handles dependency installation across multiple package managers and provides flexible authentication options.

## Features

- 🔄 Smart package manager detection (npm, yarn, pnpm, bun)
- 📦 Zero-config package manager installation
- 🎯 Customizable Node.js version
- 🔐 Flexible authentication (PAT or GitHub App)

## How it works

1. Handles authentication by:
   - Using provided Personal Access Token (PAT), or
   - Generating a token from GitHub App credentials
2. Checks out your repository
3. Configures Node.js environment
4. Auto-detects package manager from lock files
5. Sets up detected package manager (if not npm)
6. Installs project dependencies

## Inputs

- `node-version`: Node.js version to install (default: 20)
- `token`: GitHub Personal Access Token (optional)
- `app-id`: GitHub App ID for authentication (optional)
- `private-key`: GitHub App private key (optional)

## Outputs

> None

## Example

```yaml
name: Format code
run-name: Format code

on: [push]

jobs:
  format-code:
    name: Format code

    if: ${{ github.event.head_commit.message != "style: format code" }}

    runs-on: ubuntu-latest

    steps:
      - name: CSI
        uses: trdev20-actions/csi@v1
        with:
          node-version: 20
          token: ${{ secrets.PAT }} # A token with the necessary permissions (including workflows permission) to format workflow files

      - name: Check format
        id: check-format
        run: |
          yarn run format:check || echo "needs-format" >> $GITHUB_OUTPUT

      - name: Format code
        if: steps.check-format.outputs.needs-format == 'true'
        run: |
          yarn run format

      - name: Commit changes
        if: steps.check-format.outputs.needs-format == 'true'
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "style: format code"
          git push
```
