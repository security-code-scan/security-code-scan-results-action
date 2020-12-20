# Security-Code-Scan Action

This action is designed to run as part of a workflow that builds [SecurityCodeScan](https://www.nuget.org/packages/SecurityCodeScan/) or [SecurityCodeScan.VS2017](https://www.nuget.org/packages/SecurityCodeScan.VS2017/) referencing projects.

It produces a GitHub compatible SARIF file for uploading to the repository 'Code scanning alerts'.

# Usage

See [action.yml](action.yml)

### Input Parameters

**sarif_directory**: _(optional)_ The output directory where SARIF files should be collected.

### Workflow Examples

The recommended way to add this action to your workflow, is with a subsequent action that uploads the prepared SARIF files to the repository 'Code scanning alerts'. The analyzed projects must be already referencing [SecurityCodeScan](https://www.nuget.org/packages/SecurityCodeScan/) or [SecurityCodeScan.VS2017](https://www.nuget.org/packages/SecurityCodeScan.VS2017/) Nuget package.  
For example:

```yaml
on:
  push:

jobs:
  SCS:
    runs-on: ubuntu-latest
    steps:     
      - uses: actions/checkout@v2

      - name: Build
        run: |
          dotnet build /p:ErrorLog=analysis.sarif
        
      - name: Convert sarif for uploading to GitHub
        uses: security-code-scan/security-code-scan-results-action@v1.0
        
      - name: Upload sarif	
        uses: github/codeql-action/upload-sarif@v1
```

Another option is to add the nuget package to specific projects from the script:

```yaml
on:
  push:

jobs:
  SCS:
    runs-on: ubuntu-latest
    steps:     
      - uses: actions/checkout@v2

      - name: Build
        run: |
          dotnet add src/SourcesFolderName/ProjectName.csproj package SecurityCodeScan.VS2017
          dotnet add src/SourcesFolderName2/ProjectName2.csproj package SecurityCodeScan.VS2017
          dotnet build /p:ErrorLog=analysis.sarif
        
      - name: Convert sarif for uploading to GitHub
        uses: security-code-scan/security-code-scan-results-action@v1.0
        
      - name: Upload sarif	
        uses: github/codeql-action/upload-sarif@v1
```
