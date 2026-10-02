# company-portal-11.2.1952.0-to-11.2.2021.0

## Scope

This report compares Company Portal `11.2.1952.0` with `11.2.2021.0` using the original APPXBUNDLE packages and the extracted x64 APPX payloads.

The comparison includes package inventory, APPX manifest changes, SHA 256 comparison, `CompanyPortal.dll`, compiled XAML runtime metadata, `resources.pri`, printable strings, and the managed bridge assemblies. `CompanyPortal.dll` is .NET Native, so structural evidence and shipped strings are used rather than claiming normal managed IL control flow.

Presence of a code path does not prove that it is enabled for every tenant because Company Portal uses service configuration and feature flighting.

## Executive summary

`11.2.2021.0` is a servicing release. There is no new Company Portal feature surface that can be identified with confidence.

The package keeps the same app services, capabilities, startup task, CUE architecture, IME status contract, XAML shape, and Windows minimum version as `11.2.1952.0`. The main native image changes by only `512` bytes and no new meaningful user facing resources appear.

The value of this release in the tracker is that it gives us a clean baseline immediately before the much larger `11.2.2035.0` change.

## Package level changes

| Property | 11.2.1952.0 | 11.2.2021.0 |
| --- | ---: | ---: |
| APPXBUNDLE size | `77,903,506` | `77,903,082` |
| x64 APPX size | `19,773,299` | `19,773,496` |
| x64 extracted size | `53,810,134` | `53,810,574` |
| x64 file count | `271` | `271` |
| Added files | `0` | `0` |
| Removed files | `0` | `0` |
| Files with changed SHA 256 |  | `58` |
| `CompanyPortal.dll` | `39,243,264` | `39,243,776` |
| `resources.pri` | `968,760` | `968,688` |
| `Core/Core.xr.xml` | `79,445` | `79,445` |
| `Navigation/Navigation.xr.xml` | `11,276` | `11,276` |
| Windows minimum version | `10.0.18362.0` | `10.0.18362.0` |

APPXBUNDLE SHA 256:

`11.2.1952.0`: `18d6a48318d97682123627a1ab0c36064b923a8eb3527b3c9cb217e7733262ac`

`11.2.2021.0`: `876c09142edccc9fafe0d8894995a20824f6b3cb9c55944233ea215544320a36`

`CompanyPortal.dll` SHA 256:

`11.2.1952.0`: `5956f5b6d0d54ef134fc4f30365c199871710e8fd0d2914f98e45fbd30e483f0`

`11.2.2021.0`: `0d335d63a2d1f2d930740a3ab15d8f4cbe534ddb3b479660cb575803d3a8fab6`

## 1. The package contract stays the same

The APPX manifest changes only the package identity version. There is no new app service, capability, protocol handler, full trust parameter group, or startup task.

The existing CUE startup task remains present:

```xml
<desktop:StartupTask
    TaskId="CueOnboardingAutoLaunch"
    Enabled="false"
    DisplayName="ms-resource://Microsoft.CompanyPortal/AppConstants/ApplicationName" />
```

The existing bridge app services also remain unchanged:

```text
msft.intune.companyportal.cmbridge
msft.intune.companyportal.sidecarbridge
msft.intune.companyportal.rmbridge
msft.intune.companyportal.epmbridge
```

## 2. The Company Portal UI shape does not change

The compiled XAML files are byte identical between the two versions:

```text
Core/Core.xr.xml
Navigation/Navigation.xr.xml
```

That is strong evidence against a new page, new navigation surface, or meaningful UI redesign in `2021`.

`resources.pri` changes slightly in size, but the printable resource comparison does not expose a new meaningful feature string or user facing experience.

## 3. The IME integration stays on the older status contract

The bundled status library is byte identical in both packages:

```text
Microsoft.Management.Clients.IntuneManagementExtension.StatusServiceLibrary.dll
Size: 24,984 bytes
Version: 1.52.202.0
Target: .NET Framework 4.6
SHA256: 6f2855fa2afc806972c6f445fcae850e62935f7964b926425f01c68f1f017bb6
```

This build line does not yet contain:

```text
AppInstallStatus3
NegotiateSessionVersionAsync
InstallDeadlineTime
DownloadedPendingInstallDeadline
```

Those appear in the later `2035` package.

## 4. Existing Company Portal architecture remains intact

The onboarding state model introduced earlier remains. The unified Devices page path remains. The existing app catalog, compliance, recovery media, EPM, Configuration Manager, and IME bridge paths all remain present.

There is no evidence of a new architecture layer in this release.

## 5. Bridge components

The bridge executables are rebuilt and receive new whole file hashes, but no functional BridgeLauncher code change was identified. The detailed hash and signing history is kept in the separate `BridgeLauncher-history-11.2.1952.0-to-11.2.2044.0.MD` report so it does not dominate the Company Portal release comparison.

## Bottom line

`11.2.2021.0` is best described as a servicing build. It changes binaries internally but does not expose a new Company Portal feature, UI surface, service contract, or package architecture. The important role of this version is that it separates the stable `1952/2021` line from the much larger `2035` release.
