# Additional Notes for ROS 2 Jazzy
## Tested Environment

* Ubuntu 22.04

* ROS 2 Jazzy

* GCC 13

* Python 3.12

* Ceres Solver 2.0.0 (built from source)

## Build System Adjustments for Modern ROS 2
### C++ Standard Update

The original project enforced C++14.
ROS 2 Jazzy and modern `rclcpp` headers require C++17 features (e.g., `std::optional`, type traits utilities).

The build configuration was updated to use C++17 to ensure compatibility with modern ROS 2 distributions and toolchains.

## Python / NumPy Header Integration

The project uses `matplotlibcpp`, which depends on NumPy C headers.

On modern systems, NumPy headers are not automatically exposed through standard Python CMake discovery. Without explicitly adding the NumPy include path, the build fails due to missing `numpy/arrayobject.h`.

The build configuration was updated to correctly detect and include NumPy headers for the active Python interpreter.

This improves portability across Python 3.10–3.12+.

## Modern Python CMake Usage

Legacy Python discovery mechanisms were removed in favour of modern CMake `FindPython3` usage.

This avoids hardcoded interpreter paths and improves compatibility with:

System Python

Virtual environments

Future Ubuntu releases

## Ceres Solver Version Requirement

⚠️ **Important**: This project requires Ceres Solver 2.0.0.

Ubuntu currently provides Ceres 2.2.x, which introduces API changes that remove `LocalParameterization`.
The solver implementation in this project depends on the pre-2.1 API.

To preserve solver behaviour without modifying algorithm internals, Ceres 2.0.0 must be built from source.

## Ceres 2.0 on Modern Ubuntu

When building Ceres 2.0 on recent Ubuntu versions, SuiteSparse detection may fail due to TBB layout changes.

To avoid configuration errors, Ceres should be built with SuiteSparse and TBB disabled.

# Summary of Changes

The fork primarily:

Modernises the CMake configuration

Ensures ROS 2 Jazzy compatibility

Fixes Python/NumPy header integration

Documents the required Ceres version

Algorithmic behaviour and solver logic remain unchanged.