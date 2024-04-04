# Soroban Smart Contract Build and Release Workflow

This reusable GitHub Actions workflow streamlines the building and release process for smart contracts. It's designed to automate contract compilation, generate WebAssembly (.wasm) artifacts, and create GitHub release with attached build artifacts.

## Key Features

- **Multiple Contract Support**: Handles builds for multiple contract directories simultaneously.
- **Release Automation**: Creates GitHub releases, including a tag name, description (optional), and automatically attaches compiled .wasm files.
- **Hash Verification**: Includes SHA256 hashes of the .wasm files in build output for verification.

## Usage

### Workflow Inputs and Secrets
```yaml
- uses: stellar-expert/soroban-build.action/.github/workflows/create-contract-release.yml@v1.0.0
  with:
    # JSON-encoded array of relative path to the contract directories. Empty string for root directory. 
    # Default is '[""]'
    contract_dirs:

    # The name of the release. 
    # Required.
    release_name:

    # Description for the release.
    # Optional.
    release_description:
  secrets:
    # GitHub token for creating releases.
    # Required.
    release_token:
```

### Example Workflow

1. Create a new workflow file (e.g., `.github/workflows/release.yml`) in your repository.
2. Trigger the workflow by pushing a new tag to the repository.

```yaml
name: Build and Release

on:
  push: 
    tags:
      - '*'

jobs:
  release_contracts:
    uses: stellar-expert/soroban-build.action/.github/workflows/create-contract-release.yml@v1.0.0
    with:
        release_name: 'v1.0.0' # Release name
        release_description: 'Initial release' # Release description
        contract_dirs: '["contract1", "contract2", ""]' # List of contract directories to build. Empty string for root directory.
    secrets:
        release_token: ${{ secrets.GITHUB_TOKEN }}
```

## Important Notes
- The workflow assumes that each contract directory contains the necessary structure and files for your build process.
- The workflow doesn't run tests. The workflow focuses solely on compilation and release; it does not include testing steps.
- The workflow doesn't check the contract logic or the build logic.
- Target contracts cannot have the same name and version. This restriction is necessary to avoid conflicts during the release process.