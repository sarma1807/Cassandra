## Apache Cassandra nodetool stops working after Java upgrade to 8u331 (1.8.0_332)

### Originally reported on 26-April-2022

## I have recently upgraded Java from version 1.8.0_322 to 1.8.0_332 and immdiately my Apache Cassandra nodetool stopped working

##### JIRA : https://issues.apache.org/jira/browse/CASSANDRA-17581

### I cannot upgrade to latest version of Apache Cassandra or I cannot downgrade my Java to previous version

### Following is a quick temporary fix for this issue

```
$ java -version
openjdk version "1.8.0_332"
OpenJDK Runtime Environment (build 1.8.0_332-b09)
OpenJDK 64-Bit Server VM (build 25.332-b09, mixed mode)
$
```

## Expected output
```
$ nodetool version
ReleaseVersion: 3.11.11
$
```

## Unexpected ERROR
```
$ nodetool version
nodetool: Failed to connect to '127.0.0.1:7199' - URISyntaxException: 'Malformed IPv6 address at index 7: rmi://[127.0.0.1]:7199'.
$
```

## Backup nodetool
```
$ which nodetool
~/cassandra/bin/nodetool

$ file ~/cassandra/bin/nodetool
~/cassandra/bin/nodetool: POSIX shell script, ASCII text executable

cp ~/cassandra/bin/nodetool ~/cassandra/bin/nodetool_original_`date +'%Y%m%d-%H%M%S'`
```

## Original nodetool JVM_ARGS
```
$ cat ~/cassandra/bin/nodetool_original_* | grep -n JVM_ARGS
70:JVM_ARGS=""
92:      JVM_ARGS="$JVM_ARGS -Dssl.enable=true $SSL_ARGS"
95:      JVM_ARGS="$JVM_ARGS $1"
113:        $JVM_ARGS \
$
```

## MODIFIED nodetool JVM_ARGS
```
$ cat ~/cassandra/bin/nodetool | grep -n JVM_ARGS
70:# JVM_ARGS=""
71:JVM_ARGS="-Dcom.sun.jndi.rmiURLParsing=legacy"
93:      JVM_ARGS="$JVM_ARGS -Dssl.enable=true $SSL_ARGS"
96:      JVM_ARGS="$JVM_ARGS $1"
114:        $JVM_ARGS \
$
```

## Notice that :
### I have commented out the original JVM_ARGS="" line which is on line # 70
### I have added a new entry on line # 71 [JVM_ARGS="-Dcom.sun.jndi.rmiURLParsing=legacy"]

## Apache Cassandra nodetool is working again


#### tested and confirmed with both Java 1.8.0_322 & Java 1.8.0_332
