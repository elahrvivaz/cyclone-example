# cyclone-maven-plugin-example for issue [#436](https://github.com/CycloneDX/cyclonedx-maven-plugin/issues/436)

## First Analysis

`pom.xml` defines `osgeo` remote repository, where `org.geotools` groupId dependencies can be downloaded from.

Build of the project works perfectly, dependencies are downloaded between `central` and `osgeo` as expected, and packaging happens, successfully compiling and creating the `jar`.

But after packaging, when `cyclonedx:makeBom` executes, there are WARNINGs in the output for `osgeo` dependencies with a pattern:

```
[INFO] Artifact org.geotools:gt-main:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[INFO] Artifact org.geotools:gt-main:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[WARNING] Unable to create Maven project for org.geotools:gt-main:jar:32.1 from repository.
```

= `[INFO] Artifact org.geotools:...:pom:... is present in the local repository, but cached from a remote repository that is unavailable in current build context`

followed by `[WARNING] Unable to create Maven project for org.geotools:...:jar:... from repository.`

Messages are mixing INFO on `pom` artifact from local repository and WARNING on "creating a Maven project" for corresponding `jar`.

Notice that the generated CycloneDX documents `target/bom.json`/`.xml` contain the description of the `org.osgeo` dependencies, that seem well described: impact seems limited to noise in the build output.

## Full Build Output

```
$ mvn verify
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< org.elahrvivaz.test:cyclone-test >------------------
[INFO] Building Cyclone Test 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ cyclone-test ---
[INFO] skip non existing resourceDirectory /home/herve/tmp/cyclone-example/src/main/resources
[INFO] 
[INFO] --- compiler:3.14.0:compile (default-compile) @ cyclone-test ---
[INFO] Nothing to compile - all classes are up to date.
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ cyclone-test ---
[INFO] skip non existing resourceDirectory /home/herve/tmp/cyclone-example/src/test/resources
[INFO] 
[INFO] --- compiler:3.14.0:testCompile (default-testCompile) @ cyclone-test ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.5.2:test (default-test) @ cyclone-test ---
[INFO] No tests to run.
[INFO] 
[INFO] --- jar:3.4.2:jar (default-jar) @ cyclone-test ---
[INFO] 
[INFO] --- cyclonedx:2.9.1:makeBom (makeBom) @ cyclone-test ---
[INFO] CycloneDX: Resolving Dependencies
[INFO] Artifact org.geotools:gt-main:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[INFO] Artifact org.geotools:gt-main:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[WARNING] Unable to create Maven project for org.geotools:gt-main:jar:32.1 from repository.
[INFO] Artifact org.geotools:gt-referencing:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[INFO] Artifact org.geotools:gt-referencing:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases)]
[WARNING] Unable to create Maven project for org.geotools:gt-referencing:jar:32.1 from repository.
...
[INFO] Artifact org.geotools:gt-http:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases), ossrh-snapshot (https://oss.sonatype.org/content/repositories/snapshots/, default, releases+snapshots), jcenter (https://jcenter.bintray.com, default, releases+snapshots), bintray-release (https://oss.jfrog.org/artifactory/libs-release, default, releases), bintray-snapshot (https://oss.jfrog.org/artifactory/libs-snapshot, default, releases+snapshots), maven-default-http-blocker (http://0.0.0.0/, default, snapshots, blocked)]
[INFO] Artifact org.geotools:gt-http:pom:32.1 is present in the local repository, but cached from a remote repository ID that is unavailable in current build context, verifying that is downloadable from [central (https://repo.maven.apache.org/maven2, default, releases), ossrh-snapshot (https://oss.sonatype.org/content/repositories/snapshots/, default, releases+snapshots), jcenter (https://jcenter.bintray.com, default, releases+snapshots), bintray-release (https://oss.jfrog.org/artifactory/libs-release, default, releases), bintray-snapshot (https://oss.jfrog.org/artifactory/libs-snapshot, default, releases+snapshots), maven-default-http-blocker (http://0.0.0.0/, default, snapshots, blocked)]
[WARNING] Unable to create Maven project for org.geotools:gt-http:jar:32.1 from repository.
[INFO] CycloneDX: Creating BOM version 1.4 with 32 component(s)
[INFO] CycloneDX: Writing and validating BOM (XML): /home/herve/tmp/cyclone-example/target/bom.xml
[INFO]            attaching as cyclone-test-0.0.1-SNAPSHOT-cyclonedx.xml
[INFO] CycloneDX: Writing and validating BOM (JSON): /home/herve/tmp/cyclone-example/target/bom.json
[INFO]            attaching as cyclone-test-0.0.1-SNAPSHOT-cyclonedx.json
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.173 s
[INFO] Finished at: 2025-03-22T01:29:36+01:00
[INFO] ------------------------------------------------------------------------
```
