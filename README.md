# Cordova/PhoneGap SQLCipher adapter plugin

Native interface to sqlcipher in a Cordova/PhoneGap plugin for Android, iOS, and Windows, with API similar to HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/).

License for Android and Windows versions: MIT or Apache 2.0

License for iOS version: MIT only

|Android Circle-CI (**full** suite)|iOS Travis-CI (*very* limited suite)|
|-----------------------|----------------------|
|[![Circle CI](https://circleci.com/gh/litehelpers/Cordova-sqlcipher-adapter.svg?style=svg)](https://circleci.com/gh/litehelpers/Cordova-sqlcipher-adapter)|[![Build Status](https://travis-ci.org/litehelpers/Cordova-sqlcipher-adapter.svg)](https://travis-ci.org/litehelpers/Cordova-sqlcipher-adapter)|

**WARNING:** In case you lose the database password you have no way to recover the data.

## BREAKING CHANGE: Database location parameter is now mandatory

The `location` or `iosDatabaseLocation` *must* be specified in the `openDatabase` and `deleteDatabase` calls, as documented below.

### IMPORTANT: iCloud backup of SQLite database is NOT allowed

As documented in the "**A User’s iCloud Storage Is Limited**" section of [iCloudFundamentals in Mac Developer Library iCloud Design Guide](https://developer.apple.com/library/mac/documentation/General/Conceptual/iCloudDesignGuide/Chapters/iCloudFundametals.html) (near the beginning):

<blockquote>
<ul>
<li><b>DO</b> store the following in iCloud:
  <ul>
   <li>[<i>other items omitted</i>]</li>
   <li>Change log files for a SQLite database (a SQLite database’s store file must never be stored in iCloud)</li>
  </ul>
</li>
<li><b>DO NOT</b> store the following in iCloud:
  <ul>
   <li>[<i>items omitted</i>]</li>
  </ul>
</li>
</ul>
- <cite><a href="https://developer.apple.com/library/mac/documentation/General/Conceptual/iCloudDesignGuide/Chapters/iCloudFundametals.html">iCloudFundamentals in Mac Developer Library iCloud Design Guide</a>
</blockquote>

### How to disable iCloud backup

Use the `location` or `iosDatabaseLocation` option in `sqlitePlugin.openDatabase()` to store the database in a subdirectory that is *NOT* backed up to iCloud, as described in the section below.

**NOTE:** Changing `BackupWebStorage` in `config.xml` has no effect on a database created by this plugin. `BackupWebStorage` applies only to local storage and/or Web SQL storage created in the WebView (*not* using this plugin). For reference: [phonegap/build#338 (comment)](https://github.com/phonegap/build/issues/338#issuecomment-113328140)

## Available for hire

The primary author and maintainer [@brodybits (Christopher J. Brody aka Chris Brody)](https://github.com/brodybits) is available for contracting assignments. Part-time assignments would really help keep this project alive. ([@brodybits](https://github.com/brodybits) dedicates a significant amount of working time and has turned down multiple full-time work opportunities to maintain and improve this project.)

[@brodybits](https://github.com/brodybits) can be contacted at:
- <brodybits@litehelpers.net>
- <sales@litehelpers.net>
- LinkedIn: <https://www.linkedin.com/in/chrisbrody>

Some other projects by [@brodybits](https://github.com/brodybits):
- [liteglue / Android-sqlite-connector](https://github.com/liteglue/Android-sqlite-connector) - Lightweight SQLiteConnection library with abstraction layer over [liteglue / Android-sqlite-native-driver](https://github.com/liteglue/Android-sqlite-native-driver)
- [brodybits / node-uvhttp](https://github.com/brodybits/node-uvhttp) - HTTP server library that serves static content from native code - *experimental and extremely limited*
- [brodybits / java-node](https://github.com/brodybits/java-node) - two-way binding interface between Java and Node.js (Javascript) - *experimental and extremely limited*

## Status

- Alpha version with SQLCipher `3.4.0`
  - with OpenSSL libcrypto for Android 
  - using CommonCrypto framework for iOS
  - with LibTomCrypt (1.17) embedded for Windows
  - for future consideration: embed OpenSSL libcrypto for all target platforms
- iOS database location is now mandatory, as documented below.
- A recent version of Cordova (such as `6.1.1`) is recommended. Cordova versions older than `6.0.0` are not supported by this project.
- Windows version is in an alpha state (using the performant [doo / SQLite3-WinRT](https://github.com/doo/SQLite3-WinRT) component):
  - Issue with UNICODE `\u0000` character (same as `\0`)
  - No background processing (for future consideration)
  - Uses libTomCrypt for encryption which *may* be inferior to OpenSSL for encryption and apparently runs much more slowly
  - WAL/MMAP *disabled* for Windows Phone 8.1
  - JSON1 not working for Windows
  - **NOTE:** libTomCrypt may have inferior entropy (randomness) for encryption. It is desired to replace libTomCrypt with a recent build of the OpenSSL crypto library.
- Android version:
  - Build from [litehelpers / android-database-sqlcipher-api-fix](https://github.com/litehelpers/android-database-sqlcipher-api-fix), now supports Android N (preview) and fixed for Android API 23
  - ARM (v5/v6/v7/v7a) and x86 CPUs
  - Minimum SDK 10 (a.k.a. Gingerbread, Android 2.3.3); support for older versions is available upon request.
  - SQLCipher for Android build uses the OpenSSL crypto library for encryption
  - NOTE: 64-bit CPUs such as `x64_64`, ARM-64, and MIPS are currently not supported by the SQLCipher for Android build (support for these CPUs is for future consideration).
  - ICU case-insensitive matching and other Unicode string manipulations is no longer supported for Android.
- FTS3, FTS4, FTS5, and R-Tree support is tested working OK for all target platforms in this version branch Android/iOS/Windows
- JSON1 support for Android/iOS (not working for Windows)
- iOS version:
  - iOS versions supported: 7.x/8.x/9.x
  - REGEXP is no longer supported for iOS.
- In case of memory issues please use smaller transactions.
- Pre-populatd DB is NOT supported by this version.
- Lawnchair adapter has *not* been validated with this version *and is not expected to work (see below)*.

## Announcements

- SQLCipher version `3.4.0` for Android/iOS/Windows with FTS5 (all platforms) and JSON1 (Android/iOS)
- Windows 10 UWP is now supported by this version - along with Windows 8.1 and Windows Phone 8.1
- More explicit `openDatabase` and `deleteDatabase` `iosDatabaseLocation` option
- Added simple sql batch query function
- Added echo test function to verify installation of this plugin
- All iOS operations are now using background processing (reported to resolve intermittent problems with cordova-ios@4.0.1)
- Published [brodybits / Cordova-quick-start-checklist](https://github.com/brodybits/Cordova-quick-start-checklist) and [brodybits / Cordova-troubleshooting-guide](https://github.com/brodybits/Cordova-troubleshooting-guide)
- A version with support for web workers is available (with a different licensing scheme _without SQLCipher_) at: [litehelpers / cordova-sqlite-workers-evfree](https://github.com/litehelpers/cordova-sqlite-workers-evfree)
- PhoneGap Build is now supported through the npm package: http://phonegap.com/blog/2015/05/26/npm-plugins-available/
- [MetaMemoryT / websql-promise](https://github.com/MetaMemoryT/websql-promise) now provides a Promises-based interface to both Web SQL and this plugin

## Highlights

- This version connects to [SQLCipher](https://www.zetetic.net/sqlcipher/).
- Drop-in replacement for HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/): the only change should be to replace the static `window.openDatabase()` factory call with `window.sqlitePlugin.openDatabase()`, with parameters as documented below.
- Failure-safe nested transactions with batch processing optimizations (according to HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/))
- As described in [this posting](http://brodyspark.blogspot.com/2012/12/cordovaphonegap-sqlite-plugins-offer.html):
  - Keeps sqlite database in a user data location that is known; can be reconfigured (iOS version); and synchronized to iCloud by default (iOS version; can be disabled as described below).
  - No 5MB maximum, more information at: http://www.sqlite.org/limits.html
- This project is self-contained: no dependencies on other plugins such as cordova-plugin-file
- Windows 8.1/Windows Phone 8.1/Windows 10 version uses the performant C++ [doo / SQLite3-WinRT](https://github.com/doo/SQLite3-WinRT) component.
- Intellectual property:
  - All source code is tracked to the original author in git
  - Major authors are tracked in AUTHORS.md
  - Licensing of each component is tracked in LICENSE.md
  - History of this project is also described in HISTORY.md

## Some apps using Cordova SQLCipher adapter

TBD *YOUR APP HERE*

## Known issues

- iOS version does not support certain rapidly repeated open-and-close or open-and-delete test scenarios due to how the implementation handles background processing
- As described below, auto-vacuum is NOT enabled by default.
- INSERT statement that affects multiple rows (due to SELECT cause or using TRIGGER(s), for example) does not report proper rowsAffected on Android
- Memory issue observed when adding a large number of records due to the JSON implementation which is improved in [litehelpers / Cordova-sqlite-enterprise-free](https://github.com/litehelpers/Cordova-sqlite-enterprise-free) (available with a different licensing scheme *without SQLCipher*)
- A stability issue was reported on the iOS version when in use together with [SockJS](http://sockjs.org/) client such as [pusher-js](https://github.com/pusher/pusher-js) at the same time (see [litehelpers/Cordova-sqlite-storage#196](https://github.com/litehelpers/Cordova-sqlite-storage/issues/196)). The workaround is to call sqlite functions and [SockJS](http://sockjs.org/) client functions in separate ticks (using setTimeout with 0 timeout).
- If a sql statement fails for which there is no error handler or the error handler does not return `false` to signal transaction recovery, the plugin fires the remaining sql callbacks before aborting the transaction.
- In case of an error, the error `code` member is bogus on Android and Windows (fixed for Android in [litehelpers / Cordova-sqlite-enterprise-free](https://github.com/litehelpers/Cordova-sqlite-enterprise-free), available under a different licensing scheme *without SQLCipher*).
- Possible crash on Android when using Unicode emoji characters due to [Android bug 81341](https://code.google.com/p/android/issues/detail?id=81341), which *should* be fixed in Android 6.x
- Close/delete database bugs described below.
- When a database is opened and deleted without closing, the iOS version is known to leak resources.
- Lawnchair adapter is *not* expected to work as described below.
- JSON1 feature is not working for Windows
- It is NOT possible to open multiple databases with the same name but in different locations (iOS version).
- Problems reported with PhoneGap Build in the past:
  - PhoneGap Build Hydration.
  - Apparently FIXED: ~~PhoneGap Build may fail to build the iOS version unless the name of the app starts with an uppercase and contains no spaces (see [litehelpers/Cordova-sqlite-storage#243](https://github.com/litehelpers/Cordova-sqlite-storage/issues/243); [Wizcorp/phonegap-facebook-plugin#830](https://github.com/Wizcorp/phonegap-facebook-plugin/issues/830); [phonegap/build#431](https://github.com/phonegap/build/issues/431)).~~

## Other limitations

- ~~The db version, display name, and size parameter values are not supported and will be ignored.~~ (No longer supported by the API)
- Absolute and relative subdirectory path(s) are not tested or supported.
- This plugin will not work before the callback for the 'deviceready' event has been fired, as described in **Usage**. (This is consistent with the other Cordova plugins.)
- This version will not work within a web worker (not properly supported by the Cordova framework). Use within a web worker is supported for Android and iOS (*without SQLCipher*) in: [litehelpers / cordova-sqlite-workers-evfree](https://github.com/litehelpers/cordova-sqlite-workers-evfree) (available with a different licensing scheme)
- In-memory database `db=window.sqlitePlugin.openDatabase({name: ':memory:', ...})` is currently not supported.
- The Android version cannot work with more than 100 open db files (due to the threading model used).
- UNICODE `\u2028` (line separator) and `\u2029` (paragraph separator) characters are currently not supported and known to be broken in iOS version due to [Cordova bug CB-9435](https://issues.apache.org/jira/browse/CB-9435). There *may* be a similar issue with certain other UNICODE characters in the iOS version (needs further investigation). This is fixed in: [litehelpers / Cordova-sqlite-enterprise-free](https://github.com/litehelpers/Cordova-sqlite-enterprise-free) (available with a different licensing scheme - *without SQLCipher*)
- Blob type is currently not supported and known to be broken on multiple platforms.
- UNICODE `\u0000` (same as `\0`) character not working in Windows version
- iOS version uses a thread pool but with only one thread working at a time due to "synchronized" database access
- Large query result can be slow, also due to JSON implementation
- ATTACH to another database file is not supported by this version. Attach/detach is supported (along with the memory and iOS UNICODE `\u2028` line separator / `\u2029` paragraph separator fixes) in: [litehelpers / Cordova-sqlite-evfree-ext](https://github.com/litehelpers/Cordova-sqlite-evfree-ext) (available with a different licensing scheme - *without SQLCipher*)
- User-defined savepoints are not supported and not expected to be compatible with the transaction locking mechanism used by this plugin. In addition, the use of BEGIN/COMMIT/ROLLBACK statements is not supported.
- Problems have been reported when using _other version(s) of_ this plugin with Crosswalk (for Android). It may help to install Crosswalk as a plugin instead of using Crosswalk to create the project.
- Does not work with [axemclion / react-native-cordova-plugin](https://github.com/axemclion/react-native-cordova-plugin) since the `window.sqlitePlugin` object exported (ES5 feature). Possible alternative solutions:
  - Follow the solution given in [this comment to axemclion/react-native-cordova-plugin#4](https://github.com/axemclion/react-native-cordova-plugin/issues/4#issuecomment-156751197) or [litehelpers/Cordova-sqlite-storage#382](https://github.com/litehelpers/Cordova-sqlite-storage/issues/382)
  - Adapt [andpor / react-native-sqlite-storage](https://github.com/andpor/react-native-sqlite-storage) to work with SQLCipher

## Further testing needed

- Integration with PhoneGap developer app
- Multi-page apps
- Use within [InAppBrowser](http://docs.phonegap.com/en/edge/cordova_inappbrowser_inappbrowser.md.html)
- Use within an iframe (see [litehelpers/Cordova-sqlite-storage#368 (comment)](https://github.com/litehelpers/Cordova-sqlite-storage/issues/368#issuecomment-154046367))
- Actual behavior when using SAVEPOINT(s)
- R-Tree is not fully tested with Android
- UNICODE characters not fully tested
- Use with TRIGGER(s), JOIN and ORDER BY RANDOM
- UPDATE/DELETE with LIMIT or ORDER BY (not supported by older sqlite3 versions)
- WITH clause (not supported by older sqlite3 versions)
- SQL statements with extra semicolon(s) in the beginning (known to cause issues with android.database implementation)
- Integration with JXCore for Cordova (must be built without sqlite(3) built-in)
- Delete an open database inside a statement or transaction callback.

## Some tips and tricks

- If you run into problems and your code follows the asynchronous HTML5/[Web SQL](http://www.w3.org/TR/webdatabase/) transaction API, you can try opening a test database using `window.openDatabase` and see if you get the same problems.
- In case your database schema may change, it is recommended to keep a table with one row and one column to keep track of your own schema version number. It is possible to add it later. The recommended schema update procedure is described below.

## Common pitfall(s)

- It is NOT allowed to execute sql statements on a transaction that has already finished, as described below. This is consistent with the HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/).
- The plugin class name starts with "SQL" in capital letters, but in Javascript the `sqlitePlugin` object name starts with "sql" in small letters.
- Attempting to open a database before receiving the 'deviceready' event callback.
- Inserting STRING into ID field
- Auto-vacuum is NOT enabled by default. It is recommended to periodically VACUUM the database.

## Weird pitfall(s)

- intent whitelist: blocked intent such as external URL intent *may* cause this and perhaps certain Cordova plugin(s) to misbehave (see [litehelpers/Cordova-sqlite-storage#396](https://github.com/litehelpers/Cordova-sqlite-storage/issues/396))

## Angular/ngCordova/Ionic-related pitfalls

- Angular/ngCordova/Ionic controller/factory/service callbacks may be triggered before the 'deviceready' event is fired
- As discussed in [litehelpers/Cordova-sqlite-storage#355](https://github.com/litehelpers/Cordova-sqlite-storage/issues/355), it may be necessary to install ionic-plugin-keyboard

## Major TODOs

- Integrate with IndexedDBShim and some other libraries such as Sequelize, Squel.js, WebSqlSync, Persistence.js, Knex, etc.
- Version with proper BLOB support

## For future considertion

- Auto-vacuum option
- Browser platform
- Windows improvements: build with OpenSSL crypto; background threading

## Alternatives

**NOTE:** None of these alternatives currently support SQLCipher.

### Other versions

- [litehelpers / Cordova-sqlite-storage](https://github.com/litehelpers/Cordova-sqlite-storage) - Cordova sqlite storage plugin without sqlcipher, using built-in SQLite library on Android/iOS
- [litehelpers / cordova-sqlite-ext](https://github.com/litehelpers/cordova-sqlite-ext) - version with [Android-sqlite-connector](https://github.com/liteglue/Android-sqlite-connector), Windows 8.1/Windows Phone 8.1/Windows 10 (using [doo / SQLite3-WinRT](https://github.com/doo/SQLite3-WinRT)), REGEXP support for Android/iOS, and pre-populated database support for Android/iOS/Windows
- [litehelpers / Cordova-sqlite-legacy](https://github.com/litehelpers/Cordova-sqlite-legacy) - maintenance of WP8 version along with the other supported platforms Android/iOS/Windows 8.1/Windows Phone 8.1/Windows 10
- [litehelpers / Cordova-sqlite-enterprise-free](https://github.com/litehelpers/Cordova-sqlite-enterprise-free) - internal memory improvements to support larger transactions (Android/iOS) and fix to support all Unicode characters (iOS) - with a different licensing scheme
- [litehelpers / Cordova-sqlite-evfree-ext](https://github.com/litehelpers/Cordova-sqlite-evfree-ext) - version with support for ATTACH, includes internal memory improvements to support larger transactions (Android/iOS) and fix to support all Unicode characters (with a different licensing scheme)
- [litehelpers / cordova-sqlite-workers-evfree](https://github.com/litehelpers/cordova-sqlite-workers-evfree) - version with support for web workers, includes internal memory improvements to support larger transactions (Android/iOS) and fix to support all Unicode characters (iOS) (with a different licensing scheme)
- Adaptation for React Native Android and iOS: [andpor / react-native-sqlite-storage](https://github.com/andpor/react-native-sqlite-storage)
- Original version for iOS (with a slightly different transaction API): [davibe / Phonegap-SQLitePlugin](https://github.com/davibe/Phonegap-SQLitePlugin)

### Other SQLite adapter projects

- [an-rahulpandey / cordova-plugin-dbcopy](https://github.com/an-rahulpandey/cordova-plugin-dbcopy) - Supports pre-populated database (*NOT* expected with work SQLCipher for Android)
- [object-layer / AnySQL](https://github.com/object-layer/anysql) - Unified SQL API over multiple database engines
- [samikrc / CordovaSQLite](https://github.com/samikrc/CordovaSQLite) - Simpler sqlite plugin with a simpler API and browser platform
- [nolanlawson / sqlite-plugin-2](https://github.com/nolanlawson/sqlite-plugin-2) - Simpler fork/partial rewrite (TBD not sure how much of the code, such as iOS code by [@davibe](https://github.com/davibe), is copied/reused)
- [nolanlawson / node-websql](https://github.com/nolanlawson/node-websql) - Web SQL API implementation for Node.js
- [an-rahulpandey / cordova-plugin-dbcopy](https://github.com/an-rahulpandey/cordova-plugin-dbcopy) - Alternative way to copy pre-populated database
- [EionRobb / phonegap-win8-sqlite](https://github.com/EionRobb/phonegap-win8-sqlite) - WebSQL add-on for Win8/Metro apps (perhaps with a different API), using an old version of the C++ library from [SQLite3-WinRT Component](https://github.com/doo/SQLite3-WinRT) (as referenced by [01org / cordova-win8](https://github.com/01org/cordova-win8))
- [SQLite3-WinRT Component](https://github.com/doo/SQLite3-WinRT) - C++ component that provides a nice SQLite API with promises for WinJS
- [01org / cordova-win8](https://github.com/01org/cordova-win8) - old, unofficial version of Cordova API support for Windows 8 Metro that includes an old version of the C++ [SQLite3-WinRT Component](https://github.com/doo/SQLite3-WinRT)
- [MSOpenTech / cordova-plugin-websql](https://github.com/MSOpenTech/cordova-plugin-websql) - Windows 8(+) and Windows Phone 8(+) WebSQL plugin versions in C#
- [Thinkwise / cordova-plugin-websql](https://github.com/Thinkwise/cordova-plugin-websql) - fork of [MSOpenTech / cordova-plugin-websql](https://github.com/MSOpenTech/cordova-plugin-websql) that supports asynchronous execution
- [MetaMemoryT / websql-client](https://github.com/MetaMemoryT/websql-client) - provides the same API and connects to [websql-server](https://github.com/MetaMemoryT/websql-server) through WebSockets.

### Alternative solutions

- [ABB-Austin / cordova-plugin-indexeddb-async](https://github.com/ABB-Austin/cordova-plugin-indexeddb-async) - Asynchronous IndexedDB plugin for Cordova that uses [axemclion / IndexedDBShim](https://github.com/axemclion/IndexedDBShim) (Browser/iOS/Android/Windows) and [Thinkwise / cordova-plugin-websql](https://github.com/Thinkwise/cordova-plugin-websql) - (Windows)
- Another sqlite binding for React-Native (iOS version): [almost/react-native-sqlite](https://github.com/almost/react-native-sqlite)
- Use [NativeScript](https://www.nativescript.org) with its web view and [NathanaelA / nativescript-sqlite](https://github.com/Natha
naelA/nativescript-sqlite) (Android and/or iOS)
- Standard HTML5 [local storage](https://en.wikipedia.org/wiki/Web_storage#localStorage)
- [Realm.io](https://realm.io/)

# Usage

## Echo test

To verify that both the Javascript and native part of this plugin are installed in your application:

```js
window.sqlitePlugin.echoTest(successCallback, errorCallback);
```

**IMPORTANT:** Please wait for the 'deviceready' event (see below for an example).

## General

The idea is to emulate the HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/) as closely as possible. The only major change is to use `window.sqlitePlugin.openDatabase()` (or `sqlitePlugin.openDatabase()`) *with parameters as documented below* instead of `window.openDatabase()`. If you see any other major change please report it, it is probably a bug.

**NOTE:** If a sqlite statement in a transaction fails with an error, the error handler *must* return `false` in order to recover the transaction. This is correct according to the HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/) standard. This is different from the WebKit implementation of Web SQL in Android and iOS which recovers the transaction if a sql error hander returns a non-`true` value.

## Opening a database

**Supported way:** To open a database access handle object (in the **new** default location):

```js
var db = window.sqlitePlugin.openDatabase({name: 'my.db', key: 'your-password-here', location: 'default'}, successcb, errorcb);
```

**WARNING:** The new "default" location value is *NOT* the same as the old default location and would break an upgrade for an app that was using the old default value (0) on iOS.

To specify a different location (affects iOS *only*):

```js
var db = window.sqlitePlugin.openDatabase({name: 'my.db', key: 'your-password-here', iosDatabaseLocation: 'Library'}, successcb, errorcb);
```

where the `iosDatabaseLocation` option may be set to one of the following choices:
- `default`: `Library/LocalDatabase` subdirectory - *NOT* visible to iTunes and *NOT* backed up by iCloud
- `Library`: `Library` subdirectory - backed up by iCloud, *NOT* visible to iTunes
- `Documents`: `Documents` subdirectory - visible to iTunes and backed up by iCloud

**WARNING:** Again, the new "default" iosDatabaseLocation value is *NOT* the same as the old default location and would break an upgrade for an app using the old default value (0) on iOS.

*ALTERNATIVE (deprecated):*
- `var db = window.sqlitePlugin.openDatabase({name: 'my.db', key: 'your-password-here', location: 1}, successcb, errorcb);`

with the `location` option set to one the following choices (affects iOS *only*):
- `0` ~~(default)~~: `Documents` - visible to iTunes and backed up by iCloud
- `1`: `Library` - backed up by iCloud, *NOT* visible to iTunes
- `2`: `Library/LocalDatabase` - *NOT* visible to iTunes and *NOT* backed up by iCloud (same as using "default")

No longer supported (see tip below to overwrite `window.openDatabase`): ~~`var db = window.sqlitePlugin.openDatabase("myDatabase.db", "1.0", "Demo", -1);`~~

**IMPORTANT:** Please wait for the 'deviceready' event, as in the following example:

```js
// Wait for Cordova to load
document.addEventListener('deviceready', onDeviceReady, false);

// Cordova is ready
function onDeviceReady() {
  var db = window.sqlitePlugin.openDatabase({name: 'my.db', key: 'your-password-here', location: 'default'});
  // ...
}
```

The successcb and errorcb callback parameters are optional but can be extremely helpful in case anything goes wrong. For example:

```js
window.sqlitePlugin.openDatabase({name: 'my.db', key: 'your-password-here', location: 'default'}, function(db) {
  db.transaction(function(tx) {
    // ...
  }, function(err) {
    console.log('Open database ERROR: ' + JSON.stringify(err));
  });
});
```

If any sql statements or transactions are attempted on a database object before the openDatabase result is known, they will be queued and will be aborted in case the database cannot be opened.

**OTHER NOTES:**
- The database file name should include the extension, if desired.
- It is possible to open multiple database access handle objects for the same database.
- The database handle access object can be closed as described below.

**TIP:**

To overwrite `window.openDatabase`:

```Javascript
window.openDatabase = function(dbname, ignored1, ignored2, ignored3) {
  return window.sqlitePlugin.openDatabase({name: dbname, location: 'default'});
};
```

## SQL transactions

The following types of SQL transactions are supported by this version:
- Single-statement transactions
- SQL batch query transactions
- Standard asynchronous transactions

### Single-statement transactions

Sample with INSERT:

```Javascript
db.executeSql('INSERT INTO MyTable VALUES (?)', ['test-value'], function (resultSet) {
  console.log('resultSet.insertId: ' + resultSet.insertId);
  console.log('resultSet.rowsAffected: ' + resultSet.rowsAffected);
}, function(error) {
  console.log('SELECT error: ' + error.message);
});
```

Sample with SELECT:

```Javascript
db.executeSql("SELECT LENGTH('tenletters') AS stringlength", [], function (resultSet) {
  console.log('got stringlength: ' + resultSet.rows.item(0).stringlength);
}, function(error) {
  console.log('SELECT error: ' + error.message);
});
```

**NOTE/minor bug:** The object returned by `resultSet.rows.item(rowNumber)` is **not** immutable. In addition, multiple calls to `resultSet.rows.item(rowNumber)` with the same `rowNumber` on the same `resultSet` object return the same object. For example, the following code will show `Second uppertext result: ANOTHER`:

```Javascript
db.executeSql("SELECT UPPER('First') AS uppertext", [], function (resultSet) {
  var obj1 = resultSet.rows.item(0);
  obj1.uppertext = 'ANOTHER';
  console.log('Second uppertext result: ' + resultSet.rows.item(0).uppertext);
  console.log('SELECT error: ' + error.message);
});
```

### SQL batch query transactions

Sample:

```Javascript
db.sqlBatch([
  'DROP TABLE IF EXISTS MyTable',
  'CREATE TABLE MyTable (SampleColumn)',
  [ 'INSERT INTO MyTable VALUES (?)', ['test-value'] ],
], function() {
  db.executeSql('SELECT * FROM MyTable', [], function (resultSet) {
    console.log('Sample column value: ' + resultSet.rows.item(0).SampleColumn);
  });
}, function(error) {
  console.log('Populate table error: ' + error.message);
});
```

In case of an error, all changes in a sql batch are automatically discarded using ROLLBACK.

### Standard asynchronous transactions

Standard asynchronous transactions follow the HTML5/[Web SQL API](http://www.w3.org/TR/webdatabase/) which is very well documented and uses BEGIN and COMMIT or ROLLBACK to keep the transactions failure-safe. Here is a simple example:

```Javascript
db.transaction(function(tx) {
  tx.executeSql('DROP TABLE IF EXISTS MyTable');
  tx.executeSql('CREATE TABLE MyTable (SampleColumn)');
  tx.executeSql('INSERT INTO MyTable VALUES (?)', ['test-value'], function(tx, resultSet) {
    console.log('resultSet.insertId: ' + resultSet.insertId);
    console.log('resultSet.rowsAffected: ' + resultSet.rowsAffected);
  }, function(tx, error) {
    console.log('INSERT error: ' + error.message);
  });
}, function(error) {
  console.log('transaction error: ' + error.message);
}, function() {
  console.log('transaction ok');
});
```

In case of a read-only transaction, it is possible to use `readTransaction` which will not use BEGIN, COMMIT, or ROLLBACK:

```Javascript
db.readTransaction(function(tx) {
  tx.executeSql("SELECT UPPER('Some US-ASCII text') AS uppertext", [], function(tx, resultSet) {
    console.log("resultSet.rows.item(0).uppertext: " + resultSet.rows.item(0).uppertext);
  }, function(error) {
    console.log('SELECT error: ' + error.message);
  });
}, function(tx, error) {
  console.log('transaction error: ' + error.message);
}, function() {
  console.log('transaction ok');
});
```

**WARNING:** It is NOT allowed to execute sql statements on a transaction after it has finished. Here is an example from the **Populating Cordova SQLite storage with the JQuery API post** at <http://www.brodybits.com/cordova/sqlite/api/jquery/2015/10/26/populating-cordova-sqlite-storage-with-the-jquery-api.html>:

```Javascript
  // BROKEN SAMPLE:
  db.executeSql("DROP TABLE IF EXISTS tt");
  db.executeSql("CREATE TABLE tt (data)");

  db.transaction(function(tx) {
    $.ajax({
      url: 'https://api.github.com/users/litehelpers/repos',
      dataType: 'json',
      success: function(res) {
        console.log('Got AJAX response: ' + JSON.stringify(res));
        $.each(res, function(i, item) {
          console.log('REPO NAME: ' + item.name);
          tx.executeSql("INSERT INTO tt values (?)", JSON.stringify(item.name));
        });
      }
    });
  }, function(e) {
    console.log('Transaction error: ' + e.message);
  }, function() {
    // Check results:
    db.executeSql('SELECT COUNT(*) FROM tt', [], function(res) {
      console.log('Check SELECT result: ' + JSON.stringify(res.rows.item(0)));
    });
  });
```

You can find more details and a step-by-step description how to do this right in the **Populating Cordova SQLite storage with the JQuery API** post at: <http://www.brodybits.com/cordova/sqlite/api/jquery/2015/10/26/populating-cordova-sqlite-storage-with-the-jquery-api.html>

**NOTE/minor bug:** Just like the single-statement transaction described above, the object returned by `resultSet.rows.item(rowNumber)` is **not** immutable. In addition, multiple calls to `resultSet.rows.item(rowNumber)` with the same `rowNumber` on the same `resultSet` object return the same object. For example, the following code will show `Second uppertext result: ANOTHER`:

```Javascript
db.readTransaction(function(tx) {
  tx.executeSql("SELECT UPPER('First') AS uppertext", [], function(tx, resultSet) {
    var obj1 = resultSet.rows.item(0);
    obj1.uppertext = 'ANOTHER';
    console.log('Second uppertext result: ' + resultSet.rows.item(0).uppertext);
    console.log('SELECT error: ' + error.message);
  });
});
```

**FUTURE TBD:** It should be possible to get a row result object using `resultSet.rows[rowNumber]`, also in case of a single-statement transaction. This is non-standard but is supported by the Chrome desktop browser.

## Background processing

The threading model depends on which version is used:
- For Android, one background thread per db;
- for iOS, background processing using a very limited thread pool (only one thread working at a time);
- for Windows, no background processing (for future consideration).

## Sample with PRAGMA feature

Creates a table, adds a single entry, then queries the count to check if the item was inserted as expected. Note that a new transaction is created in the middle of the first callback.

```js
// Wait for Cordova to load
document.addEventListener('deviceready', onDeviceReady, false);

// Cordova is ready
function onDeviceReady() {
  var db = window.sqlitePlugin.openDatabase({name: 'my.db', location: 'default'});

  db.transaction(function(tx) {
    tx.executeSql('DROP TABLE IF EXISTS test_table');
    tx.executeSql('CREATE TABLE IF NOT EXISTS test_table (id integer primary key, data text, data_num integer)');

    // demonstrate PRAGMA:
    db.executeSql("pragma table_info (test_table);", [], function(res) {
      console.log("PRAGMA res: " + JSON.stringify(res));
    });

    tx.executeSql("INSERT INTO test_table (data, data_num) VALUES (?,?)", ["test", 100], function(tx, res) {
      console.log("insertId: " + res.insertId + " -- probably 1");
      console.log("rowsAffected: " + res.rowsAffected + " -- should be 1");

      db.transaction(function(tx) {
        tx.executeSql("select count(id) as cnt from test_table;", [], function(tx, res) {
          console.log("res.rows.length: " + res.rows.length + " -- should be 1");
          console.log("res.rows.item(0).cnt: " + res.rows.item(0).cnt + " -- should be 1");
        });
      });

    }, function(e) {
      console.log("ERROR: " + e.message);
    });
  });
}
```

**NOTE:** PRAGMA statements must be executed in `executeSql()` on the database object (i.e. `db.executeSql()`) and NOT within a transaction.

## Sample with transaction-level nesting

In this case, the same transaction in the first executeSql() callback is being reused to run executeSql() again.

```js
// Wait for Cordova to load
document.addEventListener('deviceready', onDeviceReady, false);

// Cordova is ready
function onDeviceReady() {
  var db = window.sqlitePlugin.openDatabase({name: "my.db", key: "your-password-here", location: 'default'});

  db.transaction(function(tx) {
    tx.executeSql('DROP TABLE IF EXISTS test_table');
    tx.executeSql('CREATE TABLE IF NOT EXISTS test_table (id integer primary key, data text, data_num integer)');

    tx.executeSql("INSERT INTO test_table (data, data_num) VALUES (?,?)", ["test", 100], function(tx, res) {
      console.log("insertId: " + res.insertId + " -- probably 1");
      console.log("rowsAffected: " + res.rowsAffected + " -- should be 1");

      tx.executeSql("select count(id) as cnt from test_table;", [], function(tx, res) {
        console.log("res.rows.length: " + res.rows.length + " -- should be 1");
        console.log("res.rows.item(0).cnt: " + res.rows.item(0).cnt + " -- should be 1");
      });

    }, function(e) {
      console.log("ERROR: " + e.message);
    });
  });
}
```

This case will also works with Safari (WebKit) (with no encryption), assuming you replace `window.sqlitePlugin.openDatabase` with `window.openDatabase`.

## Close a database object

This will invalidate **all** handle access handle objects for the database that is closed:

```js
db.close(successcb, errorcb);
```

It is OK to close the database within a transaction callback but *NOT* within a statement callback. The following example is OK:

```Javascript
db.transaction(function(tx) {
  tx.executeSql("SELECT LENGTH('tenletters') AS stringlength", [], function(tx, res) {
    console.log('got stringlength: ' + res.rows.item(0).stringlength);
  });
}, function(error) {
  // OK to close here:
  console.log('transaction error: ' + error.message);
  db.close();
}, function() {
  // OK to close here:
  console.log('transaction ok');
  db.close(function() {
    console.log('database is closed ok');
  });
});
```

The following example is NOT OK:

```Javascript
// BROKEN:
db.transaction(function(tx) {
  tx.executeSql("SELECT LENGTH('tenletters') AS stringlength", [], function(tx, res) {
    console.log('got stringlength: ' + res.rows.item(0).stringlength);
    // BROKEN - this will trigger the error callback:
    db.close(function() {
      console.log('database is closed ok');
    }, function(error) {
      console.log('ERROR closing database');
    });
  });
});
```

**BUG:** It is currently NOT possible to close a database in a `db.executeSql` callback. For example:

```Javascript
// BROKEN DUE TO BUG:
db.executeSql("SELECT LENGTH('tenletters') AS stringlength", [], function (res) {
  var stringlength = res.rows.item(0).stringlength;
  console.log('got stringlength: ' + res.rows.item(0).stringlength);

  // BROKEN - this will trigger the error callback DUE TO BUG:
  db.close(function() {
    console.log('database is closed ok');
  }, function(error) {
    console.log('ERROR closing database');
  });
});
```

**SECOND BUG:** When a database connection is closed, any queued transactions are left hanging. All pending transactions should be errored when a database connection is closed.

**NOTE:** As described above, if multiple database access handle objects are opened for the same database and one database handle access object is closed, the database is no longer available for the other database handle objects. Possible workarounds:
- It is still possible to open one or more new database handle objects on a database that has been closed.
- It *should* be OK not to explicitly close a database handle since database transactions are [ACID](https://en.wikipedia.org/wiki/ACID) compliant and the app's memory resources are cleaned up by the system upon termination.

**FUTURE TBD:** `dispose` method on the database access handle object, such that a database is closed once **all** access handle objects are disposed.

## Delete a database

```js
window.sqlitePlugin.deleteDatabase({name: 'my.db', location: 'default'}, successcb, errorcb);
```

with `location` or `iosDatabaseLocation` parameter *required* as described above for `openDatabase` (affects iOS *only*)

**BUG:** When a database is deleted, any queued transactions for that database are left hanging. All pending transactions should be errored when a database is deleted.

## Self Healing Mechanism

The self-healing mechanism is a mechanism that helps to recover mobile apps that are unable to open the ciphered local database. One known scenario when this can happen is when the mobile app's endpoint changes between versions.

The self-heal mechanism consists in deleting the previous encrypted local database and to create a new one. So, instead of clients having errors in their apps, they will only lose the local configurations of the apps and no error screen will be shown. 

By default, this mechanism is disabled, but if you wish to use it you should use the following syntax in `config.xml`:
```
  <preference name="EnableSQLCipherSelfHealing" value="true" />
```

# Database schema versions

The transactional nature of the API makes it relatively straightforward to manage a database schema that may be upgraded over time (adding new columns or new tables, for example). Here is the recommended procedure to follow upon app startup:
- Check your database schema version number (you can use `db.executeSql` since it should be a very simple query)
- If your database needs to be upgraded, do the following *within a single transaction* to be failure-safe:
  - Create your database schema version table (single row single column) if it does not exist (you can check the `sqlite_master` table as described at: http://stackoverflow.com/questions/1601151/how-do-i-check-in-sqlite-whether-a-table-exists)
  - Add any missing columns and tables, and apply any other changes necessary

**IMPORTANT:** Since we cannot be certain when the users will actually update their apps, old schema versions will have to be supported for a very long time.

## Use with Ionic/ngCordova/Angular

It is recommended to follow the tutorial at: https://blog.nraboy.com/2014/11/use-sqlite-instead-local-storage-ionic-framework/

Documentation at: http://ngcordova.com/docs/plugins/sqlite/

# Installing

## Easy install with Cordova CLI tool

    npm install -g cordova # (in case you don't have cordova)
    cordova create MyProjectFolder com.my.project MyProject && cd MyProjectFolder # if you are just starting
    cordova plugin add https://github.com/litehelpers/Cordova-sqlcipher-adapter

**CLI NOTES:**

- You *may* have to update the platform and plugin version(s) before you can build: `cordova prepare` (or for a specific platform such as iOS: `cordova prepare ios`)
- If you cannot build for a platform after `cordova prepare`, you may have to remove the platform and add it again, such as:

```shell
cordova platform rm ios
cordova platform add ios
```

You can find some more details in a nice writeup (though with old links and package names): <http://iphonedevlog.wordpress.com/2014/04/07/installing-chris-brodys-sqlite-database-with-cordova-cli-android/>.

## Easy install with plugman tool

```shell
plugman install --platform MYPLATFORM --project path.to.my.project.folder --plugin https://github.com/litehelpers/Cordova-sqlcipher-adapter
```

where MYPLATFORM is `android`, `ios`, or `windows`.

A posting how to get started developing on Windows host without the Cordova CLI tool (for Android target only) is available [here](http://brodybits.blogspot.com/2015/03/trying-cordova-for-android-on-windows-without-cordova-cli.html).

## Plugin installation sources

- https://github.com/litehelpers/Cordova-sqlcipher-adapter - latest version
- TBD: ~~`cordova-sqlite-xxx` - stable npm package version~~

## Source tree

- `SQLitePlugin.coffee.md`: platform-independent (Literate coffee-script, can be read by recent coffee-script compiler)
- `www`: `SQLitePlugin.js` platform-independent Javascript as generated from `SQLitePlugin.coffee.md` (and checked in!)
- `src`: platform-specific source code:
   - `common` - sqlcipher version of `sqlite3.[hc]` to be built for iOS and Windows ~~Universal (8.1)~~ platforms
   - `external` - placeholder - *not used in this branch*
   - `android` - Java plugin code for Android
   - `ios` - Objective-C plugin code for iOS
   - `windows` - Javascript proxy code and SQLite3-WinRT project for Windows
- `spec`: test suite using Jasmine (2.2.0)
- `tests`: very simple Jasmine test suite that is run on Circle CI (Android version) and Travis CI (iOS version) (used as a placeholder)
- `Lawnchair-adapter`: Lawnchair adaptor, based on the version from the Lawnchair repository, with the basic Lawnchair test suite in `test-www` subdirectory

## Installation test

### Easy installation test

Use `window.sqlitePlugin.echoTest` as described above (please wait for the `deviceready` event).

### Quick installation test

Assuming your app has a recent template as used by the Cordova create script, add the following code to the `onDeviceReady` function, after `app.receivedEvent('deviceready');`:

```Javascript
  window.sqlitePlugin.openDatabase({ name: 'hello-world.db', location: 'default' }, function (db) {
    db.executeSql("select length('tenletters') as stringlength", [], function (res) {
      var stringlength = res.rows.item(0).stringlength;
      console.log('got stringlength: ' + stringlength);
      document.getElementById('deviceready').querySelector('.received').innerHTML = 'stringlength: ' + stringlength;
   });
  });
```

# Support

## Policy

Free support is provided on a best-effort basis and is only available in public forums. Please follow the steps below to be sure you have done your best before requesting help.

Commercial support is available by contacting: <sales@litehelpers.net>

## Before asking for help

First steps:
- Verify that you have followed the steps in [brodybits / Cordova-quick-start-checklist](https://github.com/brodybits/Cordova-quick-start-checklist)
- Try the new echo test as described above
- Check the troubleshooting steps and pitfalls in [brodybits / Cordova-troubleshooting-guide](https://github.com/brodybits/Cordova-troubleshooting-guide)

and check the following:
- You are using the latest version of the Plugin (Javascript and platform-specific part) from this repository.
- The plugin is installed correctly.
- You have included the correct version of `cordova.js`.
- You have registered the plugin properly in `config.xml`.

If you still cannot get something to work:
- create a fresh, clean Cordova project;
- add this plugin according to the instructions above;
- double-check that you follwed the steps in [brodybits / Cordova-quick-start-checklist](https://github.com/brodybits/Cordova-quick-start-checklist);
- try a simple test program;
- double-check the troubleshooting steps and pitfalls in [brodybits / Cordova-troubleshooting-guide](https://github.com/brodybits/Cordova-troubleshooting-guide)

If you continue to see the issue in the fresh, clean Cordova project:
- Make the simplest test program you can to demonstrate the issue, including the following characteristics:
  - it completely self-contained, i.e. it is using no extra libraries beyond cordova & SQLitePlugin.js;
  - if the issue is with *adding* data to a table, that the test program includes the statements you used to open the database and create the table;
  - if the issue is with *retrieving* data from a table, that the test program includes the statements you used to open the database, create the table, and enter the data you are trying to retrieve.

## What will be supported for free

Please make a small, self-contained test program that can demonstrate your problem and post it. Please do not use any other plugins or frameworks than are absolutely necessary to demonstrate your problem.

In case of a problem with a pre-populated database, please post your entire project.

## Support for issues with Angular/"ngCordova"/Ionic

Free support for issues with Angular/"ngCordova"/Ionic will only be provided if you can demonstrate that you can do the same thing without such a framework.
- Make a fresh, clean ngCordova or Ionic project with a test program that demonstrates the issue and post it. Please do not use any other plugins or frameworks unless absolutely necessary to demonstrate your issue.
- Make another project without any form of Angular including ngCordova or Ionic, with the same test program to show that it will work outside Angular/"ngCordova"/Ionic.

## What information is needed for help

Please include the following:
- Which platform(s) Android/iOS/Windows 8.1/Windows Phone 8.1/Windows 10
- Clear description of the issue
- A small, complete, self-contained program that demonstrates the problem, preferably as a Github project. ZIP/TGZ/BZ2 archive available from a public link is OK. No RAR or other such formats please!
- A Cordova project is highly preferred. Intel, MS IDE, or similar project formats should be avoided.

## Please do NOT use any of these formats

- screen casts or videos
- RAR or similar archive formats
- Intel, MS IDE, or similar project formats unless absolutely necessary

## Where to ask for help

Once you have followed the directions above, you may request free support in the following location(s):
- [litehelpers / Cordova-sqlcipher-adapter / issues](https://github.com/litehelpers/Cordova-sqlcipher-adapter/issues)
- [litehelpers / Cordova-sqlite-help](https://github.com/litehelpers/Cordova-sqlite-help)

Please include the information described above otherwise.

## Professional support

Professional support is available, please contact: <sales@litehelpers.net>

# Unit tests

Unit testing is done in `spec`.

## running tests from shell

**TBD** `test.sh` ~~not~~ tested with sqlcipher version of this plugin: *does not auto-remove correct plugin id*

To run the tests from \*nix shell, simply do either:

    ./bin/test.sh ios

or for Android:

    ./bin/test.sh android

To run from a windows powershell (here is a sample for android target):

    .\bin\test.ps1 android

# Adapters

## PouchDB

The adapter is part of [PouchDB](http://pouchdb.com/) as documented at:
- <https://pouchdb.com/api.html#create_database> (with `key` option)
- <https://pouchdb.com/adapters.html>
- <http://pouchdb.com/faq.html>

## Lawnchair adapter

**BROKEN:** The Lawnchair adapter does not support the `openDatabase` options such as `key`, `location` or `iosDatabaseLocation` options and is therefore *not* expected to work with this plugin.

### Common adapter

Please look at the `Lawnchair-adapter` tree that contains a common adapter, which should also work with the Android version, along with a test-www directory.

### Included files

Include the following Javascript files in your HTML:

- `cordova.js` (don't forget!)
- `lawnchair.js` (you provide)
- `SQLitePlugin.js` (in case of Cordova pre-3.0)
- `Lawnchair-sqlitePlugin.js` (must come after `SQLitePlugin.js` in case of Cordova pre-3.0)

### Sample

The `name` option determines the sqlite database filename, *with no extension automatically added*. Optionally, you can change the db filename using the `db` option.

In this example, you would be using/creating a database with filename `kvstore`:

```Javascript
kvstore = new Lawnchair({name: "kvstore"}, function() {
  // do stuff
);
```

Using the `db` option you can specify the filename with the desired extension and be able to create multiple stores in the same database file. (There will be one table per store.)

```Javascript
recipes = new Lawnchair({db: "cookbook", name: "recipes", ...}, myCallback());
ingredients = new Lawnchair({db: "cookbook", name: "ingredients", ...}, myCallback());
```

**KNOWN ISSUE:** the new db options are *not* supported by the Lawnchair adapter. The workaround is to first open the database file using `sqlitePlugin.openDatabase()`.

# Contributing

## Community

- Testimonials of apps that are using this plugin would be especially helpful.
- Reporting issues at [litehelpers / Cordova-sqlcipher-adapter / issues](https://github.com/litehelpers/Cordova-sqlcipher-adapter/issues) can help improve the quality of this plugin.

## Code

**WARNING:** Please do NOT propose changes from your default branch. Contributions may be rebased using `git rebase` or `git cherry-pick` and not merged.

- Patches with bug fixes are helpful, especially when submitted with test code.
- Other enhancements welcome for consideration, when submitted with test code and are working for all supported platforms. Increase of complexity should be avoided.
- All contributions may be reused by [@brodybits](https://github.com/brodybits) under another license in the future. Efforts will be taken to give credit for major contributions but it will not be guaranteed.
- Project restructuring, i.e. moving files and/or directories around, should be avoided if possible.
- If you see a need for restructuring, it is better to first discuss it in new issue where alternatives can be discussed before reaching a conclusion. If you want to propose a change to the project structure:
  - Remember to make (and use) a special branch within your fork from which you can send the proposed restructuring;
  - Always use `git mv` to move files & directories;
  - Never mix a move/rename operation with any other changes in the same commit.

# Contact

<sales@litehelpers.net>
