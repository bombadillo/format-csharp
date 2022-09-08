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
  <Exec Command="dotnet tool update husky --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" />
  <Exec Command="dotnet tool update csharpier --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" />
  <Exec Command="dotnet tool install husky --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
  <Exec Command="dotnet tool install csharpier --ignore-failed-sources" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
  <Exec Command="dotnet husky install" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="./" />
  <Exec Command="SETX HUSKY 0" StandardOutputImportance="Low" StandardErrorImportance="High" />
</Target>
```

> Note that the `WorkingDirectory` attribute in the `dotnet husky install` command needs to point to the directory that holds the repos `.git` folder, usually the root folder of the repo.

Now run `dotnet restore` and the tools should now be installed.

To add the pre-commit hook simply run this command `dotnet husky add pre-commit -c "dotnet husky run"`. 

> This command could potentially be added to the .csproj but it's untested.

## Config
Once Husky has been installed after a project/solution is restored the Husky config (located in `.husky/task-runner.json`) must be updated to control which files and formatted and under which conditions. The following is a good start:

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

#### Formatting all existing files
Since the default config we're using only formats staged files, it means that existing files will remain unformatted. We can alter the config to format all existing files and then revert back to formatting only staged files. This can be done as follows:

- Change the args located in the husky config from `${staged}` to `${all-files}`.
- Run husky `dotnet husky run`
- All files should now be formatted 
- Commit formatted files
- Revert the args change from `${all-files}` back to `${staged}` 
- Success 🤑
