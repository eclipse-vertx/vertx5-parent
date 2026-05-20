## Base parent pom for Vert.x 5 projects

The parent pom defines common default configuration for Vert.x 5 projects:

- Base plugins versions
- File encoding
- Java 11 release
- Install test-jar, sources, test-sources and docs artifacts
- Vert.x release/snapshots repositories
- Asciidoc source dir
- Asciidoc target dir
- Generated main sources dir
- Surefire configuration
  - Run tests with module path
  - Netty leak detector

This parent pom provides common default configuration for data object generation and documentation.

### Code generation

The Java compiler configures the execution of the Vert.x codegen and docgen processors but does not declare their
execution, those processors must be configured explicitly by the pom.

```xml
  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <executions>
            <execution>
              <id>default-compile</id>
              <configuration>
                <annotationProcessors>
                  <annotationProcessor>io.vertx.codegen.CodeGenProcessor</annotationProcessor>
                </annotationProcessors>
              </configuration>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
```

### Spotless support

Provides Spotless support with the Spotless Maven Plugin.

This parent provides a default plugin configuration that checks

- unused imports
- trailing whitespaces
- end with newlines

The plugin can be invoked manually to check or make the source code compliant

```shell
> mvn spotless:check
> mvn spotless:apply
```

This parent provides a profile named `checkstyle` that runs the `check` goal bound to the `verify` phase, usually this profile is used by the CI to ensure contributions do not break the spotless invariants.

### Asciidoc

Asciidoc is generated from `src/main/asciidoc` in the `target/asciidoc/java` directory in _Asciidoc_ format at compilation
time and can be previewed using the Chrome _Asciidoctor.js_ plugin.

```xml
  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <executions>
            <execution>
              <id>default-compile</id>
              <configuration>
                <annotationProcessors>
                  <annotationProcessor>io.vertx.docgen.JavaDocGenProcessor</annotationProcessor>
                </annotationProcessors>
              </configuration>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
```

Documentation can be generated with `mvn asciidoctor:process-asciidoc` or `mvn asciidoctor:auto-refresh`, the _Asciidoc_ files are transformed to HTML the _target/docs_ directory.

By default, the generated HTML uses a left TOC with 2 levels show, you can override the number of levels shown with the `vertx.asciidoc.attributes.toclevels` property: `mvn asciidoctor:process-asciidoc -Dvertx.asciidoc.attributes.toclevels=5`

This documentation shall be packaged in a `-docs.zip` file.

```xml
  <build>
    <plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
          <executions>
          <execution>
            <id>package-docs</id>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugin>
  </build>
```
