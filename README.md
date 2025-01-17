This is a native Android application for [Ruffle](https://ruffle.rs).

It is in a very early stage.

# Prebuilt APKs

The latest release [(here)](https://github.com/torokati44/ruffle-android/releases) should have a few `.apk` files uploaded as assets.

You can try this app by downloading and installing one of those.

- **For the vast majority of modern phones, tablets, single board computers, and small game consoles, you'll need the `arm64-v8a` version.**

- The `armeabi-v7a` version is for older, 32-bit ARM stuff.

- The `x86_64` version is for some rare Intel/Microsoft tablets and/or for Chromebooks, and/or for running on a PC on Android-x86 or in Waydroid or similar.

- The `x86` version is there mostly just for completeness.

- The `universal` version should work on all 4 of the above architectures, but it's _huge_.

# Build Prerequisites

- Install Android Sudio with at least the Platform SDK (e.g. version 29) and the NDK Tools (e.g. version 25).
- Ensure `java` is Java 17.
- `cargo install cargo-ndk`
- `rustup target add aarch64-linux-android armv7-linux-androideabi x86_64-linux-android i686-linux-android`

# Build Steps

```bash
# substitute the appropriate location and version:
export ANDROID_NDK_ROOT=$HOME/Android/Sdk/ndk/24.0.8215888/
cd app
./gradlew assembleDebug # the "release" version requires a keyfile
```

The final APK should be at: `app/ruffle/build/outputs/apk/debug/ruffle-universal-debug.apk`
There are also single-arch split APKs next to it.

Opening the `app` project in Android Studio for development and debugging also works.

# Development Tips

Set the target of `rust-analyzer` to `x86_64-linux-android` (or even `aarch64-linux-android`), to stop one of the NDK crates from erroring out due to compilation to an unsupported platform.

Also set the `ANDROID_HOME` and `ANDROID_NDK_ROOT` extra environment variables for it to suitable values, so the C parts of the dependencies can also be built by it.

You may also need to set the `TARGET_CC`, `TARGET_CXX`, and `TARGET_AR` environment variables to the full paths of the (sometimes API level-specific) `clang`, `clang++`, and `llvm-ar` binaries, respectively.

---

# TODO

In no particular order:

- [ ] Ability to show the built-in virtual keyboard (softinput), for text input
- [ ] Controller/Gamepad input?
  - Mapped to key presses and/or virtual mouse pointer
- [ ] Own custom keyboard overlay, maybe even per-content configs
  - Not an overlay, and not per-content, but custom keyboard is there
- [ ] Navigator backend (fetch, open browser)
  - Opening links works at least
- [ ] Error/panic handling
- [ ] Loading "animation" (spinner)
- [ ] Logging?
- [ ] Alternative audio backend (OpenSL ES) for Android < 8
- [ ] UI backend (context menu)
  - Context menu works
- [ ] Proper storage backend?
- [ ] Cross-platform build instructions?
  - I think gradle should take care of it now
- [ ] Resolve design glitches/styling/theming (immersive mode, window insets for holes/notches/corners)
- [ ] Publish to various app stores, maybe automatically?
- [ ] Bundle demo animations/games
- [ ] Add ability to load content from well known online collections? (well maybe not z0r... unless?)
- [ ] History, favorites, other flair...?
- [ ] Clean up ~everything

### DONE:

- [X] Touch/mouse input
- [X] Keyboard input: only with physical keyboard connected or through `scrcpy`
  - This was needed: https://github.com/rust-windowing/winit/pull/2226
- [X] Split into a separate repo
- [X] Add ability to Open SWF by entered/pasted URL (or even directly from clipboard)
  - No direct clipboard open, but easy to paste into the text field...
- [X] Unglitchify rendering: scale, center and letterbox the content properly
- [ ] Ask CPAL/Oboe to open a "media" type output stream instead of a "call" one
  - so the right volume slider controls it, and it uses the loud(er)speaker
  - -> solved by switching to a direct AAudio (ndk-audio) backend
- [X] Add building this to CI, at least to the release workflow
  - This repo has its own CI setup, which builds APKs
- [X] Simplify build process (hook cargo-apk into gradle, drop cargo-apk?)
  - ~cargo-apk is fine, but is only used to detect the SDK/NDK environment and run Cargo in it, and not to build an APK.~
  - actually solved by switching to `cargo-ndk` and the corresponding Gradle plugin
- [X] Somehow filter files to be picked to .swf
  - How well this works depends on the file picker, but it "should work most of the time"
- [X] Unglitchify audio volume (buttons unresponsive?)
  - (pending: https://github.com/rust-windowing/winit/pull/1919)
  - actually solved by switching to GameActivity instead
- [ ] Register Ruffle to open .swf files
  - How well this works depends on the application opening the file, but it "should work most of the time"
- [X] Figure out why videos are not playing (could be a seeking issue)
  - The video decoder features weren't enabled on `ruffle_core`...
- [X] Sign the APK
  - Using a very simple key for now, with just my name in it
- [X] Support for 32-bit ARM phones
  - Untested, but should work in theory
- [X] Support for x86(_64) tablets?
  - Sorted out
- [X] Consider not building the intermediate .apk just for the shared libraries
  - Figured out, no intermediate .apk any more, only native libs built
- [ ] Unbreak the regular build on CI
  - No longer relevant after the repo split
- [ ] Clean up commit history of the branch
  - No longer relevant after the repo split
