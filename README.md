> 🇯🇵 [日本語版はこちら](README.ja.md)

# Soboro

**Swift Oriented Base for Operating Runtime Objects**

Soboro is an experimental OS project that aims to implement the entire userland on the Linux kernel using Swift. By leveraging existing C libraries (musl, libdrm, libinput, etc.), it presents a new form of system programming with type-safe and modern Swift.

## Project Vision

```
┌─────────────────────────────────────────┐
│         Swift Applications              │
├─────────────────────────────────────────┤
│         Swift Desktop Environment       │
│   (Wayland compositor / WM / UI Kit)    │
├─────────────────────────────────────────┤
│         Swift System Services           │
│   (init / device mgmt / networking)     │
├─────────────────────────────────────────┤
│         Swift Thin Wrapper Layer        │
│   (libdrm / libinput / D-Bus etc.)      │
├─────────────────────────────────────────┤
│              musl libc                  │
├─────────────────────────────────────────┤
│            Linux Kernel                 │
└─────────────────────────────────────────┘
```

---

## Repository Structure

```
soboro/
├── README.md                     # Project overview (English)
├── README.ja.md                  # Project overview (Japanese)
├── CLAUDE.md                     # Claude Code development guidelines
├── CONTRIBUTING.md               # Contribution guide (English)
├── CONTRIBUTING.ja.md            # Contribution guide (Japanese)
├── LICENSE
├── .gitignore
├── Package.swift                 # Root manifest (workspace)
│
├── docs/                         # Documentation
│   ├── architecture/             # Architecture design documents
│   │   ├── ja/                   # Japanese version
│   │   └── en/                   # English version
│   ├── api/                      # API reference
│   ├── guides/                   # Development guides
│   │   ├── ja/
│   │   └── en/
│   └── decisions/                # ADR (Architecture Decision Records)
│       ├── ja/
│       └── en/
│
├── core/                         # Core system
│   ├── soboro-init/              # init system (PID 1)
│   │   ├── Package.swift
│   │   └── Sources/
│   ├── soboro-service/           # Service management daemon
│   ├── soboro-log/               # Logging system
│   └── soboro-config/            # System configuration management
│
├── bindings/                     # C library bindings
│   ├── CLinux/                   # Linux syscall / ioctl
│   ├── CDrm/                     # libdrm
│   ├── CInput/                   # libinput
│   ├── CWayland/                 # wayland-server
│   ├── CDBus/                    # D-Bus
│   └── CUdev/                    # libudev
│
├── libs/                         # Shared libraries
│   ├── SoboroFoundation/         # Foundation utilities
│   ├── SoboroIPC/                # Inter-process communication
│   ├── SoboroNetwork/            # Network management
│   ├── SoboroDevice/             # Device abstraction
│   └── SoboroGraphics/           # Graphics abstraction
│
├── services/                     # System services
│   ├── soboro-deviced/           # Device management (udev equivalent)
│   ├── soboro-networkd/          # Network management
│   ├── soboro-sessiond/          # Session management
│   └── soboro-powerd/            # Power management
│
├── desktop/                      # Desktop environment
│   ├── soboro-compositor/        # Wayland compositor
│   ├── soboro-shell/             # Desktop shell
│   ├── soboro-panel/             # Panel / taskbar
│   └── SoboroUI/                 # UI toolkit
│
├── apps/                         # Core applications
│   ├── soboro-terminal/          # Terminal emulator
│   ├── soboro-files/             # File manager
│   ├── soboro-settings/          # Settings app
│   └── soboro-editor/            # Text editor
│
├── tools/                        # Development and operations tools
│   ├── soboro-pkg/               # Package manager
│   ├── soboro-build/             # Build system extensions
│   └── soboro-debug/             # Debug tools
│
├── tests/                        # Integration tests
│   ├── integration/
│   └── e2e/
│
└── dist/                         # Distribution
    ├── rootfs/                   # Root filesystem configuration
    ├── kernel/                   # Kernel configuration
    └── images/                   # Pre-built images
```

---

## Naming Conventions

### Packages and Modules

| Type | Format | Example |
|------|--------|---------|
| Executables | `soboro-{name}` (kebab-case) | `soboro-init`, `soboro-compositor` |
| Libraries | `Soboro{Name}` (PascalCase) | `SoboroFoundation`, `SoboroUI` |
| C bindings | `C{Name}` (PascalCase) | `CDrm`, `CWayland` |
| Daemons | `soboro-{name}d` | `soboro-deviced`, `soboro-networkd` |

