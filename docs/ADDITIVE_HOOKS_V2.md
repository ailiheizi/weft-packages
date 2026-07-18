# Additive hooks v2

## Decision

Weft packages are independently versioned modules. A package update is a verified replacement of one named module. It is not a dependency-resolution event.

Modules extend products through named hooks. A hook is an explicit, versioned contract owned by the base module or core. Extensions may add a handler before, after, or around a hook. They may not replace a module, mutate another module on disk, or implicitly install a dependency.

## Runtime contract

Core exposes a small HookHost:
- list hook contracts and hook API versions
- load a base module
- attach ordered handlers to declared hooks
- invoke hooks with typed input and output
- reject unknown hooks and incompatible hook API versions

A module declares its identity, version, runtime, capabilities, and optional exported hooks. An overlay declares its target module, target hook API range, handlers, and explicit order. Capability names remain runtime routing identifiers only; they are not install dependencies.

## Profile and update

A profile is the complete desired module set. It contains package id, immutable version, artifact URL, sha256, and core compatibility. Overlays are listed explicitly and ordered explicitly.

Update flow:
1. Fetch one signed or checksummed catalog.
2. Compare the selected profile entries with available releases.
3. Validate core and hook API compatibility.
4. Download and verify each selected artifact.
5. Stage the full profile, validate manifests, then atomically activate it.
6. Reload or restart the affected runtime.

No transitive dependency solver, multi-registry merge, source precedence, package discovery priority, or automatic dependency installation is part of v2.

## Compatibility

Stable hook ids use a versioned namespace, such as core.chat.before_request.v1 or weft_claw.turn.after_tools.v1. Breaking a hook contract creates a new hook id. Updating a base package cannot silently reinterpret an existing overlay.

## Migration

Existing package.toml files remain source-compatible during migration through an adapter. Existing requires and provides metadata is retained for diagnostics, but requires no longer causes installation or version resolution. The legacy manager remains preserved in the legacy branches of weft and weft-core.
