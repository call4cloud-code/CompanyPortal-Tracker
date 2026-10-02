# company-portal-11.2.2035.0-to-11.2.2037.0

## Scope

This document describes the direct package changes between `11.2.2035.0` and `11.2.2037.0`.

Evidence sources: direct extraction of both APPXBUNDLEs and their x64 APPX payloads, exact SHA 256 inventory, APPX manifest comparison, package file sizes, compiled XAML runtime metadata, `resources.pri`, printable binary strings, and managed bridge metadata. The x64 package is used for the detailed comparison because the functional payload is duplicated across architectures. `CompanyPortal.dll` is .NET Native, so this report does not claim ordinary managed IL control flow for that binary.

Presence of a code path does not prove that it is enabled for every tenant. Company Portal uses server configuration, feature flighting, and negotiated service contracts.

## Executive summary

`11.2.2037.0` is a partial rollback of the `2035` feature build, not a complete return to the `2021` line.

The newer IME StatusService contract and future installation deadline UX are removed. The advanced Sidecar CUE admission/reconciliation layer is also removed, while the original `CueOnboardingAutoLaunch` startup architecture remains. The Configuration Manager HTTPS/certificate hardening strings introduced in `2035` disappear as well. Search/accessibility metadata shrinks and the Windows minimum version returns to `10.0.18362.0`.

One major `2035` feature does survive: the EAM/Recast All Apps pipeline remains present in `2037`, including `IncludeAllEAMApps`, `EamRecast`, the Recast count code, and the `Include all apps` resources. The shared SelfServicePortal REST library carrying those EAM additions is byte identical between `2035` and `2037`.

## Package level changes

| Property | 11.2.2035.0 | 11.2.2037.0 |
| --- | ---: | ---: |
| APPXBUNDLE size | `78,976,286` | `78,025,583` |
| x64 APPX size | `20,060,885` | `19,810,385` |
| x64 extracted size | `54,562,921` | `53,906,137` |
| x64 file count | `271` | `271` |
| Added files | `0` | `0` |
| Removed files | `0` | `0` |
| Files with changed SHA 256 |  | `64` |
| `CompanyPortal.dll` | `39,955,968` | `39,310,336` |
| `resources.pri` | `970,648` | `969,216` |
| `Core/Core.xr.xml` | `79,565` | `79,487` |
| `Navigation/Navigation.xr.xml` | `12,030` | `11,276` |
| Windows minimum version | `10.0.17763.0` | `10.0.18362.0` |

APPXBUNDLE SHA 256:

`11.2.2035.0`: `b7a7930a8380ded8f24e023af1a320b131396a34efc26d0400d38a78227b83a2`

`11.2.2037.0`: `7369c76bfdd443807e2086b6b6d050f81df344aeacbbd2487b28eb19cc361faa`

`CompanyPortal.dll` SHA 256:

`11.2.2035.0`: `4b2b52ae0a4904e595c7867f4938011ea69402a0951b516f12bd94da773df2ad`

`11.2.2037.0`: `e71294278c86630e025e96dce8a1fb0069152ca2d20f678c8ab0ff3591fb8dff`

## 1. Status3 and deadline handling are rolled back

The IME status library changes from `1.106.52.0` back to the old `1.52.202.0` generation:

```text
2035: 28,536 bytes, .NET Framework 4.7.2
2037: 24,984 bytes, .NET Framework 4.6
```

The following disappear:

```text
AppInstallStatus3
AppInstallStatusCategory
AppInstallSubstatus
NegotiateSessionVersionAsync
InstallDeadlineTime
DownloadedPendingInstallDeadline
WinGetPackageNotFound
WinGetProcessTimeout
```

`IntuneManagementExtensionBridge.exe` loses the matching negotiation and Status3 members, and every `AppServices.Portable.dll` loses `NegotiatedSessionVersion`.

The user facing deadline resources disappear too:

```text
Downloaded for future installation
Downloaded successfully, will be installed on {0:g}
```

## 2. The new Sidecar CUE admission layer is also rolled back

The original CUE startup task is still present. What disappears is the newer `2035` admission/reconciliation layer:

