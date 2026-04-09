# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Summary

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a full NuGet package restore to ensure all dependencies are resolved correctly in the new target framework:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages that may need to be updated.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Address any warnings that surface during the build, particularly those related to nullable reference types or API obsolescence, as these can indicate subtle compatibility issues introduced during migration.

---

## 3. Run Unit Tests

Execute the test project to verify that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated to determine whether they represent regressions introduced by the migration or pre-existing issues.

---

## 4. Validate the Web Application Locally

Run the `Bookstore.Web` project locally to confirm the application starts and functions correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:
- The application starts without runtime exceptions.
- Key pages and routes load correctly.
- Any database connections defined in `Bookstore.Data` are functioning as expected (check connection strings in `appsettings.json` or environment-specific configuration files).

---

## 5. Review Data Layer

Open `Bookstore.Data` and confirm the following:
- The database provider package (e.g., Entity Framework Core provider for SQL Server, SQLite, etc.) is compatible with the target .NET version.
- Any migrations are up to date. Run the following if using Entity Framework Core:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

If migrations are missing or out of sync, generate a new migration to reflect the current model state:

```bash
dotnet ef migrations add PostMigration --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

---

## 6. Review the CDK Project

Inspect `Bookstore.Cdk` to ensure that any infrastructure definitions still align with the updated application structure. Confirm that:
- Referenced output paths or artifact names have not changed as a result of the migration.
- Any environment-specific configuration values (e.g., runtime identifiers, framework monikers) have been updated to reflect the new target framework.

---

## 7. Check Target Framework Consistency

Verify that all projects in the solution are targeting the same or compatible .NET version. Open each `.csproj` file and confirm the `<TargetFramework>` element is consistent:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Mismatched target frameworks between projects can cause runtime issues that do not surface as build errors.

---

## 8. Review Configuration Files

Check `appsettings.json` and any environment-specific variants (e.g., `appsettings.Production.json`) to ensure:
- Connection strings are correct for the target environment.
- Any configuration keys that were previously handled by `System.Configuration` (common in legacy .NET Framework projects) have been properly migrated to the `Microsoft.Extensions.Configuration` model.