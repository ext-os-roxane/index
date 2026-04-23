# ExtOS — The Declarative Multi-Origin Linux Operating System

**ExtOS** is a hybrid, declarative Linux meta-distribution that runs packages from Arch, Debian, Alpine, SUSE/RPM, Void, Nix, Android, and language-specific ecosystems (Python, NPM, Rust, Go) in parallel, without ABI conflicts.

## Philosophy

1. **Declarative Over Imperative** — System state is defined in `.nix` configurations, not shell commands
2. **Origin-Agnostic** — Dependencies resolve by *capability* (SONAME, binary), not package name
3. **Immutable Core** — All packages live in a read-only Content-Addressable Storage (CAS)
4. **Zero-Risk Updates** — CoW snapshots (Btrfs) enable instant atomic rollbacks

## Architecture

```
/
├── boot/           ← Physical ESP (bootloader, kernel, initramfs)
├── repository/     ← Read-Only CAS (objects/ + refs/)
├── config/         ← Control Plane (system.nix declarations)
├── runlevel/       ← Guest Plane (0=boot, 1=host, 2=apps, 3=containers)
└── user/           ← Universal Persistent Data Pool
```

## Core Components

| Component | Language | Role |
|-----------|----------|------|
| **`pkt`** | Rust | Package engine — CAS, fetching, Rosetta translation, P2P swarm |
| **`instanz`** | Rust | Hypervisor — weaver, snapshots, containers, runlevel management |
| **`libextos`** | Rust | Shared library — types, config parsing, CAS, weaver engine |
| **`libexpr-extos`** | C++ (fork) | Nix expression evaluator (patched for ExtOS store paths) |
| **`pacman-extos`** | C (fork) | `libalpm` fork for native Arch package oracle |

## Building

```bash
# Prerequisites: Rust nightly, cargo
cargo build --workspace

# Run the CLIs
cargo run --bin pkt -- --help
cargo run --bin instanz -- --help
```

## Project Structure

```
extos/
├── manifests/          ← Google repo manifest
├── core/
│   ├── pkt/            ← Package engine binary
│   ├── instanz/        ← Hypervisor binary
│   └── libextos/       ← Shared library
├── inherited/
│   ├── libexpr-extos/  ← Nix evaluator fork
│   ├── pacman-extos/   ← libalpm fork
│   └── librpm-extos/   ← RPM library fork
├── design/             ← Architecture documentation
└── LICENSE
```

## License

Core Rust tools: **AGPL-3.0-or-later**
Inherited forks: Follow upstream licenses (GPL/LGPL)

## Status

🚧 **Early Development** — See [ARCHITECTURE.md](design/ARCHITECTURE.md) for the full specification.
