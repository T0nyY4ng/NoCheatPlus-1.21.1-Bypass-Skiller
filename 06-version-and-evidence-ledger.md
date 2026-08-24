# Version and Evidence Ledger

## Why This Exists

Anti-cheat behavior is version-specific. This ledger prevents a general statement from silently becoming a claim about Minecraft 1.21.1.

## Repository Facts

| Item | Observed in checkout | Interpretation |
|---|---|---|
| project | Updated-NoCheatPlus | not GrimAC |
| modern API module | description says 1.21 and later | supports a line, not proof of 1.21.1 certification |
| plugin API | `api-version: 1.13` | Bukkit descriptor compatibility declaration |
| packet library | ProtocolLib | no PacketEvents evidence found |
| Java compiler | source/target 1.8 in parent POM | build language target, not server version |
| check registry | `CheckType` enum | primary check taxonomy |
| movement implementation | `SurvivalFly`, `CreativeFly`, `MorePackets`, `NoFall`, `Passable` | NCP naming and architecture |

## Claim Template

```text
Claim ID:
Statement:
Artifact/commit:
Source path:
Exact symbol or config key:
Minecraft/server version:
Dependency versions:
Evidence grade: A/B/C/D
Observed result:
Known limitations:
Next validation:
```

## Version Questions

Before making a 1.21.1 recommendation, answer:

- Which server implementation is running?
- Which exact NCP artifact is installed?
- Which commit produced it?
- Which modern Bukkit API artifact is selected?
- Is ProtocolLib installed, and which version?
- Is ViaVersion present?
- Are clients connecting with mixed protocol versions?
- Does the production config differ by world?
- Are other plugins modifying movement, damage, blocks, or inventory?

## External Documentation Policy

External GrimAC documentation may be useful for conceptual comparison, but it must never be merged into an NCP claim without source confirmation. Names such as `Simulation`, `Knockback`, `GroundSpoof`, `ElytraA`, and `PacketEvents` are not automatically valid NCP identifiers.

## Known Uncertainties

- The checkout does not by itself prove a dedicated 1.21.1 release.
- Runtime behavior depends on the selected compatibility modules.
- Non-free modules may require locally installed server artifacts.
- ProtocolLib adapters are conditional and may be absent.
- Configuration defaults must be read from the exact shipped config implementation.
- Logs can be reordered or incomplete under asynchronous systems.

## Change Ledger

```text
Date | Artifact | Change | Scope | Evidence | Result | Rollback
-----|----------|--------|-------|----------|--------|---------
```

## Closing Rule

When evidence conflicts, prefer the running artifact, then the repository source, then shipped configuration, then tests, and only then external documentation. Record the conflict instead of smoothing it over with a confident summary.
