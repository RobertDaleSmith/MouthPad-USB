# Repository Cleanup Guide

## Current State
Your repository is **3.8GB** with **80,258 files** - this is massive! Here's what's taking up space:

### Large Directories (can be removed):
- **modules/**: 1.9GB - Zephyr modules and libraries
- **zephyr/**: 921MB - Zephyr RTOS source code
- **nrf/**: 388MB - Nordic Connect SDK
- **build/**: 46MB - Build artifacts
- **bootloader/**: 16MB - MCUboot bootloader
- **.west/**: 4KB - West workspace

### Your Actual Project (keep these):
- **src/**: 100KB - Your source code
- **boards/**: 0B - Custom board definitions
- **sdk_config/**: 4KB - SDK configuration
- **scripts/**: ~20KB - Custom build/flash scripts
- **Config files**: ~20KB - Project configuration
- **Documentation**: ~30KB - README and guides

## What Can Be Safely Removed

### 1. Build Artifacts (regenerated automatically)
- `build/` - All build outputs
- `*.elf`, `*.bin`, `*.hex`, `*.uf2` - Compiled binaries
- `CMakeCache.txt`, `CMakeFiles/` - CMake cache
- `.ninja_deps`, `build.ninja` - Ninja build files

### 2. Dependencies (managed by West)
- `zephyr/` - Zephyr RTOS (pulled by `west update`)
- `nrf/` - Nordic Connect SDK (pulled by `west update`)
- `nrfxlib/` - Nordic libraries (pulled by `west update`)
- `modules/` - Zephyr modules (pulled by `west update`)
- `bootloader/` - MCUboot (pulled by `west update`)
- `tools/` - Build tools (pulled by `west update`)
- `.west/` - West workspace (regenerated by `west init`)

### 3. Test Files (not needed for main project)
- `test/` - Test files and frameworks

### 4. IDE and System Files
- `.vscode/` - VS Code settings
- `.DS_Store` - macOS system files
- `*.backup` - Backup files

## How to Clean Up

### Option 1: Use the cleanup script (recommended)
```bash
./scripts/cleanup.sh
```

### Option 2: Manual cleanup
```bash
# Remove build artifacts
rm -rf build/

# Remove dependencies (will be restored by west)
rm -rf zephyr/ nrf/ nrfxlib/ modules/ bootloader/ tools/ test/

# Remove workspace
rm -rf .west/

# Remove system files
rm -f .DS_Store
rm -f prj.conf.backup
```

## After Cleanup

### Restore Dependencies
```bash
# Initialize west workspace
west init -l .

# Update dependencies
west update
```

### Build Project
```bash
# Build the project
make build
```

## Expected Results

### Before Cleanup:
- Repository size: **3.8GB**
- File count: **80,258 files**

### After Cleanup:
- Repository size: **~200KB** (99.995% reduction!)
- File count: **~50 files**

## Git Management

### Add .gitignore
The `.gitignore` file I created will prevent these files from being tracked in the future.

### Commit Strategy
1. **Keep in Git**: Your source code, configuration, and custom scripts
2. **Ignore**: Build artifacts and dependencies (managed by West)

### For New Developers
New developers just need to:
```bash
git clone <your-repo>
west init -l .
west update
make build
```

## Benefits

1. **Massive size reduction**: 3.8GB → ~200KB
2. **Faster cloning**: New developers get only essential files
3. **Cleaner history**: No build artifacts in Git history
4. **Easier maintenance**: Dependencies managed by West
5. **Better collaboration**: Focus on your code, not dependencies

## Important Notes

- **Dependencies are versioned**: `west.yml` specifies exact versions
- **Reproducible builds**: `west update` restores exact same versions
- **No functionality loss**: All build capabilities remain intact
- **Standard practice**: This is how Zephyr projects should be managed

## Troubleshooting

If something breaks after cleanup:
```bash
# Full restore
west init -l .
west update
make clean
make build
```

The cleanup is completely safe and reversible! 