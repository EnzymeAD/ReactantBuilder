# ReactantBuilder

## Overview

ReactantBuilder is a [`BinaryBuilder.jl`](https://github.com/JuliaPackaging/BinaryBuilder.jl)-based build system that creates cross-platform binary artifacts for the [`Reactant_jll.jl`](https://github.com/EnzymeAD/Reactant_jll.jl) library, which powers [`Reactant.jl`](https://github.com/EnzymeAD/Reactant.jl).

This repository automates the process of:

- Building ReactantExtra (the core C++ library) using Bazel
- Cross-compiling for multiple platforms (Linux, macOS, Windows)
- Supporting multiple GPU backends (CUDA, ROCm, CPU-only)
- Creating optimized and debug builds
- Packaging binaries for distribution via Julia's package ecosystem

## Building the library locally

### Prerequisites

Before building locally, ensure you have:

* [**Julia**](https://julialang.org/downloads/) (version 1.7 or v1.12 and later)
* **Sufficient disk space** (builds can require 50+ GB)

### Quick Start

1. **Clone the repository**:
   ```bash
   git clone https://github.com/EnzymeAD/ReactantBuilder.git
   cd ReactantBuilder
   ```

2. **Install Julia dependencies**:
   ```bash
   julia --project=. -e 'using Pkg; Pkg.instantiate()'
   ```

3. **Build for a specific platform**:
   ```bash
   cd R/Reactant
   julia --project build_tarballs.jl --verbose x86_64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+opt
   ```

### Building Specific Configurations

#### Build for a specific platform and GPU backend

```bash
# CPU-only build for Linux x86_64
julia --project build_tarballs.jl --debug --verbose x86_64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+opt

# CUDA build for Linux x86_64
julia --project build_tarballs.jl --debug --verbose x86_64-linux-gnu-cxx11-gpu_version+12.9-gpu+cuda-mode+opt

# ROCm build for Linux x86_64
julia --project build_tarballs.jl --debug --verbose x86_64-linux-gnu-cxx11-gpu_version+7.1-gpu+rocm-mode+opt

# macOS build (Apple Silicon)
julia --project build_tarballs.jl --debug --verbose aarch64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+opt

# Windows build
julia --project build_tarballs.jl --debug --verbose x86_64-w64-mingw32-cxx11-gpu_version+none-gpu+none-mode+opt
```

#### Build multiple platforms

```bash
# Build for multiple platforms (comma-separated)
julia --project build_tarballs.jl --verbose x86_64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+opt,aarch64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+opt
```

#### Build with debug symbols

```bash
# Debug build (CPU-only, limited platforms)
julia --project build_tarballs.jl --verbose x86_64-linux-gnu-cxx11-gpu_version+none-gpu+none-mode+dbg
```

### Build Output

After a successful build, artifacts will be located in:
```
R/Reactant/products/
```

Each platform build creates a compressed tarball containing:
- The compiled `libReactantExtra` library
- GPU runtime libraries (if applicable)
- CUDA/ROCm tools and bitcode files (if applicable)
- License files

### Cleaning Build Artifacts

To clean up build directories and free disk space, use the scripts in the top-level directory

```bash
# Clean build artifacts
./clean_builds.sh

# Clean product artifacts
./clean_products.sh
```

## Contributing

We welcome contributions! Here's how you can help:

### Reporting Issues

If you encounter problems:
1. Check existing [issues](https://github.com/EnzymeAD/ReactantBuilder/issues)
2. Create a new issue with:
   - Description of the problem
   - Platform and configuration details
   - Steps to reproduce
   - Relevant error messages or logs

### Making Changes

1. **Fork the repository** and create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**:
   - Follow existing code style and conventions
   - Update documentation as needed
   - Test your changes on relevant platforms

3. **Test locally**:
   - Build for at least one platform to verify your changes
   - Ensure the build completes successfully
   - Check that products are generated correctly

4. **Submit a pull request**:
   - Provide a clear description of your changes
   - Reference any related issues
   - Include test results if applicable

### CI/CD

The repository uses GitHub Actions for automated builds. When you submit a PR:
- CI will test builds on multiple platforms
- Build artifacts are uploaded as GitHub Actions artifacts
- Successful builds on `main` automatically register with the Julia package registry

## Development Workflow

### Updating Reactant Version

To update the Reactant commit or version:

1. Edit `R/Reactant/build_tarballs.jl`
2. Update `reactant_commit` with the new Git commit hash
3. Update `version` with the new version number
4. Test the build locally
5. Submit a PR

### Debugging Build Issues

If a build fails:

1. **Check the verbose output**: Use `--verbose` flag for detailed logs
2. **Review platform-specific sections**: Check if your platform has special handling
3. **Verify dependencies**: Ensure all source URLs and checksums are correct
4. **Test incrementally**: Try building for a single platform first

### Local Testing Without Full Build

For faster iteration, you can:
- Test build script syntax without running the full build
- Use `should_build_platform()` to limit builds to specific platforms
- Modify the platform filter in `build_tarballs.jl` to test only your target platform

## License

This project is licensed under the MIT License.
See the [LICENSE](LICENSE) file for details.

## Related Projects

- **[Reactant.jl](https://github.com/EnzymeAD/Reactant.jl)**: The Julia package that uses these binaries
- **[Reactant_jll.jl](https://github.com/EnzymeAD/Reactant_jll.jl)**: The Julia package wrapper for the built binaries
- **[BinaryBuilder.jl](https://github.com/JuliaPackaging/BinaryBuilder.jl)**: The build system used by this project
- **[Enzyme-JAX](https://github.com/EnzymeAD/Enzyme-JAX)**: The underlying XLA/Enzyme integration

## Support

For questions or issues:
- Open an issue on GitHub
- Check existing issues for similar problems
- Review the [Reactant.jl documentation](https://github.com/EnzymeAD/Reactant.jl)

## Acknowledgments

This project is part of the EnzymeAD ecosystem and builds upon the excellent work of the Julia packaging community, particularly the BinaryBuilder.jl project.
