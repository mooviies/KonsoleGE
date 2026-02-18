# KonsoleGE

KonsoleGE (Konsole Game Engine) is a lightweight, cross-platform C++17 library for building console-based games. It provides platform abstraction, input handling, a panel-based UI rendering system, a hierarchical menu system, audio playback, and utility classes.

## Features

- **Platform abstraction** -- Console initialization, cleanup, output flushing, terminal resize detection, and data directory resolution. OS-specific code lives in separate `*Linux.cpp` / `*Win32.cpp` files with no `#ifdef` in shared code.
- **Input system** -- Action-based key binding model. Declare actions, bind one or more keys to each, and poll per-frame. Supports ASCII keys, arrow keys, F-keys, numpad, and modifier keys.
- **Panel rendering** -- Box-drawn frame primitives with row-level dirty tracking. Supports text rows (with alignment and color), separators with smart junction characters, and custom `PanelElement` subclasses.
- **Menu system** -- Hierarchical, tree-based navigation with keyboard input. Supports submenus, selectable values (cycled with LEFT/RIGHT), callbacks, hint text, and escape-to-close.
- **Sound engine** -- miniaudio wrapper with an embedded virtual filesystem. Supports streaming music and decoded sound effects, independent volume control, and configurable soundtrack modes.
- **Game engine base class** -- Generic game loop with virtual callbacks (`onInit`, `onFrame`, `onCleanup`, `onResize`, `onTerminalTooSmall`, `onTerminalRestored`), FPS capping, and terminal resize handling.
- **Utilities** -- Named timers (`steady_clock`-based), Mersenne Twister random number generation, 2D integer vectors, and string helpers.

## Source Layout

```
source/
  Core/        GameEngine (generic game loop base class)
  Platform/    Platform (console I/O), Input (key binding system)
  UI/          Panel, Menu, Icon, Color, RowDrawContext
  Util/        Timer, Random, SoundEngine, Utility, Vector2i
include/
  miniaudio.h  Cross-platform audio (header-only, vendored)
  rlutil.h     Console colors & cursor positioning (header-only, vendored)
scripts/
  embed_media.py  Build-time media embedding (generates C++ byte arrays)
```

## Usage

KonsoleGE is designed to be included as a subdirectory (e.g. via git submodule) in a parent CMake project:

```cmake
add_subdirectory(KonsoleGE)
target_link_libraries(my_game PRIVATE konsolege)
```

All public include directories and link dependencies propagate automatically.

### Media Embedding

KonsoleGE includes `scripts/embed_media.py` which generates C++ source files containing binary media data at build time. The parent project is responsible for invoking it:

```cmake
add_custom_command(
    OUTPUT ${CMAKE_BINARY_DIR}/media_data.h ${CMAKE_BINARY_DIR}/media_data.cpp
    COMMAND python ${CMAKE_SOURCE_DIR}/KonsoleGE/scripts/embed_media.py
            ${CMAKE_BINARY_DIR} ${CMAKE_SOURCE_DIR}/path/to/media
    DEPENDS ${MEDIA_FILES} ${CMAKE_SOURCE_DIR}/KonsoleGE/scripts/embed_media.py
    COMMENT "Embedding media files"
)
add_custom_target(media_embed DEPENDS ${CMAKE_BINARY_DIR}/media_data.h)
add_dependencies(konsolege media_embed)
```

## Platforms

- Linux (GCC, Clang)
- Windows (MSVC, MinGW)
- macOS (Clang)

## License

MIT -- see [LICENSE](LICENSE).
