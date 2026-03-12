# C# Standards SDK

```bash
dotnet add package ChartedCode.Sdk.Standards
```

See the **[releases](./releases)** page for version and change information.

## Features

### **- Package Lock**

Default to utilizing `RestorePackagesWithLockFile` as `true` to enable nuget `package.lock.json` functionality which prevents un-vetted dependency changes.

Also default `RestoreLockedMode` to `true` when in a `CI` environment.

The package dependency graph will be re-evaluated and update the lock file when `RestoreLockedMode` is `false`; which is the default when not in a `CI` environment.

### **- Git Versioning**

Automatically generate the Version property based upon the Git Tag Version.  Also appends the commit hash to the Description property.

Disable by setting `UseStandardsGitVersioning` to `false`

Additionally, support having prefixed tags trimmed by setting `StandardsGitTagPrefix` to the prefix value. Useful for monorepos with multiple tagging schemes. The prefix may not contain any hyphens (`-`) as that is the tag delimiter character. If using the prefix, note that at least one prefix tag will need to exist on the repo else builds might not work. For if no instances of that prefix tag exist, this will pull in the latest available tag and if that is another prefix tag, the build will fail.  Init prefix tags on the repo with addition of this property (example: `git tag myprefix-0.0.0-pre`).

### **- Code Standards RuleSet**

Using [Roslynator](https://github.com/JosefPihrt/Roslynator).  Will output warnings or errors on build based on best practices or standards here at Charted Code.

To visually enable the analyzers and helpers in VSCode, enable the `omnisharp.enableRoslynAnalyzers` setting.

See the ruleset here: [/src/ChartedCode.Sdk.Standards/rulesets/chartedcode.ruleset](./src/ChartedCode.Sdk.Standards/rulesets/chartedcode.ruleset)

#### RuleSet Override (per Project)

If you need to override the settings per project, add the following
property to your `.csproj` with a path to your ruleset file.

```xml
<PropertyGroup>
  <CodeAnalysisRuleSet>../../override.ruleset</CodeAnalysisRuleSet>
</PropertyGroup>
```

Example `override.ruleset` file
```xml
<RuleSet Name="Rules for ASP.NET Core" ToolsVersion="15.0">
  <Rules AnalyzerId="Roslynator.CSharp.Analyzers" RuleNamespace="Roslynator.CSharp.Analyzers">
    <Rule Id="RCS1090" Action="None" />
  </Rules>
</RuleSet>
```

### **- Outdated Packages Check**

Before build, checks for outdated versions of package references.  The default setting just checks packages for newer minor or patch versions.

The default check level can be changed with property `CheckForDefault`.  Can be set to `latest`, `minor` (default), or `none` to disable default checking.

Add the `CheckFor` attribute to `PackageReference` nodes to explicitly check at a certain level.  Can be set to `latest` for any higher version, `minor` for higher same major version, or `none` to prevent check.  Example:

```xml
  <PackagaeReference Include="Newtonsoft.Json" Version="12.0.1" CheckFor="latest" />
```

The property `CheckPackageVersions` can be set to `warn` (default), `error`, or `false` to disable all package checking.

> If you are encountering timeouts on the outdated package check, increase the `CheckPackageTimeout` property value (default 30000 milliseconds).

----

## Create a Release

The project uses `semver` without the `v`.  Make sure to use proper identifiers for release on non master branches.

```bash
# master
1.0.0

# develop
1.1.0-rc

# (all else)
1.1.1-pre
```

Create a release in GitHub.  The release created will kick off the CircleCI deploy process.

## Deploy

> Automatically done by GitHub Actions
