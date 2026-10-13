# OAK2 FGbxDefPtr null-guard test build

This branch builds the OAK2 SDK against Maxunit/unrealsdk commit
`6e9ecfc46c610cf758b1b60195514b5e32514e53`.
The only SDK source change adds a null check before validating a generic
definition-pointer property's struct constraint.

The workflow in `.github/workflows/build.yml` runs on pushes to this branch.
It uses the upstream `clang-oak2-release` preset (x64, RelWithDebInfo).
It explicitly checks out the corrected unrealsdk commit into `libs/unrealsdk`;
the original upstream submodule pointer is overridden for this test build.

## Download

Open this repository's Actions page and select the OAK2 null-guard test run.
After it succeeds, download the `OAK2-SDK-DefPtr-NullGuard` artifact.
It contains unrealsdk.dll, pyunrealsdk.dll, matching PDB files,
BUILD_INFO.txt and SHA256SUMS.txt. Artifacts are retained for 30 days.

If no run appears, GitHub Actions may still be disabled for this fork.
Enable workflows on the Actions page. You can then select CI, Run workflow,
and choose branch `test/gbx-defptr-null-guard-oak2`: the workflow path already
exists on the default branch and this test branch supplies the targeted build.

## Scope

Local GCC/UBSan tests reproduced the original null access in the actual setter
body with stand-in engine objects. The patched setter passed six cases,
including rejection of an incompatible constrained type without modifying the
destination. These are not Windows SDK integration tests or game tests.

Both DLLs must be rebuilt: unrealsdk exposes its sources to dependent targets.
The build uses Python 3.14.3 from the existing upstream preset. The installed
Python patch version on the test machine has not been established.

Exit the game before replacing files. Back up both original SDK DLLs.
Install the two newly built DLLs together in their existing plugin location.
Keep the PDB files for crash analysis. This artifact does not replace Python,
mod-manager scripts, or TPS/AutoTravel mods.

Do not treat a successful build as proof that probe v103 is safe.
A staged follow-up probe is still needed before trying NPC brain resolution.
The exact crashing function in the original dump was not symbolicated.

This branch creates Actions artifacts only; it does not publish a release.
