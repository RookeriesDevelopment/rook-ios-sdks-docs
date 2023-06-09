# Rook Users SDK

This SDK allows the [client](https://docs.tryrook.io/docs/Definitions/#client) to register their [Users](https://docs.tryrook.io/docs/Definitions/#User) in ROOK server.

## Features

* Register Users in Health Connect

## Installation

![Maven Central](https://img.shields.io/maven-central/v/com.rookmotion.android/rook-users?color=%23F44336)

Add the following to your dependencies (app level build.gradle):

```groovy
implementation 'com.rookmotion.android:rook-users:version'
```

## Getting started

To get permission of usage you'll need to install and configure
the [rook-auth](https://mvnrepository.com/artifact/com.rookmotion.android/rook-auth)
SDK.

### Logging

If you want to see the logs generated by this SDK

When creating an instance of `RookUsersManager` provide a logLevel:

```kotlin
RookUsersManager(logLevel = "ADVANCED")
```

Available levels:

* "ADVANCED" -> All logs from API. All logs from SDK.
* "BASIC" -> Basic logs from API. All logs from SDK.
* "NONE" -> No logs.

## Usage

Create an instance of `RookUsersManager` providing a context, and URL without https.

```kotlin
val manager = RookUsersManager(context, "api2.rookmotion.review")
```

### Registering Users

To register a User call `registerRookUser` providing your clientUUID, a userID and a userType. It
will return an instance of `RookUser`. After a successful registration it will store a `RookUser`
instance in preferences so the next time you call `registerRookUser` the local value will be
returned. Unless you provide a different userID, in that case it will behave like the first time.

The `userID` is defined by you, so feel free to use the same ID your app uses to identify it's
Users. If you provide an ID which is already exists in the server it will only store it in
preferences.

The `userType` determines where the User will be created. E.g. if you want to create a User for
Health Connect pass `UserType.HEALTH_CONNECT`:

```kotlin
fun registerUser() {
    scope.launch {
        try {
            val result = manager.registerRookUser(CLIENT_UUID, USER_ID, UserType.HEALTH_CONNECT)

            // Success
        } catch (e: Exception) {
            // Manage error
        }
    }
}
```

### Removing registered Users from preferences

This SDK already manages the case were you need to register a different userID (the new userID
will replace the previous one in preferences).

If you want to manually remove a userID call `removeUserFromPreferences` and provide the type of
User you want to remove it will return a boolean indicating if the operation was successful.

```kotlin
fun remove() {
    scope.launch {
        try {
            val result = manager.removeUserFromPreferences(UserType.HEALTH_CONNECT)

            // Success
        } catch (e: Exception) {
            // Manage error
        }
    }
}
```

* `removeUserFromPreferences` will only delete from local storage, the User will remain registered
  on server.

## Additional information

The first time your Users use this SDK they MUST have an active internet connection otherwise
the request will fail and the userID won't be registered or stored.