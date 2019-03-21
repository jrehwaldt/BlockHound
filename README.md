# BlockHound (experimental)

[![Travis CI](https://travis-ci.org/reactor/BlockHound.svg?branch=master)](https://travis-ci.org/reactor/BlockHound)
[![](https://img.shields.io/badge/dynamic/xml.svg?label=Milestone&color=blue&query=%2F%2Fmetadata%2Fversion&url=https%3A%2F%2Frepo.spring.io%2Fmilestone%2Fio%2Fprojectreactor%2Ftools%2Fblockhound%2Fmaven-metadata.xml)](https://repo.spring.io/milestone/io/projectreactor/tools/blockhound/)
[![](https://img.shields.io/badge/dynamic/xml.svg?label=Snapshot&color=orange&query=%2F%2Fmetadata%2Fversion&url=https%3A%2F%2Frepo.spring.io%2Fsnapshot%2Fio%2Fprojectreactor%2Ftools%2Fblockhound%2Fmaven-metadata.xml)](https://repo.spring.io/snapshot/io/projectreactor/tools/blockhound/)

Java agent to detect blocking calls from non-blocking threads.

## How it works
BlockHound will transparently instrument the JVM classes and intercept blocking calls (e.g. IO) if they are performed from threads marked as "non-blocking operations only" (ie. threads implementing Reactor's `NonBlocking` marker interface, like those started by `Schedulers.parallel()`). If and when this happens (but remember, this should never happen!:stuck_out_tongue_winking_eye:), an error will be thrown. Here is an example:
```java
// Example.java
BlockHound.install();

Mono.delay(Duration.ofSeconds(1))
    .doOnNext(it -> {
        try {
            Thread.sleep(10);
        }
        catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    })
    .block();
```

Will result in:
```
java.lang.Error: Blocking call! java.lang.Thread.sleep
	at java.base/java.lang.Thread.sleep(Native Method)
	at com.example.Example.lambda$exampleTest$0(Example.java:16)
```
Note that it points to the exact place where the blocking call got triggered. In this example it was `Example.java:16`.

## Getting it

Download it from Maven Central repositories (stable releases only) or repo.spring.io:

```groovy
repositories {
  maven { url 'https://repo.spring.io/milestone' }
  // maven { url 'https://repo.spring.io/snapshot' }
}

dependencies {
  testCompile 'io.projectreactor.tools:blockhound:$LATEST_RELEASE'
  // testCompile 'io.projectreactor.tools:blockhound:$LATEST_SNAPSHOT'
}
```
Where:

|||
|-|-|
|`$LATEST_RELEASE`|[![](https://img.shields.io/badge/dynamic/xml.svg?label=&color=blue&query=%2F%2Fmetadata%2Fversion&url=https%3A%2F%2Frepo.spring.io%2Fmilestone%2Fio%2Fprojectreactor%2Ftools%2Fblockhound%2Fmaven-metadata.xml)](https://repo.spring.io/milestone/io/projectreactor/tools/blockhound/)|
|`$LATEST_SNAPSHOT`|[![](https://img.shields.io/badge/dynamic/xml.svg?label=&color=orange&query=%2F%2Fmetadata%2Fversion&url=https%3A%2F%2Frepo.spring.io%2Fsnapshot%2Fio%2Fprojectreactor%2Ftools%2Fblockhound%2Fmaven-metadata.xml)](https://repo.spring.io/snapshot/io/projectreactor/tools/blockhound/)|

# Quick Start
See [the docs](./docs/README.md).

-------------------------------------
_Licensed under [Apache Software License 2.0](www.apache.org/licenses/LICENSE-2.0)_

_Sponsored by [Pivotal](https://pivotal.io)_
