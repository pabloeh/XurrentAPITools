# XurrentAPITools

A PowerShell module for interacting with the [Xurrent](https://www.xurrent.com/) (formerly 4me) REST API — connect to one or more Xurrent environments, and create, update, delete, copy, synchronize, and export data.

## Features

- Simple, token-based connection handling via `Connect-Xurrent`
- Save and reuse connections across sessions with `Export-XurrentConnection` / `Import-XurrentConnection`
- Generic data access (`Get-XurrentData`, `New-XurrentRecord`, `Update-XurrentRecord`)
- Import/export utilities for bulk data operations
- Sync cmdlets for copying configuration objects between environments (calendars, teams, services, templates, SLAs, and more)
- Support for multiple concurrent environment connections

## Requirements

- Windows PowerShell 5.1 or later
- .NET Framework 4.5.2 or later
- A Xurrent account and a valid API token
- **PowerShell 7+ is required** for a large part of the module's functionality, not just basic connectivity. This includes:
  - `Export-XurrentConnection` / `Import-XurrentConnection` (relies on `System.Security.Cryptography.ProtectedData`)
  - `Export-XurrentData` / `Import-XurrentData` (the import/export endpoints)
  - All `Sync-Xurrent*` cmdlets (e.g. `Sync-XurrentTeams`, `Sync-XurrentServices`, `Sync-XurrentCalendars`, and the other synchronization cmdlets)

  Windows PowerShell 5.1 can be used for basic operations such as `Connect-Xurrent` and `Get-XurrentData`, but PowerShell 7+ is recommended for full functionality.

## Installation

### Option 1: From the PowerShell Gallery

```powershell
Install-Module -Name XurrentAPITools -SkipPublisherCheck
```

### Option 2: Directly from the repository

Clone or download the repository, then import the module manually:

```powershell
git clone https://github.com/romanenz/XurrentAPITools.git
Import-Module .\XurrentAPITools\XurrentAPITools.psd1
```

Alternatively, download the [latest release](https://github.com/romanenz/XurrentAPITools/releases) as a ZIP, extract it, and import the `.psd1` file the same way.

## Getting Started

### Connecting to Xurrent

Use `Connect-Xurrent` to open a connection to a Xurrent environment. You can supply the API token directly via the `-Token` parameter, or omit it to be prompted interactively.

```powershell
$Environment = Connect-Xurrent -Account wdc -Environment Demo -Region Global
```

`$Environment` is then passed to other cmdlets to indicate which connection they should operate against, which makes it possible to work with several environments in the same session.

### Saving and reusing connections

On PowerShell 7+ (see [Requirements](#requirements)), connections can be persisted so you don't have to re-enter a token every session. The token is encrypted (`System.Security.Cryptography.ProtectedData`, with additional entropy) and stored under `%appdata%\XurrentAPITools\connection`.

`Export-XurrentConnection` and `Import-XurrentConnection` always operate on **all** currently saved/active connections at once — there's no way to export or import a single environment selectively.

```powershell
# Save all current connections for later reuse
Export-XurrentConnection

# In a later session, load all saved connections back without re-authenticating
Import-XurrentConnection
```

## Examples

**Connect and fetch a record:**

```powershell
$Environment = Connect-Xurrent -Account wdc -Environment Demo -Region Global

Get-XurrentData -Type requests -ID 123456789 -Environment $Environment
```

**Create a new record:**

```powershell
New-XurrentRecord -Type requests -Environment $Environment -Body @{
    subject = "New printer request"
    service = "Printing"
}
```

**Update an existing record:**

```powershell
Update-XurrentRecord -Type requests -ID 123456789 -Environment $Environment -Body @{
    status = "assigned"
}
```

**Export data to a file:**

```powershell
Export-XurrentData -Type requests -Environment $Environment -Path "C:\Export\requests.json"
```

**Work with two environments at once (e.g. sync teams from Demo to Production):**

```powershell
$Source = Connect-Xurrent -Account wdc -Environment Demo -Region Global
$Destination = Connect-Xurrent -Account wdc -Environment Production -Region Global

Sync-XurrentTeams -SourceEnvironment $Source -DestinationEnvironment $Destination
```

## Available Cmdlets

<details>
<summary>Click to expand full list</summary>

- `Clear-XurrentExportCache`
- `Connect-Xurrent`
- `ConvertFrom-XurrentCustomFields`
- `ConvertFrom-XurrentWebHookPayload`
- `Copy-XurrentAutomationRule`
- `Copy-XurrentCustomFields`
- `Export-XurrentConnection`
- `Export-XurrentData`
- `Get-XurrentAiClassifierHits`
- `Get-XurrentApiTools`
- `Get-XurrentApproval`
- `Get-XurrentData`
- `Get-XurrentEnvironments`
- `Get-XurrentRateLimit`
- `Get-XurrentShopReferences`
- `Get-XurrentWorkflowTasks`
- `Import-XurrentConnection`
- `Import-XurrentData`
- `New-XurrentRecord`
- `Resolve-XurrentCustomFieldsRelation`
- `Resolve-XurrentRelation`
- `Set-XurrentAPITools`
- `Set-XurrentSource`
- `Set-XurrentSyncDefaults`
- `Sync-XurrentCalendars`
- `Sync-XurrentCustomCollectionElements`
- `Sync-XurrentCustomCollections`
- `Sync-XurrentCustomViews`
- `Sync-XurrentFirstLineSupportAgreements`
- `Sync-XurrentObject`
- `Sync-XurrentProjectCategories`
- `Sync-XurrentRequestTemplates`
- `Sync-XurrentRequestTemplatesAutomationRules`
- `Sync-XurrentServiceInstances`
- `Sync-XurrentServiceOfferings`
- `Sync-XurrentServices`
- `Sync-XurrentShopArtikleCategories`
- `Sync-XurrentShopArtikles`
- `Sync-XurrentSLACoverageGroups`
- `Sync-XurrentSLANotificationSchemes`
- `Sync-XurrentTaskTemplateApprovals`
- `Sync-XurrentTaskTemplateAutomationRules`
- `Sync-XurrentTaskTemplates`
- `Sync-XurrentTeams`
- `Sync-XurrentUIExtensions`
- `Sync-XurrentWaitingForCustomerFollowUps`
- `Sync-XurrentWorkflowTemplateAutomationRules`
- `Sync-XurrentWorkflowTemplates`
- `Update-XurrentInternationalization`
- `Update-XurrentRecord`

</details>

Use `Get-Help <CmdletName> -Full` in PowerShell to see detailed parameter documentation for any of the cmdlets above.

## License

This project is licensed under the [LGPL-2.1 License](LICENSE).

## Links

- [Project repository](https://github.com/romanenz/XurrentAPITools)
- [Releases](https://github.com/romanenz/XurrentAPITools/releases)
- [Xurrent API documentation](https://developer.xurrent.com/)
