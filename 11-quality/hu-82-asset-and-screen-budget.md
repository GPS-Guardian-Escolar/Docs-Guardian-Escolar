# H-82 — Asset and screen budget

This document records the intentional exceptions to the production asset and screen
budgets for GPS Guardian Escolar.

## Production assets

| Asset | Approx. size | Justification |
| --- | ---: | --- |
| `Fronted-GuardianEscolar-/assets/images/icon.png` | 799 KB | Native Android/iOS icon source retained at source resolution for adaptive-icon generation. |
| `Fronted-GuardianEscolar-/assets/images/logo-glow.png` | 332 KB | Branded transparent hero artwork; lossy conversion introduces visible halos. |

The remaining production PNG assets are below 150 KB. The two listed files are native
build inputs and are not downloaded as web page content.

## Screen budget

The student dashboard is currently a large protected route because it coordinates
authentication, tracking, notifications, safe zones, QR linking, and responsive layout.
Splitting it is tracked as a separate refactor so the deployment-evidence change does
not alter navigation or mobile behavior.

## Evidence

The infrastructure quality workflow publishes the Compose status, rendered configuration,
container logs, healthcheck results, and rollback instructions as the `deployment-evidence`
artifact. This provides reproducible evidence for the deployed stack while the asset and
screen exceptions remain documented and reviewable in this repository.
