# Testing and Hardening

## Test Philosophy

A good anti-cheat test asks whether the server distinguishes legal behavior, malformed protocol input, and policy violations under realistic conditions. It does not ask how to make a cheat invisible.

## Test Matrix

| Area | Legitimate cases | Stress/context cases | Expected evidence |
|---|---|---|---|
| movement | walk, sprint, jump | ice, slime, fluids, teleport | no false alert, recovery after transition |
| flight | permitted creative flight | mode change, respawn | correct activation and exemption |
| network | ordinary cadence | jitter, proxy burst, server lag | no mass false positives |
| combat | normal attack, strafe | knockback, latency, obstacles | stable reach and direction signals |
| blocks | tool mining, building | haste, scaffolding, containers | correct speed/frequency behavior |
| inventory | clicks, drag, swaps | death, teleport, custom menus | no stuck state |
| vehicles | boat, minecart, horse | mount/dismount, velocity | state resets correctly |
| chat | message and command | reconnect, hidden chat | valid input accepted |

## Baseline Procedure

1. Create a staging server matching production versions.
2. Install only NCP and required dependencies.
3. Capture a clean vanilla baseline.
4. Add production plugins one at a time.
5. Repeat the matrix after each addition.
6. Store NCP logs, server logs, MSPT, ping, and config hash.
7. Compare distributions, not anecdotes.

## Metrics

Track per check:

- alerts per 1,000 movement or action events;
- unique players affected;
- false-positive confirmations;
- mean and p95 ping;
- mean and p95 MSPT;
- alerts during transitions;
- recovery time after setback;
- punishment actions per alert;
- repeat offenders after staff review.

A parameter change is not successful merely because alerts decrease. It must also preserve detection of controlled policy violations and avoid increasing server instability.

## Regression Cases

Every configuration change should run:

```text
TC-001 vanilla movement on flat ground
TC-002 sprint and jump with ordinary latency
TC-003 ice and slime transitions
TC-004 teleport followed by movement
TC-005 vehicle mount and dismount
TC-006 normal combat at measured distances
TC-007 ordinary block break and place
TC-008 inventory open, click, close
TC-009 ProtocolLib absent or intentionally disabled
TC-010 reload followed by join and leave
```

Record pass/fail and the exact artifact under test.

## Fuzzing Boundary

Protocol validation may use generated malformed values and client-side packet fixtures in the authorized anarchy server's isolated test process. Keep fuzzing bounded and record the exact target environment. The purpose is to understand NCP behavior, develop compatible clients, and prevent crashes, uncaught exceptions, state corruption, and unsafe resource use.

Useful assertions:

- non-finite numeric input is rejected safely;
- oversized text is bounded;
- negative or impossible indexes do not crash the server;
- repeated actions do not grow memory without bound;
- disconnect and reconnect clear transient state;
- a cancelled action cannot bypass server-side authorization.

## Hardening Priorities

1. Keep the server, ProtocolLib, ViaVersion, and NCP compatibility layer aligned.
2. Protect the server from crash and packet-spam conditions independently of movement checks.
3. Keep economy and block protection server-authoritative.
4. Limit punishment automation until staff review confirms signal quality.
5. Use per-world policy for test, event, and production worlds.
6. Monitor exceptions and adapter failures as security events.

## Incident Response

When abuse is reported:

```text
preserve logs -> identify artifact/version -> classify check family
-> correlate latency/tick health -> reproduce with vanilla client
-> isolate plugin/config change -> apply narrow mitigation
-> rerun regression matrix -> document outcome
```

For this authorized anarchy server, a bypass report may be converted into client-development documentation or production configuration when the operator has reviewed its scope and impact. Record the exact server, NCP artifact, configuration, rollback, and stability result so the change is not misapplied elsewhere.

## Review Template

```text
Incident: NCP-INC-000
Window:
Server and NCP artifact:
ProtocolLib/ViaVersion:
World:
Check family:
Observed logs:
MSPT/ping distribution:
Vanilla reproduction:
Root cause confidence: A/B/C/D
Mitigation:
Regression cases:
Rollback:
Owner and review date:
```

## Acceptance Criteria

A hardened configuration should have documented versions, reproducible source evidence, a baseline matrix, controlled alerting, a rollback path, and no unresolved mass false-positive pattern. Detection quality and server availability are both part of the security result.
