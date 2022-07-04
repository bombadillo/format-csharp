# format-csharp

## Formatting csharp
This repo uses [CSharpier](https://github.com/belav/csharpier) and [Husky](https://www.nuget.org/packages/Husky/) to format code that is being committed to a git repo. This ensures code styling consistency for every commit.

## Requirements
An existing dotnet tools manifests needs to exist. This can be added by running the following command: 
`dotnet new tool-manifest`.

## Install
Installation can be implemented in a such a way that developers would not require any additional steps. It's installed when a project is restored simply by adding the following to a project within the solution:

```xml
<!-- set HUSKY to 0 in CI/CD disable this -->
<Target Name="husky" BeforeTargets="Restore;CollectPackageReferences" Condition="'$(HUSKY)' != 0">
  <Exec Command="dotnet tool restore --ignore-failed-sources"  StandardOutputImportance="Low" StandardErrorImportance="High"/>
  <Exec Command="dotnet tool install husky --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
  <Exec Command="dotnet tool install csharpier --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
  <Exec Command="dotnet husky install" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
</Target>
```

## Config
Once Husky has been installed after a project/solution is restored the Husky config must be updated to control which files and formatted and under which conditions. The following is a good start:

```json
{
  "tasks": [
    {
      "name": "Run csharpier",
      "command": "dotnet",
      "args": ["csharpier", "${staged}"],
      "include": ["**/*.cs"]
    }
  ]
}

```

This runs CSharpier on any files that are staged and being committed. It targets any files that are found using the pattern `**/*.cs`.

## Manual usage
### CSharpier
CSharpier can be run manually using the command `dotnet csharpier .`.

### Husky
The husky command can be run manually using `dotnet husky run`.