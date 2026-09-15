# Mocktail Configuration

Mocktail is a Roblox runtime configuration for Linux systems. This repository contains a YAML configuration file and a collection of Roblox fast flags for controlling the device profile, graphics pipeline, performance, integrations, and update behavior.

## Files

- `config.yaml` - Main Mocktail configuration.
- `fflags.json` - Roblox fast-flag overrides for rendering, visual quality, reduced effects, and telemetry-related behavior.

## Configuration

`config.yaml` uses schema version `1`. The default configuration targets a Windows 11 PC profile and uses the direct Vulkan graphics backend.

### Device profiles

The `device` value accepts these short presets:

- `pc-windows-11`
- `mobile-pixel-7`
- `console-ps5`

A custom device profile can be supplied instead of a preset:

```yaml
device:
  type: mobile
  platform: Android
  name: Google Pixel 9 Pro
  manufacturer: Google
  model: Pixel 9 Pro
  brand: google
  code: komodo
  sku: pixel-9-pro
  soc_model: Google Tensor G4
  touch: true
  mouse: false
  keyboard: false
```

### Runtime and graphics

- `runtime.headless` starts Mocktail without a visible SDL window when set to `true`.
- `appearance.theme` supports `roblox`, `dark`, `light`, and `system`.
- `graphics.backend` supports `direct-vulkan`, `opengl`, `system`, and `angle-vulkan`.
- `graphics.frame_rate_limit` accepts `display`, `unlimited`, or a positive integer. Use `-1` or leave it unset to let Roblox control the frame-rate limit.
- `graphics.vsync` optionally accepts `auto`, `on`, or `off`.

Use `opengl` when Vulkan is unavailable or unreliable. OpenGL ES 3.0 or newer is required.

### Performance

The performance section controls resource usage:

- `multithreaded_rendering` can size Roblox scheduler and rendering queues from all physical CPU cores.
- `physics_worker_mode` supports `auto`, `latency`, and `throughput`.
- `memory_limit_mb` sets a hard process-tree memory limit. `0` disables the limit.
- `gamemode` supports `auto`, `on`, and `off` for Feral GameMode integration.

The current configuration uses latency-oriented physics scheduling, a 2560 MiB memory limit, and requests GameMode when available.

### Active customizations

The supplied configuration includes these non-default or explicitly selected values:

- The frame-rate limit is set to `30` FPS.
- Physics workers use `latency` mode instead of the default `throughput` mode.
- Mocktail is limited to `2560` MiB of memory instead of having no hard limit.
- Feral GameMode is requested with `gamemode: on`.
- Discord Rich Presence place names, elapsed time, and server joining are disabled.
- The initial window size is set to `1x1` logical desktop units instead of `1280x720`. This is unusually small and may be intentional for a minimized or hidden-style launch; change it if a normal visible window is required.

Other active settings retain their documented defaults, including the Windows 11 PC device preset, the direct Vulkan backend, the Roblox theme, default audio devices, disabled system proxy, and automatic updates.

### Audio and Discord

`audio.output_device` and `audio.input_device` can use the host defaults or exact SDL device names. An input device can also be disabled with `disabled`.

Discord Rich Presence is disabled by default. When enabled, it can publish the current Roblox experience, elapsed session time, and an optional public-server join action. It does not sign in to Discord or read an account token.

Keep `public_servers_only: true` when sharing join actions to avoid exposing private or reserved servers.

### Window and network

The `window` section controls the initial logical size, title, and high-DPI rendering. The `network` section supports:

- The host HTTP/SOCKS5 proxy through `use_system_proxy`.
- A fixed HTTP proxy through `proxy_host` and `proxy_port`.
- A custom PEM CA bundle through `ca_bundle`.

Do not combine the system proxy with a fixed proxy. A proxy host must not include a URL scheme such as `http://`.

### Updates

Automatic updates are enabled in the supplied configuration. Mocktail checks for the latest Roblox version, derives a compatible host profile, runs isolated canaries with the selected graphics backend, and only promotes a payload after successful checks. The existing payload is preserved when probation fails.

The configured provider source is `apk-pure`; `auto` and `apk-pure` are supported source values.

## Fast flags

`fflags.json` contains rendering and quality overrides, including:

- Disabling post-processing effects and dynamic grass movement.
- Reducing or disabling grass, shadow, and selected visual effects.
- Forcing low texture and level-of-detail settings.
- Disabling several Roblox telemetry counters, events, and statistics.

Fast flags can materially change visual quality and behavior. Review them when troubleshooting rendering problems or after Roblox updates.

## Getting started

1. Install Mocktail and its host dependencies according to the distribution or package that provides it.
2. Place `config.yaml` in the configuration location used by Mocktail.
3. Adjust the device, graphics backend, frame-rate limit, memory limit, audio devices, and update settings for the host system.
4. Keep optional settings commented out until they are needed.
5. Start Mocktail and review its startup output for available audio devices and update or graphics diagnostics.

## Notes

- The configuration file is intended to be created once with restrictive permissions (`0600`). Mocktail preserves user edits.
- Use absolute paths for `roblox_library` and `ca_bundle`.
- Audio device IDs may change between boots; prefer an exact device name when it is unique.
- A custom `application_id` is only needed when overriding the Discord application bundled by the package maintainer.

## License

No license file is included in this repository. Add a license before distributing the project publicly.
