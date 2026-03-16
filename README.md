# SAP JCo Maven Installer GitHub Action

A GitHub Action to install SAP Java Connector (JCo) JAR files to the local Maven repository. This action simplifies the installation of proprietary SAP JCo libraries into your Maven build environment on GitHub Actions runners.

## ✨ Features

- ✅ **Flexible JAR Path Support**: Accepts absolute or relative paths to JCo JAR files
- ✅ **Automatic Path Resolution**: Automatically resolves relative paths to workspace
- ✅ **JAR Validation**: Validates JAR files before installation
- ✅ **Custom Maven Coordinates**: Supports custom groupId, artifactId, version, and classifier
- ✅ **POM File Support**: Can use existing POM files or auto-generate them
- ✅ **Installation Verification**: Verifies successful installation to Maven repository
- ✅ **Cross-Platform**: Works on Linux, Windows, and macOS runners
- ✅ **Detailed Logging**: Provides clear output and error messages
- ✅ **Step Summary**: Generates comprehensive installation summary in GitHub Actions UI
- ✅ **Multiple Outputs**: Exports installation details for downstream steps
- ✅ **Error Handling**: Configurable missing file handling strategy
- ✅ **Compatibility**: Supports Maven 3.x and Java 8+

## 🚀 Quick Start

### Basic Usage

```yaml
name: Build with SAP JCo

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Java
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '11'
        cache: 'maven'
    
    - name: Install SAP JCo
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'vendor/sapjco3.jar'
        jco-version: '3.1.0'
    
    - name: Build project
      run: mvn clean compile
```

## 📦 Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `jco-jar-path` | Path to SAP JCo JAR file (absolute or relative to workspace) | ✅ Yes | - |
| `jco-version` | SAP JCo version (e.g., 3.1.0, 3.0.20) | ❌ No | `3.1.0` |
| `jco-group-id` | Maven groupId | ❌ No | `com.sap.conn.jco` |
| `jco-artifact-id` | Maven artifactId | ❌ No | `sapjco3` |
| `jco-packaging` | Maven packaging type | ❌ No | `jar` |
| `jco-classifier` | Optional classifier (e.g., linux-x86_64, ntamd64) | ❌ No | - |
| `jco-pom-path` | Optional path to POM file | ❌ No | - |
| `fail-if-missing` | Fail if JAR file is not found | ❌ No | `true` |
| `maven-home` | Custom Maven home directory | ❌ No | - |
| `skip-verification` | Skip installation verification step | ❌ No | `false` |

## 📤 Outputs

| Output | Description | Example |
|--------|-------------|---------|
| `maven-repo-path` | Path to Maven local repository | `/home/runner/.m2/repository` |
| `installation-verified` | Whether SAP JCo was successfully installed | `true` |
| `installed-coordinates` | Maven coordinates of installed artifact | `com.sap.conn.jco:sapjco3:3.1.0:jar` |
| `jar-file-info` | JAR file information (JSON) | `{"path":"/path/to/jar","size":1234567,...}` |
| `installation-timestamp` | Timestamp of installation | `2024-01-15T10:30:00Z` |

## 🎯 Usage Examples

### Basic Installation

```yaml
- name: Install SAP JCo
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    jco-version: '3.1.0'
```

### With Platform-Specific Classifier

```yaml
- name: Install SAP JCo with classifier
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3-linux-x86_64.jar'
    jco-version: '3.1.0'
    jco-classifier: 'linux-x86_64'
```

### With Custom POM File

```yaml
- name: Install SAP JCo with POM
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    jco-pom-path: 'vendor/sapjco3.pom'
    jco-version: '3.1.0'
```

### Skip on Missing File

```yaml
- name: Install SAP JCo (skip if missing)
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    fail-if-missing: false
```

### Custom Maven Coordinates

```yaml
- name: Install with custom coordinates
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    jco-version: '3.1.0'
    jco-group-id: 'com.company.sap'
    jco-artifact-id: 'sap-connector'
```

## 🔧 Advanced Usage

### Multi-Platform Build Matrix

```yaml
name: Multi-Platform Build

on: [push]

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        jco-version: ['3.1.0', '3.0.20']
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Java
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '11'
    
    - name: Install SAP JCo
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'vendor/sapjco3-${{ matrix.jco-version }}.jar'
        jco-version: ${{ matrix.jco-version }}
    
    - name: Run tests
      run: mvn test
```

### Using Outputs in Workflow

```yaml
- name: Install SAP JCo
  id: install-jco
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    jco-version: '3.1.0'

- name: Use Installation Results
  run: |
    echo "Maven repository: ${{ steps.install-jco.outputs.maven-repo-path }}"
    echo "Maven coordinates: ${{ steps.install-jco.outputs.installed-coordinates }}"
    echo "Installation verified: ${{ steps.install-jco.outputs.installation-verified }}"
    
    # Access JAR file information
    JAR_INFO='${{ steps.install-jco.outputs.jar-file-info }}'
    echo "JAR file size: $(echo $JAR_INFO | jq -r '.size / 1024 / 1024') MB"
```

### Complete CI/CD Pipeline

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
    
    - name: Setup Java
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '11'
        cache: 'maven'
    
    - name: Install SAP JCo
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'lib/sapjco3-3.1.0.jar'
        jco-version: '3.1.0'
    
    - name: Run unit tests
      run: mvn test
    
    - name: Build package
      run: mvn package -DskipTests
      
  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
    
    - name: Setup Java
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '11'
    
    - name: Install SAP JCo
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'lib/sapjco3-3.1.0.jar'
        jco-version: '3.1.0'
    
    - name: Deploy to repository
      run: mvn deploy -DskipTests
      env:
        MAVEN_USERNAME: ${{ secrets.MAVEN_USERNAME }}
        MAVEN_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
