# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased][dev-latest]

### Added

- SR: Add ZDSR API Support (#1)
- SR: Add BoyPCReader Support (#17)
- build,ci: CMake Support, CI Maintenance
  - cmake,ci: Build with CMake and setup Github Action CI (dkager/tolk#19)
  - cmake: Fix cross compiling with mingw on Linux (#2)
  - ci: merge `mingw-w64-x64.yml` into `cmake.yml` (#5)
  - ci: install mingw only on linux (#6)
  - ci: Update ci & cmake (#8)
  - ci: upload latest dev build (#9)
  - cmake: Build TolkDotNet.dll (#10, #12)
  - cmake: build Tolk.jar (#11)
  - ci: build with RelWithDebInfo (#16)

### Fixed

- build/Makefile: Fix build errors and added build steps in docs (dkager/tolk#21)
- src/ScreenReaderDriver.h: fix `operator=` (e92abf3d6342754a8ecfe69e461e7e645b8d01d8)

### Changed

- SR: Update NVDA to release-2022.3.2 (8fa7ce08a74490acf09e805c922036eceeb9adb8)
- doc: Update build steps (#13)

### Removed

- ci: remove `appveyor.yml`

[dev-latest]: https://github.com/sig-a11y/tolk/releases/tag/dev-latest

## [1.0.0+2024-06-09]

Upstream commit: https://github.com/dkager/tolk

Last code change commit: [Switch to JDK 10 (2018-05-20)](https://github.com/dkager/tolk/commit/392c9513c2d9d4a9bdd4ae3c5d995782cd7fc3c8)

[1.0.0+2024-06-09]: https://github.com/sig-a11y/tolk/releases/tag/v1.0.0%2B20240609
