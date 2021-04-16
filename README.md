# btleplug

[![Crates.io Version](https://img.shields.io/crates/v/btleplug)](https://crates.io/crates/btleplug)
[![docs.rs page](https://docs.rs/btleplug/badge.svg)](https://docs.rs/btleplug)
[![Crates.io Downloads](https://img.shields.io/crates/d/btleplug)](https://crates.io/crates/btleplug)
[![Crates.io License](https://img.shields.io/crates/l/btleplug)](https://crates.io/crates/btleplug)

[![Discord](https://img.shields.io/discord/738080600032018443.svg?logo=discord)](https://discord.gg/QGhMFzR)

[![Github donate button](https://img.shields.io/badge/github-donate-ff69b4.svg)](https://www.github.com/sponsors/qdot)


btleplug is a Rust BLE library, support Windows 10, macOS, Linux, and
possibly iOS. It is currently made up of parts of other abandoned
projects with a goal of building a fully cross platform proof of
concept.

Our goal is the bring in some of the outstanding PRs from other
projects, expand the platform support, and possibly make the API
surface more ergonomic for being a truly cross-platform library.

Oh and async might happen to because why not.

btleplug is meant to be *host/central mode only*. If you are
interested in peripheral BTLE (i.e. acting like a Bluetooth LE device
instead of connecting to one), check out
[bluster](https://github.com/dfrankland/bluster/tree/master/src).

This library **DOES NOT SUPPORT BLUETOOTH 2/CLASSIC**. There are no
plans to add BT2/Classic support.

## A Whole New World of Bluetooth Copypasta

At the moment, and probably for the foreseeable future, very little of
what is included in BTLEPlug will be new or original code. The goal
for the moment is to get a single library that works everywhere, then
start bending the API surface around the different platform
requirements once we can at least bring up adapters and start finding
devices everywhere.

The libraries we're forking include:

- [rumble](https://github.com/mwylde/rumble)
  - Started with a bluez implementation, but became our main repo fork
    basis because it was the only library to have even a partial UWP
    implementation. API surface was built to reflect bluez 1:1, which
    makes cross-platform ergonomics a bit difficult, but it's a model
    we can start with for now, and change later.
  - Project seems to be abandoned.
- [blurmac](https://github.com/servo/devices) ([alternative repo?](https://github.com/akosthekiss/blurmac))
  - Complete-ish WebBluetooth BTLE implementation for MacOS/iOS
    CoreBluetooth, originally built for use in Mozilla's Servo
    browser. Makes some assumptions about being embedded in Servo. For
    instance, the base library doesn't spin up any event queues
    because it expects to be embedded in a Cocoa application with a
    main event queue.
  - Project seems to be abandoned.
- [blurdroid](https://github.com/servo/devices) ([alternative repo?](https://github.com/akosthekiss/blurdroid))
  - Same as blurmac, developed for Servo, but handles Android 4.4+'s
    BTLE stack via JNI calls.
  - Project seems to be abandoned.

In addition, here's the libraries we'll be referencing/cribbing from
for updating APIs.

- [bluster](https://github.com/dfrankland/bluster/tree/master/src)
  - BTLE Peripheral library. Uses async rust via Tokio.
  - Active project
- [noble-mac](https://github.com/timeular/noble-mac)
  - Noble (node BTLE module) implementation for MacOS via
    CoreBluetooth. Built in Obj-C and C.
  - Active project
- [noble-uwp](https://github.com/jasongin/noble-uwp)
  - Noble (node BTLE module) implemenation for Windows UWP. Built in
    C++.
  - Active project

## Development Goals

The issues in this repo reflect the development goals of the project.
First and foremost is getting as many platforms as possible up and
running enough to support [our main usage of this
library](https://github.com/buttplugio/buttplug-rs). For the time
being we'll most likely keep the rumble API surface model, just so we
don't have to change large portions of the code as we go.

Beyond that, some of our other goals are:

- Make API more ergonomic to support multiple bluetooth APIs (not just
  focusing on bluez)
- Add FFI so this library can be used from C (and maybe C++ using
  [cxx](https://github.com/dtolnay/cxx).
- Provide both async and sync versions of as many APIs as possible
  (once again, depending on platform API capabilities)
- Possibly create a WASM compatible layer using
  [wasm-bindgen](https://github.com/rustwasm/wasm-bindgen) and
  [WebBluetooth](https://webbluetoothcg.github.io/web-bluetooth/)

## Platform Status

- **Linux / Windows / macOS**
  - Device enumeration and characteristic/services implemented, works
    ok.
  - Please file bugs and missing features if you find them.
- **iOS**
  - Trying to figure out if the macOS implementation will translate,
    minus things having to do with background processing. Might just
    work already?
  - [Tracking issue here](https://github.com/deviceplug/btleplug/issues/12)
  - Please file bugs and missing features if you find them.
- **Android**
  - A rust android library exists (the aforementioned
    [blurdroid](https://github.com/servo/devices)), but getting a PoC
    up and tested is going to require some work. **Definitely looking
    for help**.
  - Tracking issue
    [here](https://github.com/deviceplug/btleplug/issues/8).
  - Please hold off on filing more issues until base implementation is
    landed.
- **WASM/WebBluetooth**
  - WebBluetooth has been added to wasm-bindgen's web-sys by @qdot, and a full
    implementation has been done in other libraries that could easily be ported
    here. This is now definitely in the works, once the new API surface is
    solidified.
  - [Tracking issue here](https://github.com/deviceplug/btleplug/issues/13)
  - Please hold off on filing more issues until base implementation is
    landed.

### Platform Feature Table

- X: Completed and released
- O: In development
- Blank: Not started

| Feature | Windows | MacOS | Linux |
|---------|---------|-------|-------|
| Bring Up Adapter |X|X|X|
| Handle Multiple Adapters |||X|
| Discover Devices |X|X|X|
| └ Discover Services ||| [O](https://github.com/deviceplug/btleplug/issues/11) |
| └ Discover Name |X|X|X|
| └ Discover Manufacturer Data |X||X|
| GATT Server Connect |X|X|X|
| GATT Server Connect Event |X|X|X|
| GATT Server Disconnect |X|X|X|
| GATT Server Disconnect Event |X|X|X|
| Write to Characteristic (Sync) |X|X|X|
| Write to Characteristic (Async) |||X|
| Read from Characteristic (Sync) |X|X|X|
| Read from Characteristic (Async) |X|||
| Subscribe to Characteristic (Sync) |X|X|X|
| Subscribe to Characteristic (Async) ||||
| Unsubscribe from Characteristic (Sync) |X|X||
| Unsubscribe from Characteristic (Async) ||||
| Get Characteristic Notification Event |X|X|X|
| Read Descriptor (Sync) ||||
| Read Descriptor (Async) ||||
| Write Descriptor (Sync) ||||
| Write Descriptor (Async) ||||

## Library Features

#### Serialization/Deserialization

To enable implementation of serde's `Serialize` and `Deserialize` across some common types in the `api` module, use the `serde` feature.

```toml
[dependencies]
btleplug = { version = "0.4", features = ["serde"] }
```

## License

BTLEPlug is covered under a BSD 3-Clause License, with some parts from
Rumble/Blurmac covered under MIT/Apache dual license, and BSD 3-Clause
licenses, respectively. See LICENSE.md for more info and copyright
information.