### Source Code

| Element | Format | Example |
|---------|--------|---------|
| Types (struct/class/enum) | PascalCase | `ServiceManager`, `DeviceEvent` |
| Protocols | PascalCase + adjective/noun | `Configurable`, `DeviceProvider` |
| Functions and methods | camelCase | `startService()`, `handleEvent()` |
| Variables and properties | camelCase | `isRunning`, `deviceList` |
| Constants | camelCase | `defaultTimeout`, `maxRetries` |
| File names | PascalCase.swift | `ServiceManager.swift` |

### Git Branches

| Branch | Purpose |
|--------|---------|
| `main` | Stable, always buildable |
| `develop` | Development integration branch |
| `feature/{component}/{description}` | Feature development |
| `fix/{component}/{description}` | Bug fixes |
| `docs/{description}` | Documentation updates |

Examples: `feature/init/service-dependency-resolution`, `fix/compositor/cursor-hotspot`

### Commit Messages

```
{type}({scope}): {description}

{body}

{footer}
```

type: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`
scope: Component name (`init`, `compositor`, `foundation`, etc.)

Example:
```
feat(init): add service dependency resolution

Implement topological sort for service startup order.
Services can now declare dependencies via `requires` and `after` fields.

Closes #42
```

---

## Development Schedule

### Phase 0: Foundation

**Goal**: Establish a state where a minimal Swift system boots

| Milestone | Deliverables |
|-----------|--------------|
| Development environment | Build system, CI/CD, C bindings foundation |
| Minimal init implementation | Boot as PID 1, launch shell |
| Basic service management | Service start/stop, dependency resolution |

**Phase 0 completion criteria**:
- Linux kernel + Soboro init boots in QEMU
- Shell prompt is displayed
- Basic services (logging, etc.) are operational

### Phase 1: System Services

**Goal**: Complete a practical CLI environment

| Milestone | Deliverables |
|-----------|--------------|
| Device management | soboro-deviced (udev equivalent) |
| Networking | soboro-networkd (DHCP, DNS, basic configuration) |
| Storage and power | Mount management, power state management |

**Phase 1 completion criteria**:
- Network connectivity available
- USB device hotplug support
- Proper shutdown handling

### Phase 2: Graphics Foundation

**Goal**: Basic GUI environment operational

| Milestone | Deliverables |
|-----------|--------------|
| DRM/KMS integration | Display output, mode setting |
| Wayland compositor | Basic window management |
| Input handling | Keyboard, mouse, touch |

**Phase 2 completion criteria**:
- Wayland clients can render
- Window move and resize
- Keyboard and mouse input functional

### Phase 3: Desktop Environment

**Goal**: Desktop usable for daily use

| Milestone | Deliverables |
|-----------|--------------|
| UI toolkit | SoboroUI basic components |
| Shell and panel | Desktop shell, notifications |
| Core apps | Terminal, file manager, settings |

**Phase 3 completion criteria**:
- Login to desktop environment possible
- Core apps functional
- System settings changeable via GUI

### Phase 4: Ecosystem (Ongoing)

**Goal**: Sustainable project foundation

| Item | Content |
|------|---------|
| Package management | soboro-pkg (SwiftPM integration) |
| Developer tools | IDE integration, debugger, profiler |
| Documentation | API reference, tutorials |
| Community | Contribution guide, governance |

---

## Development Environment Setup

### Requirements

- **Swift 6.2 or later** (Leveraging Swift 6 Concurrency improvements, Embedded Swift support)
- **OS**: Linux (Ubuntu 24.04+ recommended), WSL2, or macOS (cross-compilation)
- **QEMU**: Virtual environment for testing
- **Dependencies**: `musl-dev`, `libdrm-dev`, `libinput-dev`, `wayland-protocols`, etc.

### Linux (Ubuntu / Debian) Setup

```bash
# Clone repository
git clone https://github.com/example/soboro.git
cd soboro

# Install Swift 6.2 (swiftly recommended)
curl -L https://swiftlang.github.io/swiftly/swiftly-install.sh | bash
swiftly install latest

# Install dependencies
sudo apt update
sudo apt install -y \
    musl-dev musl-tools \
    libdrm-dev libinput-dev \
    wayland-protocols libwayland-dev \
    libdbus-1-dev libudev-dev \
    qemu-system-x86

