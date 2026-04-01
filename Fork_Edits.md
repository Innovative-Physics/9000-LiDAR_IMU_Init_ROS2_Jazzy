# Fork Edits
This fork does three things:

1. Makes the package compatible with ROS2 Jazzy
2. Makes the package compatible with Ubuntu 24.04 Noble
3. Changes the initialisation process to publish a `bool` topic once initialisation has completed and odometry is starting

## 1. Build System Adjustments for ROS2 Jazzy
### C++ Standard Update

The original project enforced C++14.
ROS 2 Jazzy and modern `rclcpp` headers require C++17 features (e.g., `std::optional`, type traits utilities).

The build configuration was updated to use C++17 to ensure compatibility with modern ROS 2 distributions and toolchains.

### Python / NumPy Header Integration

The project uses `matplotlibcpp`, which depends on NumPy C headers.

On modern systems, NumPy headers are not automatically exposed through standard Python CMake discovery. Without explicitly adding the NumPy include path, the build fails due to missing `numpy/arrayobject.h`.

The build configuration was updated to correctly detect and include NumPy headers for the active Python interpreter.

This improves portability across Python 3.10–3.12+.

### Modern Python CMake Usage

Legacy Python discovery mechanisms were removed in favour of modern CMake `FindPython3` usage.

This avoids hardcoded interpreter paths and improves compatibility with system Python, virtual environments, and future Ubuntu releases.

## 2. Ubuntu 24.04 Compatibility Changes
The original repo used a library called Ceres Solver, and was written to use its 2.0.0 API. The Ceres solver you install in Ubuntu Noble has API changes which break the repo. In just a few locations the following replacements were made:

``` c++
// before
ceres::LocalParameterization *quatParam =
    new ceres::QuaternionParameterization();

// after
ceres::Manifold *quatParam =
    new ceres::QuaternionManifold();
```

The package now works with the newest Ceres Solver.

## 3. Initialisation process changes
This package performs some LiDAR-IMU initialisation, and then runs its own LiDAR odometry package. I wanted to benefit from the initialisation, but have the freedom to perform odometry with whatever other package I wanted. For this reason, I added a Boolean topic that indicates when initialisation is complete. This allows a ROS system to respond to the completion of initialisation, shutting down this package and allowing another to take over for the odometry. 