```text
SidecarOnboardingAdmitted
SidecarOnboardingAdmissionOwnerV1
TryAdmitSidecarOnboardingAsync
TryResumePersistedSidecarOnboardingAsync
RefreshDeviceAndValidatePersistedSidecarOnboardingAsync
RevalidateSidecarOnboardingAdmissionWithEcsAsync
WaitForAuthenticatedConfigAsync
ShouldKeepCurrentPageForCueLaunch
OnboardingReconcileOutcome
```

The `2035` log text requiring the exact Sidecar marker also disappears, along with the `[Onboarding][Reconcile]` loop.

So the accurate description is:

```text
Base CUE startup architecture: retained
2035 Sidecar admission/reconcile enhancement: rolled back
```

## 3. Configuration Manager hardening is rolled back

The `2035` Company Portal image contained:

```text
BlockConfigurationManagerHttpsToHttpFallback
RestrictConfigurationManagerAppDescriptionLinksToWebSchemes
DisableCcmCertificateIdentityValidation
CcmIssuingSslValidator
ConfigurationManagerCredentialWithheldOverInsecureChannel
ConfigurationManagerFallbackRegistrationBlocked
```

Those security paths and their detailed certificate/HTTPS diagnostics are absent from `2037`.

## 4. EAM/Recast survives the rollback

This is the important exception. `2037` still contains:

```text
IncludeAllEAMApps
ShowIncludeEAMAppsToggle
EamStatic
EamAutoUpdate
EamRecast
RecastAppsCount
GetRecastApplicationsCountAsync
```

The resources remain:

```text
AppListing_IncludeAllApps
AppsListPage_IncludeAllAppsToggle
Include all apps
```

The shared `Microsoft.Management.Services.SelfServicePortal.Common.Portable.dll` is `646,008` bytes and byte identical between `2035` and `2037`. It keeps:

```text
IncludeAllEAMAppsQueryParameter
includeAllEAMApps
EamStatic
EamAutoUpdate
EamRecast
```

This proves `2037` is not simply a wholesale rollback of the `2035` package.

## 5. UI metadata and Windows floor move back

The extra `2035` search/accessibility resource line disappears, including:

```text
SearchBoxDisabled
SearchBoxEnabled
SearchBoxEnabledStates
SearchGlyph
SearchGlyph.Foreground
CompanyPortalTableSeparatorBrush
```

`Navigation.xr.xml` falls from `12,030` to `11,276` bytes and `Core.xr.xml` shrinks slightly. The Windows minimum version returns from `10.0.17763.0` to `10.0.18362.0`.

## BridgeLauncher check

`BridgeLauncher.exe` receives a different file hash in this release, but the launcher logic itself does not change.

| Property | 11.2.2035.0 | 11.2.2037.0 |
| --- | --- | --- |
| File size | `12,288` | `12,288` |
| SHA 256 | `51e65c20231b96f3fa593cb56400daea48d211f14919cd343f3b17f2027f6f6f` | `cb68332b6bb0607a731e3fb14336d602a9a16fb157a750c2b0f78d75364f30c2` |
| Target framework metadata | `.NET Framework 4.7.2` | `.NET Framework 4.8` |
| Embedded Authenticode security directory | `0 bytes` | `0 bytes` |
| Managed code region SHA 256 | `ff703e68626e715cde0898ee1f197b0fab6a7e919a6245d08577af924c08f8b6` | `ff703e68626e715cde0898ee1f197b0fab6a7e919a6245d08577af924c08f8b6` |

The managed code region is byte identical. The changing whole file hash comes from PE/build metadata, CLR metadata, target framework metadata where applicable, version resources, and related build output. The executable itself has no embedded Authenticode certificate table in either package. The APPX package is signed separately.

This matters for the ASR investigation because a Company Portal update can create a new `BridgeLauncher.exe` file identity and hash without changing the launcher program logic.

## Bottom line

`2037` rolls back three important `2035` lines: the richer IME Status3/deadline contract, the advanced Sidecar CUE admission/reconciliation implementation, and the Configuration Manager HTTPS/certificate hardening. It also backs out the related UI metadata and compatibility floor change. The EAM/Recast All Apps feature remains in place.