# Build
swift build

# Test
swift test

# Create development image
./tools/soboro-build/create-image.sh
```

### WSL2 (Windows Subsystem for Linux) Setup

Development on WSL2 is fully supported.

#### 1. Prepare WSL2

```powershell
# Run in PowerShell (Administrator)
wsl --install -d Ubuntu-24.04
```

#### 2. Setup within WSL2

```bash
# Run within WSL2 Ubuntu
sudo apt update && sudo apt upgrade -y

# Install Swift 6.2
curl -L https://swiftlang.github.io/swiftly/swiftly-install.sh | bash
swiftly install latest

# Install dependencies (same as Linux)
sudo apt install -y \
    musl-dev musl-tools \
    libdrm-dev libinput-dev \
    wayland-protocols libwayland-dev \
    libdbus-1-dev libudev-dev

# Clone repository
git clone https://github.com/example/soboro.git
cd soboro

# Build
swift build
```

#### 3. Running QEMU in WSL2

`-nographic` mode is recommended for WSL2.

```bash
qemu-system-x86_64 \
    -kernel dist/kernel/vmlinuz \
    -initrd dist/images/initramfs.img \
    -append "init=/usr/bin/soboro-init console=ttyS0" \
    -nographic \
    -m 1G
```

For GUI testing, use WSLg (Windows 11) or an X Server (VcXsrv, etc.).

### macOS Cross-Compilation

```bash
# Install Swift 6.2 (included in Xcode 16.2+)
xcode-select --install

# Install Linux SDK (Swift SDK)
swift sdk install https://download.swift.org/swift-6.2-release/static-sdk/swift-6.2-RELEASE/swift-6.2-RELEASE_static-linux-0.0.1.artifactbundle.tar.gz

# Cross-compile
swift build --swift-sdk x86_64-swift-linux-musl
```

### Running in QEMU

```bash
# Boot with development image
qemu-system-x86_64 \
    -kernel dist/kernel/vmlinuz \
    -initrd dist/images/initramfs.img \
    -append "init=/usr/bin/soboro-init console=ttyS0" \
    -nographic \
    -m 1G
```

---

## Technology Stack

### Language and Runtime

| Item | Choice | Reason |
|------|--------|--------|
| Language | Swift 6.2+ | Complete Concurrency safety, Embedded Swift, improved actor isolation |
| libc | musl | Statically linkable, lightweight |
| Runtime | Swift Runtime (Linux) | Official support |

### Swift 6 Features Utilized

- **Complete Concurrency Checking**: Compile-time detection of data races
- **Actor Isolation**: Safe state management between services
- **Typed Throws**: Type-safe handling of system errors
- **Embedded Swift** (experimental): Future embedded target support
- **Swift Testing**: New testing framework
- **Subprocess** (Swift 6.2): Simplified process management

### Key Dependencies

| Library | Purpose | Binding |
|---------|---------|---------|
| libdrm | GPU / Display control | `CDrm` |
| libinput | Input devices | `CInput` |
| wayland-server | Wayland protocol | `CWayland` |
| D-Bus | Inter-process communication | `CDBus` |
| libudev | Device detection | `CUdev` |

---

## Documentation

Documentation is maintained in both Japanese and English.

| Category | Japanese | English |
|----------|----------|---------|
| Architecture | `docs/architecture/ja/` | `docs/architecture/en/` |
| Development guides | `docs/guides/ja/` | `docs/guides/en/` |
| ADR | `docs/decisions/ja/` | `docs/decisions/en/` |

---

## License

MIT License

---

## Contributing

See CONTRIBUTING.md.

For development with Claude Code, see CLAUDE.md.

---

## References

- [Swift System](https://github.com/apple/swift-system) - Low-level system APIs
- [SwiftNIO](https://github.com/apple/swift-nio) - Asynchronous I/O
- [Swift Subprocess](https://github.com/apple/swift-subprocess) - Process management
- [wlroots](https://gitlab.freedesktop.org/wlroots/wlroots) - Wayland compositor library
- [systemd](https://systemd.io/) - init system reference
- [Wayland Protocol](https://wayland.freedesktop.org/docs/html/) - Wayland specification
- [Swift 6.2 Release Notes](https://www.swift.org/blog/swift-6.2-released/) - Swift 6.2 new features
