# NoCheatPlus Low-Restriction Configuration and Policy

## Configuration Principle

Configuration is a policy layer over source behavior. A key is meaningful only when the running build reads it. Always verify spelling, path, default, activation state, and world override behavior in the exact artifact.

## Check Activation

Use the `checks.<group>.<check>.active` model exposed by `CheckType` and the repository's configuration classes. Begin with the parent group, then verify the leaf check. A leaf setting cannot compensate for a disabled parent or an unavailable compatibility component.

## Recommended Low-Restriction Rollout

### Phase 0: Snapshot

Record:

```text
server software and build
Minecraft protocol and client distribution
NCP artifact and commit
Java runtime
ProtocolLib version
proxy and ViaVersion versions
world list and plugins
current config checksum
```

### Phase 1: Classify

Group every check into `gameplay`, `compatibility`, `stability`, or `integrity`. For gameplay checks, enable alert-only or disable them according to the declared server policy. For stability and integrity checks, keep telemetry enabled and record timestamps, player UUID hash, check, violation level, ping, MSPT, packet volume, and context.

### Phase 2: Reduce Gameplay Interference

Change one check family at a time. Prefer the least intrusive action in this order: disable gameplay enforcement, silence alerts, narrow an exemption, then retain only if a measured server-health or data-integrity risk justifies it. Keep a change log:

```text
2026-08-24T12:00Z
Key: checks.moving.survivalfly.active
Old: true
New: true
Reason: baseline only
Scope: staging world
Evidence: NCP-CONFIG-001
Rollback: restore previous config and reload
```

### Phase 3: Protect Infrastructure

Retain controls for crashes, malformed packets, packet floods that threaten server availability, invalid transactions, and item/data corruption. Use narrow exemptions for known plugin-controlled actors only when the replacement server-side invariant is understood. Do not use a gameplay check as a substitute for a firewall, proxy limit, transaction validator, or server resource limit.

### Phase 4: Operate and Recover

Apply no gameplay punishment by default in this policy. If an operational safeguard needs an action, prefer cancellation or rate limiting over teleportation or kick commands, and require a documented health threshold. Keep backups, a rollback command, and a post-reload health check ready.

## Permissions

The plugin descriptor exposes permissions under `nocheatplus.checks.*`, including group and leaf permissions for block break, block place, fight, inventory, moving, and network checks. Silent permissions suppress alerts; they are not equivalent to disabling the check. Document every trusted account and its reason.

Use separate roles:

- test observer: receives diagnostics and may compare restricted versus unrestricted behavior;
- compatibility tester: narrowly scoped silent or exemption permission;
- administrator: configuration, infrastructure safeguards, and rollback control;
- ordinary player: no NCP gameplay punishment under the declared anarchy policy.

## Action Policy

A low-restriction policy has four layers:

1. telemetry for retained safeguards;
2. optional staff alerting;
3. cancellation or rate limiting only for stability and integrity threats;
4. administrative review before any disruptive action.

Use a cool-down and remove stale violation state according to the plugin's supported penalty system. Do not create a command loop that escalates indefinitely while the server is lagging. Never turn a gameplay violation level into an automatic ban in this policy unless the operator explicitly changes the server contract.

## Low-Restriction Tuning Guidance

- Disable or silence gameplay checks when their only function is behavioral conformity.
- Preserve parser, crash, transaction, item-integrity, and availability safeguards.
- Prefer a documented compatibility exemption over an undocumented global weakening when a plugin causes false positives.
- Keep movement, combat, and network parameters independent.
- Record the smallest change that removes unnecessary gameplay interference.
- Never remove an infrastructure safeguard from a single log line; reproduce the failure and measure server health first.

## 2B2T-Style Policy Goals

This server policy permits cheat-assisted gameplay broadly. Translate the goal into explicit operational boundaries rather than attempting to identify every client brand:

| Goal | Defensive control |
|---|---|
| permit combat automation | disable or silence gameplay combat checks after health review |
| permit extreme movement | disable or silence movement checks; retain only server-health limits |
| permit rapid building | disable or silence block frequency and speed checks unless they cause instability |
| protect data integrity | retain item validation and authoritative server-side transactions |
| reduce crashes | retain malformed-input validation, packet limits, and proxy controls |
| preserve automation | avoid unnecessary exemptions by removing the gameplay restriction itself |

## Configuration Anti-Patterns

- copying GrimAC keys into NCP config;
- assuming `setbackvl` exists for every NCP check;
- enabling every check without compatibility testing;
- using silent permissions as a punishment bypass;
- changing several families simultaneously;
- ignoring server MSPT and packet loss;
- treating a successful reload as proof that a key was consumed.

## Verification Checklist

After reload, verify startup logs, active check state, ProtocolLib adapter registration, one normal action per check family, one controlled edge case, and the absence of new errors. Keep the prior configuration available for immediate rollback.
