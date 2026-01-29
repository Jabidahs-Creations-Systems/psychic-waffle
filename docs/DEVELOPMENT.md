# Development Setup and Debugging Guide

This guide explains how to set up your development environment and use the debugging configurations for TruffleHog.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [VSCode Setup](#vscode-setup)
3. [Debugging](#debugging)
4. [Tasks](#tasks)
5. [PR Workflow](#pr-workflow)

## Prerequisites

Before you begin, ensure you have the following installed:

- **Go** (version 1.19 or later)
- **VSCode** (Visual Studio Code)
- **Go extension for VSCode** (`golang.go`)
- **golangci-lint** (for linting)
- **make** (for running Makefile targets)

### Installing Go Extension

1. Open VSCode
2. Go to Extensions (Ctrl+Shift+X or Cmd+Shift+X)
3. Search for "Go"
4. Install the official Go extension by Go Team at Google

## VSCode Setup

The `.vscode` directory contains pre-configured files for debugging and running tasks:

- **launch.json** - Debug configurations
- **tasks.json** - Build and test tasks
- **settings.json** - Go-specific editor settings

These configurations are already set up and ready to use!

## Debugging

### Available Debug Configurations

#### 1. Debug TruffleHog
Debugs the main TruffleHog application with default arguments (scanning current directory).

**Usage:**
1. Press `F5` or go to Run and Debug (Ctrl+Shift+D)
2. Select "Debug TruffleHog" from the dropdown
3. Press the green play button or F5

#### 2. Debug TruffleHog with Custom Args
Debugs TruffleHog with custom command-line arguments.

**Usage:**
1. Edit `.vscode/launch.json`
2. Add your custom args to the "args" array
3. Select "Debug TruffleHog with Custom Args"
4. Press F5

**Example:**
```json
"args": [
  "github",
  "--org=trufflesecurity",
  "--results=verified,unknown"
]
```

#### 3. Debug Current Test
Debugs the test file you currently have open.

**Usage:**
1. Open a Go test file (e.g., `something_test.go`)
2. Set breakpoints where needed
3. Select "Debug Current Test"
4. Press F5

#### 4. Debug Test Package
Debugs all tests in the package of the currently open file.

**Usage:**
1. Open any file in the package you want to test
2. Select "Debug Test Package"
3. Press F5

#### 5. Debug Integration Tests
Runs integration tests with the `-tags=integration` build flag.

**Usage:**
1. Open an integration test file
2. Select "Debug Integration Tests"
3. Press F5

#### 6. Debug Detector Tests
Runs detector tests with the `-tags=detectors` build flag.

**Usage:**
1. Open a detector test file (usually in `pkg/detectors/`)
2. Select "Debug Detector Tests"
3. Press F5

#### 7. Attach to Process
Attaches the debugger to an already running Go process.

**Usage:**
1. Start a TruffleHog process
2. Note its process ID (PID)
3. Select "Attach to Process"
4. Enter the PID when prompted

### Setting Breakpoints

1. Click in the gutter (left of line numbers) to set a breakpoint
2. The breakpoint will appear as a red dot
3. When debugging, execution will pause at breakpoints
4. Use the debug toolbar to step through code:
   - Continue (F5)
   - Step Over (F10)
   - Step Into (F11)
   - Step Out (Shift+F11)
   - Restart (Ctrl+Shift+F5)
   - Stop (Shift+F5)

## Tasks

Tasks automate common development operations. Access them via:
- Terminal → Run Task (or Ctrl+Shift+B)

### Available Tasks

#### Build Tasks

- **build** (default) - Builds the TruffleHog binary
  ```bash
  Ctrl+Shift+B  # Quick access
  ```

#### Test Tasks

- **test** (default test) - Runs all tests
- **test-race** - Runs tests with race detector
- **test-integration** - Runs integration tests only
- **test-detectors** - Runs detector tests only

#### Quality Tasks

- **lint** - Runs golangci-lint
- **check** - Runs go fmt and go vet

#### Run Tasks

- **dogfood** - Runs TruffleHog on itself (useful for testing)
- **run** - Runs TruffleHog with default arguments
- **install** - Installs TruffleHog to your GOPATH

#### Utility Tasks

- **clean** - Removes the built binary

### Running Tasks

1. Press `Ctrl+Shift+P` (or Cmd+Shift+P on Mac)
2. Type "Tasks: Run Task"
3. Select the task you want to run

Or use the keyboard shortcut:
- `Ctrl+Shift+B` - Runs the default build task

## PR Workflow

### PR Approval Check Workflow

The repository includes an automated PR approval check workflow that ensures:

1. **Required Approval**: Every PR must have at least one approval
2. **Team Membership**: The approver must be an **active** member of:
   - `@trufflesecurity/product-eng` team, OR
   - Any child team of `@trufflesecurity/product-eng`

### How It Works

The workflow (`pr-approval-check.yml`) automatically runs on:
- When a PR review is submitted
- When a PR is opened, reopened, or synchronized

It will:
1. Fetch all reviews for the PR
2. Check if any approver is an active team member
3. Set a commit status:
   - ✅ **Success**: Approved by a product engineering team member
   - ❌ **Failure**: No approval or approval not from required team

### For PR Authors

- Ensure you request review from a member of `@trufflesecurity/product-eng`
- The PR cannot be merged until it has the required approval
- The status check will automatically update when reviews are submitted

### For Reviewers

- If you're a member of the product engineering team, your approval will satisfy the requirement
- The workflow will automatically detect your team membership
- You'll see the status check update after you submit your review

### Branch Protection

To make this check required for merging:

1. Go to **Settings** → **Branches**
2. Add or edit a branch protection rule
3. Enable "Require status checks to pass before merging"
4. Add `product-eng-approval` to required status checks

## Makefile Commands

You can also run commands directly from the terminal:

```bash
# Build
make build

# Test
make test              # All tests
make test-race         # With race detector
make test-integration  # Integration tests
make test-detectors    # Detector tests

# Quality
make lint              # Run linter
make check             # Format and vet

# Run
make run               # Run on current directory
make dogfood           # Run on TruffleHog itself
make install           # Install to GOPATH
```

## Troubleshooting

### "Go extension not found"
Install the Go extension from the VSCode marketplace.

### "golangci-lint not found"
Install golangci-lint:
```bash
# macOS
brew install golangci-lint

# Linux
curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin
```

### Debugger not stopping at breakpoints
1. Make sure you're building with debug symbols (CGO_ENABLED=0 is fine)
2. Check that your Go version is compatible with the debugger
3. Try rebuilding: `make clean && make build`

### Tests timing out
Increase the timeout in `.vscode/settings.json`:
```json
"go.testTimeout": "10m"
```

## Additional Resources

- [TruffleHog Documentation](https://github.com/trufflesecurity/trufflehog)
- [Go in VSCode](https://code.visualstudio.com/docs/languages/go)
- [Debugging Go in VSCode](https://github.com/golang/vscode-go/blob/master/docs/debugging.md)

---

**Happy Coding! 🐽**
