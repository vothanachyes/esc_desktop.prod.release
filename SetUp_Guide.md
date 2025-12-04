# Ubuntu Development & Build Setup Guide

Complete guide for setting up a **brand new Ubuntu system** (tested on Ubuntu 22.04/24.04 LTS) for developing and building the E-School Cambodia Desktop Application.

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Step 1: Update System](#step-1-update-system)
3. [Step 2: Install Git](#step-2-install-git)
4. [Step 3: Install Node.js (v22.18.0+)](#step-3-install-nodejs-v22180)
5. [Step 4: Install pnpm (v10.23.0+)](#step-4-install-pnpm-v10230)
6. [Step 5: Install Rust (for Native Module)](#step-5-install-rust-for-native-module)
7. [Step 6: Install Linux Build Dependencies](#step-6-install-linux-build-dependencies)
8. [Step 7: Clone & Setup Project](#step-7-clone--setup-project)
9. [Step 8: Configure Environment Variables](#step-8-configure-environment-variables)
10. [Step 9: Build the Application](#step-9-build-the-application)
11. [Troubleshooting](#troubleshooting)

---

## System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| Ubuntu | 22.04 LTS | 24.04 LTS |
| RAM | 8 GB | 16 GB |
| Storage | 10 GB free | 20 GB free |
| CPU | 4 cores | 8 cores |

---

## Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Step 2: Install Git

```bash
# Install Git
sudo apt install -y git

# Verify installation
git --version

# Configure Git (replace with your info)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

## Step 3: Install Node.js (v22.18.0+)

This project requires **Node.js 22.18.0 or higher** (but less than v23).

### Option A: Using NVM (Recommended)

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Reload shell configuration
source ~/.bashrc

# Install Node.js 22 (LTS)
nvm install 22

# Verify installation
node --version  # Should be v22.18.0 or higher
npm --version
```

### Option B: Using NodeSource Repository

```bash
# Install prerequisites
sudo apt install -y ca-certificates curl gnupg

# Add NodeSource repository for Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# Install Node.js
sudo apt install -y nodejs

# Verify installation
node --version  # Should be v22.18.0 or higher
npm --version
```

---

## Step 4: Install pnpm (v10.23.0+)

This project requires **pnpm 10.23.0 or higher**.

```bash
# Install pnpm globally using corepack (comes with Node.js)
corepack enable
corepack prepare pnpm@10.23.0 --activate

# OR install using npm
npm install -g pnpm@10.23.0

# Verify installation
pnpm --version  # Should be 10.23.0 or higher
```

---

## Step 5: Install Rust (for Native Module)

The project includes a native Rust module (`esc-crypto`) for cryptographic operations.

```bash
# Install Rust using rustup
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Follow prompts, select option 1 (default installation)

# Reload shell configuration
source ~/.cargo/env

# Verify installation
rustc --version
cargo --version

# Add Linux target (required for native builds)
rustup target add x86_64-unknown-linux-gnu
```

### Install napi-rs CLI

```bash
# Install napi-rs CLI globally (required for building native modules)
pnpm add -g @napi-rs/cli
```

---

## Step 6: Install Linux Build Dependencies

Install dependencies required by Electron and electron-builder for Linux builds.

```bash
# Essential build tools
sudo apt install -y build-essential

# Electron dependencies
sudo apt install -y \
  libgtk-3-dev \
  libwebkit2gtk-4.0-dev \
  libnotify-dev \
  libgconf-2-4 \
  libnss3 \
  libxss1 \
  libasound2 \
  libxtst6 \
  xauth \
  xvfb

# AppImage/FUSE support (for running AppImage builds)
sudo apt install -y fuse libfuse2

# Additional dependencies for Electron builds
sudo apt install -y \
  dpkg-dev \
  rpm \
  libarchive-tools \
  snapd

# (Optional) Install for running GUI in headless environment
sudo apt install -y xvfb
```

### For ARM64 Cross-Compilation (Optional)

If building for ARM64 architecture:

```bash
sudo apt install -y \
  gcc-aarch64-linux-gnu \
  g++-aarch64-linux-gnu

rustup target add aarch64-unknown-linux-gnu
```

---

## Step 7: Clone & Setup Project

```bash
# Clone the repository
git clone https://github.com/YOUR_ORG/esc_desktop_app.git
cd esc_desktop_app

# Install dependencies
pnpm install

# Verify setup
pnpm --version
node --version
```

---

## Step 8: Configure Environment Variables

Create a `.env` file in the project root with the required variables:

```bash
# Copy from example (if available)
cp .env.example .env

# OR create manually
touch .env
```

### Required Environment Variables

Edit the `.env` file and add these **required** variables:

```env
# API Configuration
VITE_APP_BASE_URL=https://api.example.com
VITE_APP_BASE_URL_MICRO=https://micro.api.example.com
VITE_SOCKET_URL=https://socket.example.com

# Authentication
VITE_BASE_AUTH_USERNAME=your_auth_username
VITE_BASIC_AUTH=your_basic_auth_token
VITE_BASE_APP_KEY=your_app_key

# App Configuration
VITE_WINDOW_SIZE=1280x800
VITE_APP_SIMPLE_SPLASH=true
VITE_APP_ENV=production
VITE_AUTO_FETCHING=true
VITE_ENABLE_NETWORK_STATUS=true

# App Name (optional, defaults to "E-School Cambodia")
VITE_APP_NAME=E-School Cambodia
```

> ⚠️ **Important**: Contact your team lead to get the actual values for these environment variables.

### Optional: GitHub Token for Auto-Updates

If publishing releases to GitHub:

```bash
# Add to .env or export in terminal
export GH_TOKEN=your_github_personal_access_token
```

---

## Step 9: Build the Application

### Development Mode

```bash
# Start development server with hot-reload
pnpm dev
# OR
pnpm app
```

### Production Build

```bash
# Build for production (Linux AppImage)
pnpm build
```

The build output will be in:
- `esc-release/{version}/E-School Cambodia-Linux-{version}-x64.AppImage`

### Pre-production Build

```bash
# Build for pre-production environment
pnpm preprod
```

### Build with GitHub Release Publishing

```bash
# Build and publish to GitHub releases
pnpm build --publish always
```

### Build Only Renderer (Frontend)

```bash
pnpm build-only
```

---

## Build Output

After a successful build, you'll find:

```
esc-release/
└── {version}/
    ├── E-School Cambodia-Linux-{version}-x64.AppImage  # Main executable
    ├── latest-linux.yml                                 # Auto-update manifest
    └── builder-effective-config.yaml                    # Build configuration
```

### Running the AppImage

```bash
# Make executable
chmod +x "E-School Cambodia-Linux-*.AppImage"

# Run
./E-School\ Cambodia-Linux-*.AppImage
```

---

## Troubleshooting

### Common Issues

#### 1. Node.js Version Mismatch

```
Error: The engine "node" is incompatible with this module
```

**Solution**: Ensure Node.js version is between 22.18.0 and 23.0.0:

```bash
node --version  # Check current version
nvm install 22  # Install correct version
nvm use 22      # Switch to it
```

#### 2. pnpm Version Mismatch

```
Error: This project requires pnpm version 10.23.0
```

**Solution**:

```bash
corepack prepare pnpm@10.23.0 --activate
# OR
npm install -g pnpm@10.23.0
```

#### 3. Native Module Build Failure

```
Error: Cannot find module 'esc-crypto'
```

**Solution**: Ensure Rust is installed and build the native module:

```bash
# Check Rust installation
rustc --version

# Rebuild native module
cd electron/native/esc-crypto
pnpm run build
cd ../../..
```

#### 4. Missing Linux Dependencies

```
Error: Cannot find module 'gtk-3.0'
```

**Solution**: Install missing dependencies:

```bash
sudo apt install -y libgtk-3-dev libwebkit2gtk-4.0-dev
```

#### 5. AppImage Won't Run

```
AppImages require FUSE to run
```

**Solution**:

```bash
sudo apt install -y fuse libfuse2

# If still not working, extract and run directly
./E-School\ Cambodia-Linux-*.AppImage --appimage-extract
./squashfs-root/AppRun
```

#### 6. Permission Denied

```
Permission denied when running AppImage
```

**Solution**:

```bash
chmod +x "E-School Cambodia-Linux-*.AppImage"
```

#### 7. Display Issues in Headless Environment

```
Cannot open display
```

**Solution**: Use Xvfb for headless builds:

```bash
xvfb-run pnpm build
```

---

## Quick Reference Commands

| Task | Command |
|------|---------|
| Install dependencies | `pnpm install` |
| Run dev server | `pnpm dev` |
| Build for production | `pnpm build` |
| Build for preprod | `pnpm preprod` |
| Build with publish | `pnpm build --publish always` |
| Run tests | `pnpm test` |
| Run E2E tests | `pnpm test:e2e` |
| Lint code | `pnpm lint:eslint` |
| Build native module | `cd electron/native/esc-crypto && pnpm run build` |

---

## System Check Script

Create and run this script to verify your setup:

```bash
#!/bin/bash
# save as: check-setup.sh

echo "=== E-School Desktop - System Check ==="
echo ""

# Check Node.js
echo -n "Node.js: "
if command -v node &> /dev/null; then
    NODE_VERSION=$(node --version)
    echo "$NODE_VERSION ✓"
else
    echo "NOT INSTALLED ✗"
fi

# Check pnpm
echo -n "pnpm: "
if command -v pnpm &> /dev/null; then
    PNPM_VERSION=$(pnpm --version)
    echo "$PNPM_VERSION ✓"
else
    echo "NOT INSTALLED ✗"
fi

# Check Rust
echo -n "Rust: "
if command -v rustc &> /dev/null; then
    RUST_VERSION=$(rustc --version | awk '{print $2}')
    echo "$RUST_VERSION ✓"
else
    echo "NOT INSTALLED ✗"
fi

# Check Cargo
echo -n "Cargo: "
if command -v cargo &> /dev/null; then
    CARGO_VERSION=$(cargo --version | awk '{print $2}')
    echo "$CARGO_VERSION ✓"
else
    echo "NOT INSTALLED ✗"
fi

# Check Git
echo -n "Git: "
if command -v git &> /dev/null; then
    GIT_VERSION=$(git --version | awk '{print $3}')
    echo "$GIT_VERSION ✓"
else
    echo "NOT INSTALLED ✗"
fi

# Check napi-rs CLI
echo -n "napi-rs CLI: "
if command -v napi &> /dev/null; then
    NAPI_VERSION=$(napi --version 2>/dev/null || echo "unknown")
    echo "$NAPI_VERSION ✓"
else
    echo "NOT INSTALLED (install with: pnpm add -g @napi-rs/cli)"
fi

echo ""
echo "=== Check Complete ==="
```

Run the script:

```bash
chmod +x check-setup.sh
./check-setup.sh
```

---

## One-Line Setup (Advanced)

For quick setup on a fresh Ubuntu system:

```bash
# Run this single command to install all prerequisites
sudo apt update && sudo apt upgrade -y && \
sudo apt install -y git build-essential libgtk-3-dev libwebkit2gtk-4.0-dev \
  libnotify-dev libnss3 libxss1 libasound2 libxtst6 fuse libfuse2 \
  ca-certificates curl gnupg && \
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash && \
source ~/.bashrc && \
nvm install 22 && \
corepack enable && \
corepack prepare pnpm@10.23.0 --activate && \
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y && \
source ~/.cargo/env && \
rustup target add x86_64-unknown-linux-gnu && \
pnpm add -g @napi-rs/cli
```

After running the above, clone the project and start developing!

---

## Additional Resources

- [Electron Documentation](https://www.electronjs.org/docs)
- [electron-builder Documentation](https://www.electron.build/)
- [Vue 3 Documentation](https://vuejs.org/)
- [Rust Documentation](https://doc.rust-lang.org/)
- [napi-rs Documentation](https://napi.rs/)

---

*Last updated: December 2025*

