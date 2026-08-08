# Company Portal Change Tracker

The Company Portal Release Note Tracker is a community driven project that tracks visible changes between Microsoft Intune Company Portal (`Microsoft.CompanyPortal`) releases. The goal is simple: make Company Portal changes easier to understand.

Microsoft updates the Company Portal app regularly, but not every change is easy to spot from the outside. Sometimes a new version only contains packaging and signing changes. Sometimes a new version wires up a new app extension, a new startup task, new service endpoints, or new client behaviour that hints at upcoming Intune functionality — often shipped dark and switched on later through an ECS flight. This repository collects those findings in one place.

Each release note is based on observed differences between two Company Portal versions. Each note focuses on what changed, why it may matter, and which components were touched. The intent is not to replace official Microsoft documentation.

## What each release note contains

- A short summary of the version comparison
- Package level changes (bundle/appx sizes, PE build timestamps, manifest bounds)
- Changed `AppxManifest.xml` (capabilities, extensions, protocols, startup tasks)
- Changed payload files (added / removed / content-changed, with size deltas)
- Changed compiled-XAML type indexes (`*.xr.xml`)
- New type names, resource keys, flight names, endpoints and telemetry events
- Function or flow level explanations when the evidence supports it
- Mermaid diagrams for changed flows
- Potential impact based on the available evidence
- What did not change
- Uncertainty and follow up validation notes

## A note on the method (Company Portal is not the IME)

Company Portal is a UWP app, and its main binary `CompanyPortal.dll` is a **.NET Native (AOT)** image, not a normal ECMA-335 assembly. That changes the analysis in two important ways compared to the IME:

- There are no CLR metadata table row counts to diff and no readable IL, so logic level claims are avoided. Type, method and resource key names survive as strings for reflection and serialisation, so string analysis is the primary signal.
- The .NET Native string blob is **repacked on every build**. A naive "added / removed string" diff produces thousands of false positives, because the same literal is captured with a different trailing byte each build. To avoid reporting churn as change, every claim in these notes is validated with a **whole-token presence count across every tracked version** — only true absent to present transitions are reported as new.

Because of this, these notes deliberately report a narrower set of "new" changes than a raw diff would suggest. A large binary size increase is frequently just recompilation of code that already shipped in an earlier version behind a flight.

The canonical architecture used for diffing is the **x64** application `.appx` inside each `.appxbundle`. Language and scale resource packs and the other architectures (`x86`, `arm`, `arm64`) are not diffed unless a finding needs cross-architecture confirmation.

## Available release notes

| Release note | Type | Headline |
| ------------ | ---- | -------- |
| [`11.2.1787.0 → 11.2.1899.0`](release-notes/companyportal-11.2.1787.0-to-11.2.1899.0.MD) | Servicing / rebuild | Version bump and re-sign only; no functional change |
| [`11.2.1899.0 → 11.2.1926.0`](release-notes/companyportal-11.2.1899.0-to-11.2.1926.0.MD) | Feature | "Cue" onboarding auto-launch, sovereign-cloud ECS/telemetry, HTTP throttle/back-off |
| [`11.2.1926.0 → 11.2.1952.0`](release-notes/companyportal-11.2.1926.0-to-11.2.1952.0.MD) | Servicing / rebuild | Recompile and re-sign on top of 1926; no net-new functionality |

## What these notes do not claim

- This repository is not official Microsoft documentation.
- It does not claim that every internal change has a confirmed customer impact.
- It does not claim that a changed binary always means changed behaviour in production. Company Portal ships a large amount of finished-but-dark code that only activates when Microsoft flips an ECS flight (for example `EnableWin11Redesign`, `EnableOSRecovery`, `EnableOnboarding`), so "present in the package" is not the same as "released."
- It does not expose secrets, customer data, tenant data, or private Microsoft source code. The notes are based on observable package differences and technical analysis.

When something is interpretation, it is called out as interpretation. Each release note should be read as a technical investigation.

## Repository structure

```
/
├── README.md
└── release-notes/
    ├── companyportal-11.2.1787.0-to-11.2.1899.0.MD
    ├── companyportal-11.2.1899.0-to-11.2.1926.0.MD
    └── companyportal-11.2.1926.0-to-11.2.1952.0.MD
```

## Evidence sources

`.appxbundle` unpacking and `AppxBundleManifest.xml` comparison, extraction of the x64 `.appx` payload, per-file SHA-256 inventory, `AppxManifest.xml` diffing, PE `TimeDateStamp` reads, compiled-XAML type-index (`*.xr.xml`) diffing, and ASCII plus UTF-16LE string extraction from `CompanyPortal.dll` with whole-identifier tokenisation and cross-version presence counting.
