# Rust and Tauri

## Overview 👀

{% hint style="info" %}
`bugsplat-rs` is published on crates.io as [`bugsplat`](https://crates.io/crates/bugsplat), [`tauri-plugin-bugsplat`](https://crates.io/crates/tauri-plugin-bugsplat) and [`bugsplat-sys`](https://crates.io/crates/bugsplat-sys). API documentation is on [docs.rs](https://docs.rs/bugsplat).
{% endhint %}

`bugsplat-rs` brings BugSplat crash reporting to Rust applications on Windows, macOS and Linux. It is built on bugsplat-native, BugSplat's out-of-process crash reporter, which uses [Crashpad](https://chromium.googlesource.com/crashpad/crashpad) to capture crashes:

* **Native crashes**: segfaults, illegal instructions, `abort()`, stack overflows, and crashes in C or C++ libraries you link, captured as minidumps by a separate monitor process. The crashing process doesn't have to do any work to report itself.
* **Rust panics**: a panic hook captures a minidump of the live process with the panicking thread on top, plus the panic message, source location and thread name as searchable attributes.
* **One report per failure**: when a panic is followed by an abort (`panic = "abort"`, or a panic that unwinds into an `extern "C"` function), BugSplat reports the panic and discards the abort.
* **JavaScript errors** in Tauri webviews: uncaught errors, unhandled promise rejections and handled errors you post, with their `Error.cause` chains, sent through Rust so your Content Security Policy needs no changes.
* **Rust function names** in every stack trace, demangled when you upload symbols with `symbol-upload`.

| Crate | What it is |
| --- | --- |
| `bugsplat` | Safe Rust API: `init`, attributes, attachments, non-fatal reports, panic reports and (feature `js`) JavaScript error reports |
| `tauri-plugin-bugsplat` | Tauri 2 plugin that reports JavaScript errors from every webview |
| `bugsplat-sys` | Raw FFI bindings. It builds bugsplat-native from source with CMake and links it statically |

## Installation 🏗

`bugsplat-sys` compiles bugsplat-native when you build your app, so you need:

* Rust 1.81 or later
* CMake 3.22 or later and Ninja (Ninja is required on Windows)
* A C++20 compiler: Xcode 15+, MSVC toolset 14.44+, or clang 15+ / gcc 11+

The build downloads a pinned, checksum-verified Crashpad prebuilt. Set `BUGSPLAT_CRASHPAD_CACHE` to a directory to share that download across projects.

Add the crate:

```bash
cargo add bugsplat
```

## Configuration ⚙️

Call `bugsplat::init` first thing in `main`, so crashes during startup are reported, and keep the returned guard alive for the life of the program:

```rust
fn main() {
    let _bugsplat = bugsplat::init(
        bugsplat::Options::new("your-bugsplat-database", "my-rust-app", env!("CARGO_PKG_VERSION")),
    )
    .expect("BugSplat failed to start");

    bugsplat::set_user("Jane").ok();
    bugsplat::set_email("jane@example.com").ok();
    bugsplat::set_attribute("plan", Some("pro")).ok();

    // ...
}
```

Replace `your-bugsplat-database` with the name of your BugSplat database. The application name and version must match the values you use when uploading symbols.

`Options` is a builder. Everything after `new` is optional:

```rust
use std::time::Duration;
use bugsplat::{DumpType, HangPolicy, Options, UploadPolicy};

let options = Options::new("your-bugsplat-database", "my-rust-app", env!("CARGO_PKG_VERSION"))
    .upload_policy(UploadPolicy::Dialog)        // or Quiet (no UI), or Manual
    .dump_type(DumpType::Normal)                // or Heap, or Full
    .key("en-US")                               // application key
    .user("Jane")
    .email("jane@example.com")
    .description("Description!")
    .attribute("plan", "pro")                   // searchable key/value attributes
    .attachment("app.log")                      // copied into every report
    .hang_detection(Duration::from_secs(5), HangPolicy::Report)
    .panic_reports(true);                       // default
```

The same values can change at runtime:

```rust
bugsplat::set_user(user)?;                      // The name or id of your user
bugsplat::set_email(email)?;                    // The email of your user
bugsplat::set_key(key)?;                        // Additional metadata that can be queried in BugSplat
bugsplat::set_description(description)?;        // A description of the report
bugsplat::set_attribute(name, Some(value))?;    // Searchable attributes; None removes one
bugsplat::add_attachment(path)?;                // A file to attach to later reports
bugsplat::set_quiet_mode(true);                 // Switch between the dialog and silent uploads
bugsplat::capture_report(Some("checkpoint"))?;  // Report the live process without crashing
```

### Packaging the helpers

bugsplat-native runs two helper executables next to your app: `BugSplatMonitor` captures crashes, and `BugSplatReporter` shows the crash dialog and uploads the report. `bugsplat-sys` builds both. To find them from your `build.rs`, add `bugsplat-sys` to your `[dependencies]` too; its paths then reach your build script as `DEP_BUGSPLAT_MONITOR` and `DEP_BUGSPLAT_REPORTER`.

Ship them where bugsplat-native looks for them: next to your executable, in a `Helpers/` directory beside it, or on macOS in `<App>.app/Contents/Helpers`. During development nothing needs packaging: when `cargo run` finds no packaged helpers, `bugsplat` uses the ones built for that compilation.

## Tauri 🦀

Add both crates:

```bash
cargo add bugsplat tauri-plugin-bugsplat
```

Initialize `bugsplat` at the top of `main`, before Tauri starts, then register the plugin:

```rust
fn main() {
    let _bugsplat = bugsplat::init(
        bugsplat::Options::new("your-bugsplat-database", "my-tauri-app", env!("CARGO_PKG_VERSION")),
    )
    .expect("BugSplat failed to start");

    tauri::Builder::default()
        .plugin(tauri_plugin_bugsplat::init())
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

Add the plugin's permission to your capability (for example `src-tauri/capabilities/default.json`):

```json
{
    "permissions": ["core:default", "bugsplat:default"]
}
```

The plugin injects a small script into every webview before your page's scripts run. It reports uncaught errors and unhandled promise rejections, including `Error.cause` and `AggregateError` chains (see [Error Chains](../web/javascript.md#error-chains)). Reports go from the webview to Rust and from Rust to BugSplat, so your Content Security Policy needs no BugSplat origin. Each report carries the same `session` attribute as native reports from that run, plus `tauri.version` and `webview.version`.

Report handled errors yourself with `window.BugSplat.post`:

```javascript
try {
    loadUserSettings();
} catch (error) {
    window.BugSplat.post(error, { screen: 'settings' });
}
```

To bundle the helpers on macOS, copy the paths in `DEP_TAURI_PLUGIN_BUGSPLAT_MONITOR` and `DEP_TAURI_PLUGIN_BUGSPLAT_REPORTER` into a `bugsplat-helpers/` directory in your `build.rs`, before calling `tauri_build::build()`, and map them into the bundle in `tauri.conf.json`:

```json
"bundle": {
    "macOS": {
        "files": {
            "Helpers/BugSplatMonitor": "bugsplat-helpers/BugSplatMonitor",
            "Helpers/BugSplatReporter.app": "bugsplat-helpers/BugSplatReporter.app"
        }
    }
}
```

## Symbols 🗺️

Build releases with line tables so BugSplat can show file names and line numbers:

```toml
[profile.release]
debug = "line-tables-only"
split-debuginfo = "packed"   # macOS: writes my-rust-app.dSYM
```

Then upload the debug information for every released version with [@bugsplat/symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md). The `-m` flag converts it to Breakpad `.sym` files with `dump_syms`, which demangles Rust function names:

| Platform | Upload | Notes |
| --- | --- | --- |
| macOS | `target/release/my-rust-app.dSYM` | Written by `split-debuginfo = "packed"` |
| Linux | `my-rust-app.debug` | Build with `CARGO_PROFILE_RELEASE_SPLIT_DEBUGINFO=off`, then run `objcopy --only-keep-debug my-rust-app my-rust-app.debug` and `objcopy --strip-debug --add-gnu-debuglink=my-rust-app.debug my-rust-app`. Upload the `.debug` file and ship the stripped binary. `dump_syms` can't read `.dwp` files, so split DWARF loses your code's line numbers. |
| Windows | `target/release/my-rust-app.pdb` | Always written by MSVC |

```bash
npx @bugsplat/symbol-upload -i $SYMBOL_UPLOAD_CLIENT_ID -s $SYMBOL_UPLOAD_CLIENT_SECRET -b your-bugsplat-database -a my-rust-app -v 1.2.3 -d target/release -f "my-rust-app.dSYM" -m
```

Verify that your symbols show up on the [Versions](https://app.bugsplat.com/v2/versions) page. Create client credentials on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.

## Generate a Crash 💥

Panic:

```rust
let settings: Vec<&str> = Vec::new();
let first = settings[0]; // index out of bounds
```

Crash natively:

```rust
std::process::abort();
```

Or, to test an access violation (`SIGSEGV` on macOS and Linux), write through a null pointer. This is deliberately undefined behavior, so keep it to crash tests:

```rust
let p: *mut u32 = std::hint::black_box(std::ptr::null_mut());
unsafe { p.write_volatile(0xdead) };
```

## View BugSplat Crash Report

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat. You should see new crash reports for your application. Click the link in the **ID** column to see details about your crash on the [Crash](https://app.bugsplat.com/v2/crash?id=1) page. Panic reports show the panic message, location and thread in the `rust.panic.*` attributes.

## How it works 🔬

| Failure | Captured by | Arrives at BugSplat as |
| --- | --- | --- |
| Segfault, illegal instruction, `abort()`, stack overflow | Crashpad in `BugSplatMonitor`, a separate process | Minidump (crash type Crashpad) |
| Rust panic | Panic hook → `bugsplat::capture_report` | Minidump with `rust.panic.message`, `rust.panic.location` and `rust.panic.thread` attributes |
| Panic followed by an abort | The same hook tags the abort's dump as a duplicate, and the monitor discards it | One report, not two |
| JavaScript error in a Tauri webview | Injected script → Tauri command → Rust → `post/js` | JavaScript report with the webview's stack and the run's `session` attribute |

A panic isn't a crash as far as the operating system is concerned. It either unwinds, possibly ending the thread or exiting the process with status 101, or aborts. Neither case reaches a crash handler with the panic's message. So the panic hook writes a minidump of the live process itself, synchronously and out of process, before the panic continues. It reports at most 8 panics per process, so a panic loop can't flood your database. Your existing panic hook still runs afterwards.

## Third-party licenses 📜

`BugSplatMonitor` and `BugSplatReporter` include third-party code (Crashpad, mini_chromium, and on Windows zlib and WTL, among others). If you ship them, include those notices with your app. The **Third-party licenses** section of the [`bugsplat` README on crates.io](https://crates.io/crates/bugsplat) lists each component, its license and where to find the text.

## Contributing 🤝

BugSplat loves open-source software! If you have suggestions on how we can improve this integration, please reach out to support@bugsplat.com.
