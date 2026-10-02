# company-portal-11.2.1952.0-to-11.2.2021.0

## Scope

This document describes the direct package changes between `11.2.1952.0` and `11.2.2021.0`.

Evidence sources: direct extraction of both APPXBUNDLEs and their x64 APPX payloads, exact SHA 256 inventory, APPX manifest comparison, package file sizes, compiled XAML runtime metadata, `resources.pri`, printable binary strings, and managed bridge metadata. The x64 package is used for the detailed comparison because the functional payload is duplicated across architectures. `CompanyPortal.dll` is .NET Native, so this report does not claim ordinary managed IL control flow for that binary.

Presence of a code path does not prove that it is enabled for every tenant. Company Portal uses server configuration, feature flighting, and negotiated service contracts.

## Executive summary

`11.2.2021.0` is a servicing release. There is no new static feature surface that can be named with confidence. The manifest changes only the package version, the compiled XAML files are byte identical, the IME `StatusServiceLibrary.dll` is byte identical, and `resources.pri` does not gain a meaningful new resource string.

The x64 package still contains `271` files. `58` files receive different hashes, but only four files change size. `CompanyPortal.dll` grows by only `512` bytes. The bridge binaries are rebuilt, including `BridgeLauncher.exe`, but its managed code region is byte identical to the `1952` launcher.

This version is useful mainly because it creates a clean baseline immediately before the much larger `2035` feature build.

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

## 1. Manifest and runtime surface stay the same

The APPX manifest differs only in the package identity version. The Windows minimum remains `10.0.18362.0`. No app service, capability, full trust parameter group, protocol handler, or startup task is added or removed.

The following compiled XAML files are byte identical:

```text
Core/Core.xr.xml
Navigation/Navigation.xr.xml
```

The existing `CueOnboardingAutoLaunch` task and the four bridge app services remain unchanged.

## 2. No new IME status contract

The bundled IME status contract is byte identical in both packages:

```text
Microsoft.Management.Clients.IntuneManagementExtension.StatusServiceLibrary.dll
Size: 24,984 bytes
Version: 1.52.202.0
Target: .NET Framework 4.6
SHA256: 6f2855fa2afc806972c6f445fcae850e62935f7964b926425f01c68f1f017bb6
```

There is no `AppInstallStatus3`, no `InstallDeadlineTime`, and no `NegotiateSessionVersionAsync` in this build line.

## 3. Resources do not expose a new feature

`resources.pri` shrinks by `72` bytes, but the printable resource comparison does not reveal a new meaningful user facing string or feature key. The app catalog, onboarding, device, compliance, recovery, and bridge surfaces remain the same.

## BridgeLauncher check

`BridgeLauncher.exe` receives a different file hash in this release, but the launcher logic itself does not change.

| Property | 11.2.1952.0 | 11.2.2021.0 |
| --- | --- | --- |
| File size | `12,288` | `12,288` |
| SHA 256 | `309950bfe790e5b42034caa503efaa0936105c742930b947d985f02cc8eee725` | `2e6786057ec2204e1c2b00096a4928103a4ce7b814b9e66d6b5991bad3927607` |
| Target framework metadata | `.NET Framework 4.8` | `.NET Framework 4.8` |
| Embedded Authenticode security directory | `0 bytes` | `0 bytes` |
| Managed code region SHA 256 | `ff703e68626e715cde0898ee1f197b0fab6a7e919a6245d08577af924c08f8b6` | `ff703e68626e715cde0898ee1f197b0fab6a7e919a6245d08577af924c08f8b6` |

The managed code region is byte identical. The changing whole file hash comes from PE/build metadata, CLR metadata, target framework metadata where applicable, version resources, and related build output. The executable itself has no embedded Authenticode certificate table in either package. The APPX package is signed separately.

This matters for the ASR investigation because a Company Portal update can create a new `BridgeLauncher.exe` file identity and hash without changing the launcher program logic.

## Bottom line

`11.2.2021.0` is the missing servicing step between `1952` and `2035`. It does not introduce the later deadline work, EAM/Recast app list path, advanced Sidecar onboarding admission, or the Configuration Manager security changes. Those arrive in `2035`.
