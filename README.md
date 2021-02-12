# Security-Code-Scan Action

This action is designed to run as part of a workflow that builds projects referencing NuGet [SecurityCodeScan.VS2019](https://www.nuget.org/packages/SecurityCodeScan.VS2019/).

It produces a GitHub compatible SARIF file for uploading to the repository 'Code scanning alerts'.

# Usage

See [action.yml](action.yml)

### Input Parameters

**sarif_directory**: _(optional)_ The output directory where SARIF files should be collected.

### Workflow Examples

The recommended way to add this action to your workflow is with a subsequent action that uploads the prepared SARIF files to the repository 'Code scanning alerts'. If the analyzed projects already reference [SecurityCodeScan.VS2019](https://www.nuget.org/packages/SecurityCodeScan.VS2019/) Nuget package:

```yaml
on:
  push:

jobs:
  SCS:
    runs-on: ubuntu-latest
    steps:     
      - uses: actions/checkout@v2
      
      - name: Restore dependencies
        run: dotnet restore

      - name: Build
        run: |
          dotnet build /p:ErrorLog=analysis.sarif
        
      - name: Convert sarif for uploading to GitHub
        uses: security-code-scan/security-code-scan-results-action@v1.2
        
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
      
      - name: Restore dependencies
        run: dotnet restore
      
      - name: Add Security-Code-Scan NuGet package
        run: |
          dotnet add src/SourcesFolderName/ProjectName.csproj package SecurityCodeScan.VS2019
          dotnet add src/SourcesFolderName2/ProjectName2.csproj package SecurityCodeScan.VS2019

      - name: Build
        run: |
          dotnet build /p:ErrorLog=analysis.sarif
        
      - name: Convert sarif for uploading to GitHub
        uses: security-code-scan/security-code-scan-results-action@v1.2
        
      - name: Upload sarif	
        uses: github/codeql-action/upload-sarif@v1
```

For .NET 4.x example see [FullDotNetWebApp demo repository](https://github.com/security-code-scan/FullDotNetWebApp).

## FAQ

### Q:
I have added `security-code-scan/security-code-scan-results-action` and `github/codeql-action/upload-sarif`, but I don't see any results.
### A:
Make sure `/p:ErrorLog=analysis.sarif` parameter is specified for `dotnet build` command in your workflow. See [DotNetCoreWebApp demo repository](security-code-scan/DotNetCoreWebApp) and [FullDotNetWebApp demo repository](https://github.com/security-code-scan/FullDotNetWebApp) examples for the workflow setup.

### Q:
I have added `/p:ErrorLog=analysis.sarif`, but I don't see any results.
### A:
[SecurityCodeScan.VS2019](https://www.nuget.org/packages/SecurityCodeScan.VS2019/) must be added to the projects you want to analyze. You can add it as any other NuGet and commit or you may script adding the NuGet in your workflow:

```yaml
    - name: Add Security-Code-Scan NuGet package
      run: |
        dotnet add CSPROJ_PROJECT_PATH package SecurityCodeScan.VS2019
        dotnet add CSPROJ_PROJECT_PATH2 package SecurityCodeScan.VS2019
```
