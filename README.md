# mc\_rtc new plugin template (Without CI and Tests)

This project is a fork of the original template for a new plugin within [mc_rtc].

This fork **removes GitHub Actions** and **comments out the tests**, meaning the plugin is **not tested during build compilation**. This version serves as a **temporary workaround** due to the original CI and test setup being deprecated.
It also includes a **complete tutorial** for installing an `mc_rtc` plugin with or without using the [mc-rtc-superbuild].

## Features

* A CMake project structure for building a plugin compatible with [mc_rtc]
* Can be integrated into the [mc_rtc] source tree for easier updates
* Comes with clang-format configuration files

## Quick Start

### 1. Renaming the plugin

Rename the default `NewPlugin` to your desired name, e.g., `MyPlugin`.

> **Naming convention tips**
>
> * **Repository name**: Use snake\_case prefixed with `mc_`, e.g., `mc_my_plugin`
> * **Plugin name (used in config)**: Use PascalCase, e.g., `MyPlugin`
>   You'll use this name to reference the plugin in your mc\_rtc config.

Clone the repository and apply renaming (use `gsed` on macOS if needed):

```bash
cd ~/workspace/src
git clone git@github.com:username/mc_my_plugin.git
cd mc_my_plugin
find . -type f -not -path "./.git/*" -exec sed -i -e 's/NewPlugin/MyPlugin/g' {} +
git mv src/NewPlugin.cpp src/MyPlugin.cpp
git mv src/NewPlugin.h src/MyPlugin.h
git mv etc/NewPlugin.in.yaml etc/MyPlugin.in.yaml
```

> Alternatively, you can integrate your plugin using the superbuild, but it's **strongly recommended to rename your plugin before any build** to avoid unnecessary files or complications.

### 2. Update project metadata

Customize the project name in `vcpkg.json` as needed.
Note: Follow the [vcpkg manifest rules](https://github.com/microsoft/vcpkg/blob/master/docs/users/manifests.md)

### 3. Build and Install the Plugin

#### 3.1 With the [mc-rtc-superbuild] (Recommended)

1. Create the plugin extension folder:

```bash
mkdir -p ~/workspace/mc-rtc-superbuild/extensions/plugins
```

2. Inside this folder, create a file `mc_my_plugin.cmake` with the following content:

```cmake
AddProject(mc_my_plugin
  GITHUB username/mc_my_plugin
  GIT_TAG origin/main
  DEPENDS mc_rtc
)
```

> Use `GITHUB_PRIVATE` if needed.
> If you've already cloned the repository (recommended when renaming), the superbuild will detect it without recloning or modifying your files.

3. Add the plugin to your superbuild by editing `local.cmake`:

```bash
nano ~/workspace/mc-rtc-superbuild/extensions/local.cmake
```

Add the line:

```cmake
include(${CMAKE_CURRENT_LIST_DIR}/plugins/mc_my_plugin.cmake)
```

4. Build the superbuild:

```bash
cd ~/workspace/mc-rtc-superbuild/build
cmake ../ \
  -DSOURCE_DESTINATION=$HOME/workspace/src/ \
  -DBUILD_DESTINATION=$HOME/workspace/build \
  -DCMAKE_INSTALL_PREFIX=$HOME/workspace/install \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER_LAUNCHER="ccache;distcc" \
  -DCMAKE_CXX_COMPILER_LAUNCHER="ccache;distcc"

cmake --build . --config RelWithDebInfo
```

#### 3.2 Without Superbuild (Not Recommended)

This method doesn't manage dependencies:

```bash
cd ~/workspace/src/mc_my_plugin
mkdir -p build
cd build
cmake ..
ninja
ninja install
```

### 4. Configure and Run

Edit your `mc_rtc` configuration file to load the plugin:

```bash
nano ~/.config/mc_rtc/mc_rtc.yaml
```

Example:

```yaml
Plugins: [OtherPlugin, MyPlugin]
```

[mc_rtc]: https://jrl-umi3218.github.io/mc_rtc/
[mc-rtc-superbuild]: https://github.com/mc-rtc/mc-rtc-superbuild
