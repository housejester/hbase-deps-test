This example is trying to build a java project that depends on the hbase-1.1.0 RC2.

- Adds repository: https://repository.apache.org/content/repositories/orgapachehbase-1078
- Adds dependency on org.apache.hbase:hbase-server:1.1.0
- Single java file that is an empty class.

Running the build with:

```
gradle -Dcompat.module=hbase-hadoop2-compat build
```

The build fails with:

```
FAILURE: Build failed with an exception.

* What went wrong:
Could not resolve all dependencies for configuration ':orc-hbase:compile'.
> Could not resolve org.apache.hbase:hbase-server:1.1.0.
  Required by:
      io.orchestrate:orc-hbase:unspecified
   > java.lang.NullPointerException (no error message)
   > Could not parse POM https://repository.apache.org/content/repositories/orgapachehbase-1076/org/apache/hbase/hbase-server/1.1.0/hbase-server-1.1.0.pom
      > Unable to resolve version for dependency 'org.apache.hadoop:hadoop-minikdc:jar'
```

This is even after deleting all my local gradle (an m2) caches. I see 1.1.0 deps coming down.

If you change the build.gradle to depend on 1.0.0, the build will work.

Interestingly, even with 1.0.0, I have to add the `-Dcompat.module=hbase-hadoop2-compat` to the build otherwise it fails with:

```
FAILURE: Build failed with an exception.

* What went wrong:
Could not resolve all dependencies for configuration ':compile'.
> Could not resolve org.apache.hbase:${compat.module}:1.0.0.
  Required by:
      :test-deps:unspecified > org.apache.hbase:hbase-server:1.0.0
      :test-deps:unspecified > org.apache.hbase:hbase-server:1.0.0 > org.apache.hbase:hbase-prefix-tree:1.0.0
   > Illegal character in path at index 89: https://repository.apache.org/content/repositories/orgapachehbase-1078/org/apache/hbase/${compat.module}/1.0.0/${compat.module}-1.0.0.pom
   > Illegal character in path at index 48: http://repo1.maven.org/maven2/org/apache/hbase/${compat.module}/1.0.0/${compat.module}-1.0.0.pom
```

I believe the issue with the 1.1.0 build is also related to the ${compat.module} issue in that it isn't resolvable in the parent pom.

I'm not sure if the issue with 1.1.0 is similar to what happened before with https://issues.apache.org/jira/browse/HBASE-8488 ? Or am I missing something in how it is built?
