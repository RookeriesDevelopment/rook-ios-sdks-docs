# Changelog

## 0.2.0

* Added flutter flag to `AuthorizationProvider` constructor, set it to true when using this SDK from
  a flutter plugin to enable compatibility with flutter's `shared_preferences` package.

## 0.1.0

* Removed `enableLogs` function from `AuthorizationProvider`.
* Added optional `logLevel` parameter to `AuthorizationProvider`'s constructor.
    * "ADVANCED" -> All logs from API. All logs from SDK.
    * "BASIC" -> Basic logs from API. All logs from SDK.
    * "NONE" -> No logs.

## 0.0.1

* Initial release.
