# NativeLan Android SDK

The NativeLan Android SDK allows you to deliver real-time over-the-air (OTA) localization string updates directly to your native Android apps. By intercepting resources transparently, standard `getString(R.string.key)` calls instantly resolve to the latest published translation assets.

---

## 💻 Installation

### 1. Configure Repository
Add the custom Maven repository to your root `settings.gradle` (for Gradle 7.0+):

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url 'https://raw.githubusercontent.com/Work-On-Idea/nativelan-android-sdk/main/' }
    }
}
```

Or to your project-level `build.gradle` (for older Gradle versions):
```groovy
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://raw.githubusercontent.com/Work-On-Idea/nativelan-android-sdk/main/' }
    }
}
```

### 2. Add Dependency
Add the SDK dependency to your application module's `build.gradle`:

```groovy
dependencies {
    implementation 'com.improvertech:nativelan-android:0.0.1'
}
```

---

## 🚀 Usage Guide

### 1. Initialize the SDK
Initialize `NativeLan` inside your custom `Application` class `onCreate` lifecycle hook:

```kotlin
package com.example.myapp

import android.app.Application
import com.improvertech.nativelan.NativeLan

class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()

        NativeLan.initialize(
            context = this,
            projectId = "your_project_id",
            sdkKey = "nl_sk_live_...",
            baseUrl = "https://your-nativelan-server.com",
            prerelease = false, // Set to true inside Dev/QA builds
            appVersion = "1.0.0" // Enables bundle version freeze rules
        )
    }
}
```

### 2. Intercept Base Context in Activities
Override `attachBaseContext` in your Base Activity (or individual Activities) to inject the custom Resources wrapper:

```kotlin
package com.example.myapp

import android.content.Context
import androidx.appcompat.app.AppCompatActivity
import com.improvertech.nativelan.NativeLan

open class BaseActivity : AppCompatActivity() {
    override fun attachBaseContext(newBase: Context) {
        super.attachBaseContext(NativeLan.wrap(newBase))
    }
}
```

### 3. Retrieve Strings Automatically
Any standard UI elements utilizing resources or manual lookups will now automatically pull from the OTA updates:

```kotlin
// In your Activity/Fragment:
val welcomeMsg = getString(R.string.welcome_message) 
// Will return the OTA string if published on NativeLan, otherwise falls back to local strings.xml!

// Supporting token replacements (%s placeholder formatting):
val formatted = getString(R.string.user_score, "John", 42)
```

### 4. Manual String Retrieval (Optional)
If you need to retrieve a string outside an Activity layout context:
```kotlin
val directVal = NativeLan.t("welcome_message")
```
