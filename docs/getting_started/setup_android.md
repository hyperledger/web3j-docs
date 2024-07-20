Setting up Latest Web3J Library for Android Development
--------------------------------------------------------

Web3j is a lightweight, highly modular, reactive, and type-safe Java and Android library designed to work with Smart Contracts and integrate with clients (nodes) on the Ethereum network. This powerful library enables developers to interact with the Ethereum blockchain without the additional overhead of writing custom integration code. In this article, we’ll walk through the steps to set up Web3j for Android development using the latest version.

> The latest version of Web3J Library (4.12.0) requires at least **JDK version 17**

## Step 1: Add Web3j Dependency

### Using Maven:

Add the following dependency to your pom.xml file:

```java
<dependency>
 <groupId>org.web3j</groupId>
 <artifactId>core</artifactId>
 <version>4.12.0</version>
</dependency>
```

### Using Gradle (Kotlin):

Add the Web3j dependency to your build.gradle.kts file :

```kotlin
dependencies {
   implementation("org.web3j:core:4.12.0")
}
```

## Step 2: Update Packaging Options

To avoid conflicts with certain files included in the Web3j library, you need to exclude specific resources. Add the following code snippet to your module’s build.gradle.kts file within the android block:

```kotlin
android {
   packagingOptions {
       resources {
           excludes += "/META-INF/DISCLAIMER"
       }
   }
}
```

## Step 3: Sync Gradle

After adding the dependencies and updating the packaging options, sync your Gradle files to ensure all changes are applied. This can be done by clicking the “**Sync Now**” prompt that appears in the top-right corner of Android Studio, or by selecting “**File > Sync Project with Gradle Files”**.


