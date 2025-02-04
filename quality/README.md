# Quality Tools
These are default configurations for [checkstyle](https://github.com/checkstyle/checkstyle), [pmd](https://github.com/pmd/pmd),
[spotbugs](https://plugins.gradle.org/plugin/com.github.spotbugs), [jacoco](https://github.com/jacoco/jacoco) and
[detekt](https://github.com/arturbosch/detekt), they do the following:

* configure a project to use the tool by including a single script
* register a task that will run as part of the `check` task
```groovy
// android plugin -  applies all checkstyle, pmd, spotbugs and jacoco to android projects
apply from: '../config/quality/android.gradle'
// java plugin - applies only checkstyle to java projects (TODO: add others)
apply from: '../config/quality/java.gradle'
```

Alternative you can apply the tool configurations individually.

### Checkstyle
Rules for the [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html) are provided in `checkstyle/checkstyle.xml`.
To make Android Studio aware of the rules, you'll need to install the [CheckStyle-IDEA plugin](https://plugins.jetbrains.com/plugin/1065-checkstyle-idea).
```groovy
// java plugin
apply from: '../config/quality/checkstyle/java.gradle'
// android plugin
apply from: '../config/quality/checkstyle/android.gradle'

// optional: extra configuration options, see https://docs.gradle.org/current/dsl/org.gradle.api.plugins.quality.CheckstyleExtension.html
checkstyle {
    ignoreFailures false
}
```

### PMD
```groovy
// android plugin
apply from: '../config/quality/pmd/android.gradle'

// optional: extra configuration options, see https://docs.gradle.org/current/dsl/org.gradle.api.plugins.quality.PmdExtension.html
pmd {
  consoleOutput = false
}
```

### Spotbugs
```groovy
// android plugin
apply from: '../config/quality/spotbugs/android.gradle'

// optional: extra configuration options, see https://plugins.gradle.org/plugin/com.github.spotbugs
spotbugs {
  showProgress = false
}
```

## Jacaco
```groovy
// in android project
apply from: '../config/quality/jacoco/android.gradle'
// opional, see https://docs.gradle.org/current/dsl/org.gradle.testing.jacoco.plugins.JacocoPluginExtension.html
jacoco {
  toolVersion = 'x.y.z'
}
```

## Errorprone (with NullAway)
```groovy
// root script
buildscript {
  apply from: "config/index.gradle"
}
plugins {
  id 'net.ltgt.errorprone' version '0.7' apply false
}

// sub project
apply from: '../config/quality/errorprone/android.gradle'
// optional: extra configuration options, see https://github.com/tbroyer/gradle-errorprone-plugin and http://errorprone.info/docs/flags
tasks.withType(JavaCompile) {
    options.errorprone.errorproneArgs += [ '-Xep:DeadException:WARN', '-Xep:GuardedByValidator:OFF' ]
}
```

## Detekt
Static code analysis for Kotlin.

```groovy
// root script
buildscript {
  apply from: "config/index.gradle"
}
plugins {
  id 'io.gitlab.arturbosch.detekt' version '1.19.0'
}

// sub project
apply from: "../config/quality/detekt/android.gradle"
dependencies {
  // Add to enable the KtLint rules
  detektPlugins "io.gitlab.arturbosch.detekt:detekt-formatting:1.19.0"
}
```

To test it, use the task : `./gradlew detekt`. See [the detekt docs](https://arturbosch.github.io/detekt/groovydsl.html) for additional configuration options.

## Gitleaks

Prevents you from committing passwords and other sensitive information to a git repository.

```groovy
// sub project
apply from: "../config/quality/git-leaks/build.gradle"
```

By default, Gitleaks will use the default config to detect secrets. You can also write your own secret detection rules by following those steps:
1- Create  `gitleaks.toml` file in the `.git/hooks` folder of your root project.
2- Refer to the [default gitleaks config](https://github.com/zricethezav/gitleaks/blob/master/config/gitleaks.toml) for examples and advice on writing regular expressions for secret detection.
3- **⚠** Note that this will **overwrite** the default Gitleaks config, so, if you want to keep the same rules in addition to yours, you have to copy the [default rules](https://github.com/zricethezav/gitleaks/blob/master/config/gitleaks.toml) to your config file.

See the [Gitleaks doc](https://github.com/zricethezav/gitleaks) for more details.
