Setting up Project for Android
--------------------------------------------------------

In this article, we’ll walk through the steps to set up Web3j for Android development using the latest version.

> The latest version of Web3j Library (4.12.0) requires at least **JDK version 17**

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



