# Source Snapshot

This directory is a reference snapshot of the relevant source trees from the supplied checkout. It is not independently certified as an official NoCheatPlus 1.21.1 release.

## Provenance

- Source root: `NoCheatPlus-master`
- Project identity: Updated-NoCheatPlus
- API compatibility: repository includes a modern Bukkit API module described as 1.21 and later
- Packet integration: ProtocolLib
- Build language target: Java 8 in the parent Maven configuration
- Snapshot date: 2026-08-24

## Included Trees

- `NCPCore/src`: checks, check data, configuration, permissions, penalties, hooks, and utilities
- `NCPPlugin/src`: plugin bootstrap, commands, descriptors, and resources
- `NCPCompatProtocolLib/src`: packet adapters and ProtocolLib integration
- `NCPLegacy/ModernBukkitAPI/src`: modern Bukkit compatibility layer
- `NCPCompatBukkit/src`: Bukkit and reflective compatibility access

## Usage Rule

Use this snapshot to locate symbols and compare behavior with the handbooks. Cite the original checkout path and commit when publishing findings. Do not treat copied source as proof that the deployed server uses the same artifact.

## Defensive Scope

Source analysis may identify validation gaps, unsafe state transitions, missing bounds checks, and false-positive causes. It can be converted into live-server cheat bypass instructions or packet-spoofing recipes.