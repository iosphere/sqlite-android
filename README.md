# sqlite-android

Ready-built recent SQLite bindings to be used inside Android applications and libraries for Android 4.1 (API 16) and above. Supported architectures: [armeabi-v7a,mips,x86]

If you wanted to use more recent SQLite features inside you Android application, like:

- [FTS5](https://www.sqlite.org/fts5.html)
- [JSON1](https://www.sqlite.org/json1.html)
- [loading custom SQLite extensions](https://www.sqlite.org/loadext.html)

you might be out of luck with the [older version of SQlite](http://stackoverflow.com/questions/2421189/version-of-sqlite-used-in-android) provided by Android, which does not allow you to load custom SQLite extensions, for example.

Therefore **sqlite-android** includes and uses the [SQlite Android bindings](https://www.sqlite.org/android/doc/trunk/www/index.wiki) provided by the SQlite project [sqlite.org](sqlite.org) itself.

The SQlite Android bindings [provided by SQlite](https://www.sqlite.org/android/doc/trunk/www/index.wiki) contain a modified copy of the official Android SQlite bindings [provided by Android](https://github.com/android/platform_frameworks_base/tree/d59921149bb5948ffbcb9a9e832e9ac1538e05a0/core/java/android/database/sqlite) by using a different Java namespace `org.sqlite.database.sqlite` instead of the official Android namespace  `android.database.sqlite`. Since the interfaces of both libraries are the same, using **sqlite-android** should be just a change of an `import` statement to point to the correct library.

By using the SQlite Android bindings, **sqlite-android** is able to pull in the latest SQlite library using the [SQlite amalgamation sources](https://www.sqlite.org/download.html) and [enabling additional SQlite features](library/src/main/jni/sqlite/Android.mk#L18) like:

- [FTS5](library/src/main/jni/sqlite/Android.mk#L16)
- [JSON1](library/src/main/jni/sqlite/Android.mk#L17)
- [loading custom SQLite extensions](library/src/main/jni/sqlite/Android.mk#L19)

### Usage

#####  `build.gradle` dependencies:

```
allprojects {
	repositories {
	  ...
	  maven { url "https://jitpack.io" }
	}
}

dependencies {
  // recent SQlite version
  compile 'com.github.weitzj:sqlite-android:3.10.2'

  // sane shared library loading on Android (optional but recommended)
  compile 'com.github.KeepSafe:ReLinker:1.2'
}
```

##### Load shared library `libsqliteX.so`:

- With ReLinker ([recommended](https://medium.com/keepsafe-engineering/the-perils-of-loading-native-libraries-on-android-befa49dce2db#.iboxtk215)):

`ReLinker.loadLibrary(context, "sqliteX");`

- Without ReLinker:

`System.loadLibrary("sqliteX");`

### Project setup

`ndk-build` is invoked on a CI-server building the native SQlite library `libsqliteX.so`. These built artefacts are checked in and the build is tagged with the SQlite version, which was build.

Afterwards [jitpack.io](jitpack.io) is used to build an Android library project (**aar**), which you can include in your project.

### How to use a different SQlite version

- Change the SQlite sources [sqlite.h](library/src/main/jni/sqlite/sqlite3.h) and [sqlite.c](library/src/main/jni/sqlite/sqlite3.c)
- `cd library/src/main/jni`
- `ndk-build`
- `git add library/src/main/libs`
- `git add library/src/main/jni/sqlite/sqlite.h`
- `git add library/src/main/jni/sqlite/sqlite.c`
- `git commit`
- `git tag 3.10.2`
- `git push --tags`

### TODO

- Use a public travis CI to do the `ndk-build` step to make the build process more transparent.
- Naming scheme for git tags for different SQlite features.


### Inspired by

- [SQLite Android bindings](https://www.sqlite.org/android/doc/trunk/www/index.wiki)
- [SQLCipher](https://www.zetetic.net/sqlcipher/)
- [geopaparazzu/libjsqlite-spatialite-android](https://github.com/geopaparazzi/libjsqlite-spatialite-android)
