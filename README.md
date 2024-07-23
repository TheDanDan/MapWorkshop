# MapWorkshop

# Setup from GitHub Repo
Create and open a `secrets.properties` file at the root (same folder as `local.properties`)
- Add `MAPS_API_KEY=YOUR_API_KEY`
- Replace `YOUR_API_KEY` with the key

Go to [Example](#example)


# Setup from Empty View
https://developers.google.com/maps/documentation/android-sdk/maps-compose
# Step 1: Google Maps API
**Go to:** https://developers.google.com/maps/documentation/android-sdk/get-api-key
- get API key
- restrict on Android 
	- com.example.[sample]
	- Gradle
		- Window button
		- gradle signingreport
		- Get SHA-1
# Step 2: Add Dependencies

https://developers.google.com/maps/documentation/android-sdk/config#kotlin
### 2.1 Setup Maps SDK
In the top-level `settings.gradle`, include the Gradle plugin portal, Google Maven repository, and Maven central repository. **Add to the top** before any other statements.
```programming
pluginManagement {  
    repositories {  
        gradlePluginPortal()  
        google()  
        mavenCentral()  
    }  
}
```

Also add
```programming
dependencyResolutionManagement {  
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)  
    repositories {  
        google()  
        mavenCentral()  
    }  
}
```

*Most people will probably have this already.*

#### Module `build.gradle`
In the **module** `build.gradle`, add Google Play services dependency
```programming
dependencies {    
	// Maps SDK for Android    
	implementation(libs.maps.compose)  
}
```

Ensure that **compileSdk is set to 34** or higher and **minSdk is set to 21** or higher.
```programming
android {    
	compileSdk = 34    
	defaultConfig {        
		minSdk = 21    
	}  
}
```
Then add to access metadata values
```programming
android {  // ...  
	buildFeatures {    
		buildConfig = true    // ...  
	}  
}
```

### 2.2 Add API Key
Store the API Key in a file named `secrets.properties` located on the **root** of your project

In the **top-level** `build.gradle` file add
```programming
buildscript {  
	dependencies {  
		classpath("com.google.android.libraries.mapsplatform.secrets-gradle-plugin:secrets-gradle-plugin:2.0.1")  
	}  
}
```
In the **module-level** `build.gradle`file add
```programming
plugins {  
	id("com.google.android.libraries.mapsplatform.secrets-gradle-plugin")  
}
```

Create and open a `secrets.properties` file at the root (same folder as `local.properties`)
- Add `MAPS_API_KEY=YOUR_API_KEY`
- Replace `YOUR_API_KEY` with the key
> [!NOTE]
> The purpose of this file is to provide a backup location for the API key if the `secrets.properties` file is not found so that builds don't fail. This can happen if you clone the app from a version control system which omits `secrets.properties` and you have not yet created a `secrets.properties` file locally to provide your API key.

Add to `AndroidManifest.xml` in the application
```xml
<meta-data    
	android:name="com.google.android.geo.API_KEY" 
	android:value="${MAPS_API_KEY}" />
```

Add to **module-level** `build.gradle`
```programming
secrets {
    // Optionally specify a different file name containing your secrets.
    // The plugin defaults to "local.properties"
    propertiesFileName = "secrets.properties"
    // A properties file containing default secret values. This file can be
    // checked in version control.
    defaultPropertiesFileName = "local.defaults.properties"
    // Configure which keys should be ignored by the plugin by providing regular expressions.
    // "sdk.dir" is ignored by default.
    ignoreList.add("keyToIgnore") // Ignore the key "keyToIgnore"
    ignoreList.add("sdk.*")  // Ignore all keys matching the regexp "sdk.*"
}
```

### 2.3 Update App Manifest
Add to `AndroidManifest.xml` 

Add to `application` to embed Google Play Services
```xml
<meta-data  
    android:name="com.google.android.gms.version"  
    android:value="@integer/google_play_services_version" />
```

Add permissions 
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>  
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

# Example

```kotlin
val singapore = LatLng(1.35, 103.87)
val cameraPositionState = rememberCameraPositionState {
  position = CameraPosition.fromLatLngZoom(singapore, 10f)
}
GoogleMap(
  modifier = Modifier.fillMaxSize(),
  cameraPositionState = cameraPositionState
) {
  Marker(
    state = MarkerState(position = singapore),
    title = "Singapore",
    snippet = "Marker in Singapore"
  )
}
```


