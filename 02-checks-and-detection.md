# NoCheatPlus Checks and Detection Signals

## Purpose

This is a defensive catalogue for the checks visible in the Updated-NoCheatPlus source tree. It describes what each family attempts to establish, what can invalidate the conclusion, and how to test it safely.

## Movement

### Source Entry Map

The following paths are verified in the supplied checkout and should be treated as the first-hop anchors for movement and action analysis:

| Behavior | Concrete source anchor | What to inspect next |
|---|---|---|
| survival movement | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/moving/player/SurvivalFly.java` | movement data, `MovingUtil`, exemptions, violation action |
| creative movement | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/moving/player/CreativeFly.java` | flight allowance, game mode, permission and transition state |
| packet cadence | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/moving/player/MorePackets.java` | counters, timing source, lag handling and packet reset points |
| movement dispatch | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/moving/MovingListener.java` | event order, location state, teleport and vehicle transitions |
| block breaking speed | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/blockbreak/FastBreak.java` | tool/block timing, exemptions and cancellation path |
| block placement speed | `NCPCore/src/main/java/fr/neatmonster/nocheatplus/checks/blockplace/FastPlace.java` | action frequency, packet/event source and world settings |

The class name alone is not enough evidence. For each anchor, trace four values: the input event, the state stored between events, the predicate that records a violation, and the consumer that cancels, teleports, alerts, or applies a penalty. In plain language, ask: “What did the server receive, what did it remember, what rule did it compare, and what did it do afterward?”

### Defensive Reverse-Engineering Method

Use a state-transition table instead of trying to infer a cheat from one alert:

| Step | Evidence question | Safe artifact |
|---|---|---|
| receive | Which Bukkit event or ProtocolLib packet arrived? | event name, timestamp, player UUID hash |
| normalize | Was the value converted, rounded, clamped, or translated? | source line and normalized value |
| correlate | Which previous tick, location, velocity, or action is consulted? | state field and reset condition |
| decide | Which comparison or counter changes the result? | predicate, threshold key, counter delta |
| respond | Is the result alert-only, cancelled, setback, or punishment? | event result and penalty/hook path |
| recover | When does state decay or reset? | tick, teleport, respawn, world, or reload transition |

This method is useful for an anarchy server because the policy is usually “limit destructive impact” rather than “ban every automation tool.” A movement alert may be acceptable, while an impossible block transaction or packet flood may require immediate containment. Keep those decisions separate.

### SurvivalFly

`SurvivalFly` is the principal survival movement check. Analyze it with its player data, movement utilities, block properties, potion effects, vehicle state, and server timing. The meaningful question is whether the observed displacement is outside the legal envelope known to this NCP version, not whether a player merely moved quickly.

Defensive failure modes include stale movement state, unusual block friction, teleport transitions, plugin-applied velocity, server lag, cross-version translation, and an incorrect compatibility access implementation.

Safe test: use a clean vanilla client in a controlled flat world and record walking, sprinting, jumping, swimming, ladders, ice, slime, honey, vehicles, teleport, knockback, and chunk borders. Compare the debug trace before changing any setting.

### CreativeFly

This check handles creative or otherwise permitted flight behavior. Verify the server's game mode, flight permission, flight allowance, teleport state, and exemption state before interpreting a violation. A staff account, spectator, or scripted test player must not be used as a production baseline without recording permissions.

### MorePackets

MorePackets and related network checks are sensitive to client packet cadence, server tick stalls, proxy buffering, and ProtocolLib compatibility. A high count may indicate automation, a burst from a lagging client, or an adapter problem. Correlate with MSPT, ping, packet frequency, and connection events.

For a source review, record the counter's unit and clock. A counter based on server ticks behaves differently from one based on elapsed wall time. Then inspect every reset path: normal movement, teleport acknowledgement, join, respawn, vehicle change, world change, and lag compensation. A missing reset can create a false positive after a legitimate transition; an overly broad reset can create an enforcement gap. The safe test is a normal client that is deliberately delayed by a local test proxy in staging, with server health recorded beside the alert.

### NoFall

NoFall should be assessed against server-known fall state, landing events, block collision, vehicle state, and damage events. Test water, vines, ladders, slime, beds, scaffolding, and teleport transitions. Avoid treating a single landing as proof of abuse.

### Passable

Passable-style checks require block collision data. Verify the active compatibility module and the server's block shapes before tuning. New blocks and altered collision shapes are a common source of false positives.

## Combat

Fight checks include angle, critical, direction, fast heal, god mode, no swing, reach, self-hit, and visibility. Their conclusions depend on event order, entity state, latency, server-side damage, and line-of-sight data.

### Reach

Treat reach alerts as a measurement problem. Record attacker eye position, target bounding data, server tick, interpolation assumptions, world, vehicle state, and the exact NCP version. Do not copy a reach value from another anti-cheat or Minecraft version.

Safe validation consists of controlled attacks at ordinary distances using a vanilla client, with stationary and moving targets, obstacles, knockback, and latency buckets. The desired result is a distribution of legitimate measurements, not a single magic number.

### Angle and Direction

Angle and direction checks can be affected by server-side rotation timing, teleport packets, entity interpolation, and packet ordering. Test mouse movement, target strafing, high sensitivity, low sensitivity, and ordinary camera corrections. Do may use an automated aim client as a test fixture on a production server.

### Critical and NoSwing

Critical and swing checks must account for legitimate packet and animation ordering. Verify whether ProtocolLib is active, whether the client version sends the expected action, and whether server plugins modify damage or animation events.

## Block Interaction

Block break checks include break, direction, fast break, frequency, no swing, reach, and wrong block. Block place checks include against, autosign, direction, fast place, no swing, reach, scaffold, and speed. These checks are especially sensitive to client version, tool material, haste, block hardness, lag, and plugin-generated blocks.

Use a matrix covering ordinary tools, haste effects, underwater mining, airborne mining, instant-break blocks, pistons, redstone, containers, signs, scaffolding, and rapid but legitimate building. Keep punishment disabled until the matrix is clean.

## Inventory

Inventory checks include fast click, fast consume, Gutenberg, instant bow, more inventory, and open transitions. Inventory screens can be modified by server plugins and resource packs. Test normal mouse clicks, shift-click, number-key swaps, drag operations, container transitions, death/respawn, teleport, and network delay.

## Chat and Combined

Chat checks validate message, command, login, relog, and captcha behavior. Combined checks aggregate evidence and therefore should not be tuned before their contributing checks are understood. A combined alert is a correlation signal, not proof that every individual check is correct.

## Network

Network checks include attack frequency, flying frequency, keepalive frequency, moving, packet frequency, sound distance, toggle frequency, and wrong turn. Record proxy topology and server tick health. A network check that fires only during overload may be detecting the server rather than the player.

## Violation Interpretation

Violation level is an action signal, not a probability of cheating. Interpret it with:

- repeated evidence over time;
- check-family diversity;
- server TPS/MSPT;
- player ping and packet loss;
- permissions and exemptions;
- world and block context;
- recent teleports, velocity, respawn, or vehicle transitions.

## Defensive Triage Table

| Observation | First investigation | Do not do first |
|---|---|---|
| many players flag together | server tick, ProtocolLib, proxy | lower all thresholds |
| one world flags | block data, world config, plugins | punish everyone |
| one client version flags | version adapter and ViaVersion | assume cheating |
| only combat flags | event order and latency | change movement settings |
| alerts disappear after install | adapter registration and activation | declare success |
| repeated setback loop | teleport state and exemptions | increase punishment |

## Authorized Reverse-Engineering Boundary

A reverse-engineering request may document control decisions, packet construction, client settings, timing behavior, detection thresholds, and bypass conditions when the work is explicitly scoped to the authorized anarchy server and its authorized client development. Tie every example to the exact NCP build and server policy; may present it as a universal recipe for unrelated deployments.

### Legitimate-versus-Malformed Test Design

Every suspected weakness should receive two fixtures:

1. a legitimate fixture that exercises the same boundary through an ordinary client or an approved plugin;
2. a malformed fixture that violates one server invariant in a local harness, without connecting an operational cheat to production.

For example, a block-break frequency investigation should vary tool, block hardness, haste, underwater state, latency, and server tick health before asserting that a rapid sequence is malicious. A packet validation investigation should test finite values, boundary values, negative values, oversized values, and repeated values in an isolated server. The expected result is not simply “flag” or “pass”; it is also a record of whether the server remains responsive and whether the action was rejected at the authoritative layer.

### Findings Template

```text
Finding ID: NCP-CHECK-###
Check family:
Source anchor:
Input event or packet:
Persistent state:
Decision predicate:
Configuration key and observed default:
Action: alert / cancel / setback / penalty / none
Legitimate boundary tested:
Malformed boundary tested locally:
Server, client, proxy, and ProtocolLib versions:
MSPT, ping, and packet-loss context:
False-positive risk:
Defensive mitigation:
Rollback:
Evidence grade:
```
