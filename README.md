# Paparazzi + Test Distribution Sample

Example repository demonstrating how to set up [Paparazzi](https://github.com/cashapp/paparazzi) snapshot testing with [Develocity Test Distribution (TD)](https://gradle.com/develocity/testing/test-distribution/).

## Key Setup

### Using TDPaparazzi instead of Paparazzi

When running Paparazzi tests with Test Distribution, you **must** use `TDPaparazzi` instead of the standard `Paparazzi` rule. This is provided by the `io.github.cdsap.td.paparazzi` plugin:

```kotlin
plugins {
    id("app.cash.paparazzi")
    id("io.github.cdsap.td.paparazzi")
}
```

In your tests, replace `Paparazzi` with `TDPaparazzi`:

```kotlin
import io.github.cdsap.td.paparazzi.TDPaparazzi

class ExampleUnitTest {
    @get:Rule
    val paparazzi = TDPaparazzi(maxPercentDifference = 0.1)

    @Test
    fun compose() {
        paparazzi.snapshot { HelloPaparazzi() }
    }
}
```

### Merging Reports

This project applies the [`io.github.cdsap.td.paparazzi`](https://github.com/cdsap/td-paparazzi) plugin (version `0.2.0`) to merge
Paparazzi reports when tests are distributed across multiple remote executors.

### Test Distribution Configuration

Test tasks are configured with TD enabled and tests are distributed to remote executors:

```kotlin
tasks.withType<Test>().configureEach {
    develocity.testDistribution {
        enabled = true
        maxLocalExecutors = 0
        maxRemoteExecutors = 3
    }

    inputs.dir(layout.buildDirectory.dir("intermediates/paparazzi"))
    inputs.files(layoutlibResourcesFiles)
    outputs.dir("build/reports/paparazzi/")

    useJUnitPlatform()
}
```

Note: The Paparazzi intermediates and layoutlib resources are declared as task inputs so they are transferred to remote executors(issue [cashapp/paparazzi#2292](https://github.com/cashapp/paparazzi/issues/2292))

## Compatibility

This project has only been tested with:
- **Gradle 9.2.1**
- **Paparazzi 2.0.0-alpha04**

## Running Tests

```bash
./gradlew :android-library:testDebugUnitTest
```

## Develocity example