```

## 📁 Project Structure

### Recommended Directory Layout

```
your-project/
├── .github/
│   └── workflows/
│       └── ci.yml
├── vendor/
│   ├── sapjco3-3.1.0.jar
│   ├── sapjco3-3.0.20.jar
│   ├── sapjco3-linux-x86_64.jar
│   ├── sapjco3-ntamd64.jar
│   └── sapjco3.pom
├── src/
│   └── main/
│       └── java/
└── pom.xml
```

### Sample pom.xml Configuration

```xml
<project>
  <!-- ... other configuration ... -->
  
  <dependencies>
    <dependency>
      <groupId>com.sap.conn.jco</groupId>
      <artifactId>sapjco3</artifactId>
      <version>3.1.0</version>
      <!-- Optional: specify classifier for platform-specific JAR -->
      <classifier>linux-x86_64</classifier>
    </dependency>
    
    <!-- Other dependencies -->
  </dependencies>
  
  <repositories>
    <!-- Local repository reference -->
    <repository>
      <id>local</id>
      <url>file://${user.home}/.m2/repository</url>
    </repository>
  </repositories>
</project>
```

## 🔍 Error Handling

### Missing JAR File

By default, the action will fail if the JAR file is not found:

```yaml
- name: Install SAP JCo
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    # fail-if-missing: true  # Default behavior
```

To skip installation gracefully when the file is missing:

```yaml
- name: Install SAP JCo
  uses: persiliao/sap-jco-maven-installer@v1
  with:
    jco-jar-path: 'vendor/sapjco3.jar'
    fail-if-missing: false
```

### Invalid JAR File

The action validates JAR files before installation. If an invalid file is detected:

1. **Default behavior**: Fails the action
2. **Validation includes**: JAR/ZIP file format, readable content
3. **Error message**: Clear indication of validation failure

## ⚡ Performance Tips

### 1. Cache Maven Repository

```yaml
- name: Cache Maven dependencies
  uses: actions/cache@v4
  with:
    path: ~/.m2/repository
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
    restore-keys: |
      ${{ runner.os }}-maven-
```

### 2. Reuse Installation

Install SAP JCo once and reuse it across multiple jobs:

```yaml
jobs:
  install-jco:
    runs-on: ubuntu-latest
    outputs:
      maven-repo-path: ${{ steps.install.outputs.maven-repo-path }}
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Install SAP JCo
      id: install
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'vendor/sapjco3.jar'
  
  test:
    needs: install-jco
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Use cached repository
      run: |
        echo "Using Maven repo from install job"
        # The repository is already populated
```

## 🐛 Troubleshooting

### Common Issues

1. **JAR file not found**
   ```
   Error: SAP JCo JAR not found at: vendor/sapjco3.jar
   ```
   - Verify the file exists in the repository
   - Check the path is correct
   - Use `fail-if-missing: false` to skip gracefully

2. **Maven not installed**
   ```
   WARNING: Maven (mvn) command not found in PATH
   ```
   - Add `actions/setup-java` step before this action
   - Ensure Java and Maven are properly installed

3. **Permission denied**
   ```
   Permission denied when accessing JAR file
   ```
   - Check file permissions in the repository
   - Ensure the file is readable

4. **Invalid JAR format**
   ```
   File may not be a valid JAR
   ```
   - Verify the file is a valid JAR/ZIP file
   - Download a fresh copy from SAP

### Debugging

Enable step debugging in your workflow:

```yaml
name: Debug Workflow
on: [push]

jobs:
  debug:
    runs-on: ubuntu-latest
    env:
      ACTIONS_STEP_DEBUG: true
      ACTIONS_RUNNER_DEBUG: true
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Install SAP JCo (debug)
      uses: persiliao/sap-jco-maven-installer@v1
      with:
        jco-jar-path: 'vendor/sapjco3.jar'
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## ⚖️ Disclaimer

This action is not affiliated with, authorized, maintained, sponsored, or endorsed by SAP SE or any of its affiliates. SAP is a registered trademark of SAP SE in Germany and in several other countries. Use of SAP JCo requires appropriate licenses from SAP.

## 🙏 Acknowledgments

- SAP SE for SAP Java Connector (JCo)
- GitHub for GitHub Actions platform
- The open source community for inspiration and support

---

## 📚 Additional Resources

- https://help.sap.com/viewer/product/SAP_JAVA_CONNECTOR
- https://docs.github.com/en/actions
- https://maven.apache.org/guides/
- https://maven.apache.org/plugins/maven-install-plugin/

## 📊 Version Compatibility

| Action Version | Maven | Java | GitHub Actions |
|----------------|-------|------|----------------|
| v1.x | 3.6.3+ | 8+ | 2.296.0+ |
| Latest | 3.9.6+ | 11+ | Latest |

## 🔄 Changelog

### v1.0.0
- Initial release
- Support for custom JAR paths
- Maven coordinate customization
- Installation verification
- Cross-platform support

---

**Note**: This action is designed to handle SAP JCo installation scenarios. Ensure you have the necessary licenses and permissions to use SAP JCo in your projects.