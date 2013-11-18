c3p0-play
=========

[Play Framework](http://www.playframework.com/) integrates the excellent [Bone CP](http://jolbox.com/) as its
default JDBC Connection pool. However, some users or applications may prefer to use other Connection pools for
a variety of reasons. This plug-in enables the use of [c3p0](http://www.mchange.com/projects/c3p0/) as an
effortless first-class alternative to Play's built-in pool.

### Quickstart ###

+ Configure your DataSources in the [ordinary Play way](http://www.playframework.com/documentation/2.2.x/SettingsJDBC).
+ Add the c3p0-play plug-in to your Play project's build.sbt as a libraryDependency:

```
libraryDependencies ++= Seq(
  "com.mchange" %%  "c3p0-play"  % "0.1.0-SNAPSHOT",
  "postgresql"  %   "postgresql" % "9.1-901-1.jdbc4"
)
```

+ Disable the default pool and enable c3p0 in your Play application's `conf/application.conf` file.

```
dbplugin=disabled
c3p0.play.enabled=true
```

+ Congratulations! You're app will now use c3p0. Please see below for configuration detals.

### Basic Configuration ###

The c3p0-play plugin is designed to make it extremely easy to "drop-in" c3p0, to drop-back to the
default BoneCP pool, or to mix the two pools. To enable that...

1. c3p0-play reads Play's default DataSource configuration and translates it to c3p0 configuration. Some (not so common) BoneCP configuration properties do not easily map to c3p0. Those will be ignored with a warning in the logs upon initiatlization. (They are also documented below.)
2. [_All_ c3p0 configuration](http://www.mchange.com/projects/c3p0/#configuration_properties) can be embedded directly in your `conf/application.conf` file. c3p0-native configuration will take precedence over play-style configuration for c3p0 pools.

For example, the following configuration infomration...

```
dbplugin=disabled
c3p0.play.enabled=true

db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
db.default.user=sa
db.default.password=secret

db.default.acquireIncrement=1
db.default.testConnectionOnCheckout=true

c3p0.acquireIncrement=5
c3p0.minPoolSize=10
c3p0.maxPoolSize=30
```

would configure a c3p0 DataSource named `default` using the driver, url, and authentication information given.

+ `acquireIncrement` (a configuration parameter shared by c3p0 and BoneCP) is configured twice, once in Play-typical format and once in c3p0-native format.
The value in c3p0-native format overrides and is used in preference to the value in Play format when both are present.
This usefully allows users to maintain distinct configrations for BoneCP and c3p0 while users profile and experiment with the two libraries.

+ `testConnectionOnCheckout` is a c3p0 parameter. It is translated from Play format and used as-is, since there is no c3p0-native value to override it.

+ `minPoolSize` and `maxPoolSize` are included in c3p0-native and conflict with nothing. Unsurprisingly, those values will be used by c3p0 DataSources.

 
