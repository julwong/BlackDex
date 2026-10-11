# Repository Notes

## Build system
- This is an Android/Gradle project. Use the checked-in `./gradlew` wrapper, not an installed `gradle`.
- The wrapper is Gradle 6.7.1; root `build.gradle` uses Android Gradle Plugin `com.android.tools.build:gradle:4.2.0` and Kotlin 1.5.0.
- Modules in `settings.gradle`: `:app`, `:Bcore`, `:Bcore:black-hook`, `:Bcore:black-fake`.
- `:app` depends on `:Bcore`. Bcore owns the runtime and native code and depends on the hook/fake modules.

## Build commands
```sh
./gradlew assembleDebug
./gradlew :app:assembleBlackDex32Debug
./gradlew :app:assembleBlackDex64Debug
```

## Tests
```sh
./gradlew test
./gradlew :app:testBlackDex32DebugUnitTest
./gradlew :app:testBlackDex64DebugUnitTest
./gradlew :app:testBlackDex32DebugUnitTest --tests 'top.niunaijun.blackdex.ExampleUnitTest'
```

## Prerequisites and gotchas
- Use a JDK compatible with Gradle 6.7.1; newer Java producing class-file version 71 fails.
- On this macOS workspace, use JDK 11 at `/opt/homebrew/opt/openjdk@11` and the SDK at `$HOME/Library/Android/sdk`; export `JAVA_HOME`, `ANDROID_SDK_ROOT`, and `ANDROID_HOME` because no `local.properties` is checked in.
- Native builds require SDK platform 28, build tools 28.0.2 and 30.0.3, NDK 21.4.7075529, and CMake 3.10.2.4988404.
- Flavors split ABIs: `BlackDex32` = `armeabi-v7a`; `BlackDex64` = `arm64-v8a`.
- On Apple Silicon, QEMU2 cannot boot the `armeabi-v7a` AVD; use an `arm64-v8a` AVD with the BlackDex64 APK. Test BlackDex32 on a compatible physical device.
- Java and Kotlin target 1.8. Bcore-related modules request substantial heap (4 GiB for dex work); `gradle.properties` gives the Gradle daemon 2 GiB.
- README's “Make Project” is not an executable build procedure; use the wrapper commands above.

## Runtime wiring
- `App` -> `BlackDexLoader` -> `BlackDexCore`/`BlackBoxCore`; `WelcomeActivity` is the launcher; `DexDumpRepository` drives install, launch, and dump.
- Dump output is `Download/dexDump` on Android R+; older versions use `/dump` under the external-cache parent.
