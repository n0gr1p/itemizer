# Itemizer

Provides a chat/console interface for moving items around between bags. Optionally also automatically fetches items into the main inventory before item usage, including regular item usage and Ninjutsu tools.

## Nomad / Pilgrim Moogle support

This branch is the custom Itemizer build used by the FFXI Inventory Gremlin / RemoteXI inventory-maintenance workflow.

Canonical source:

- repository: `n0gr1p/itemizer`
- branch: `nomad-moogle-support`

The branch extends Itemizer so Mog-House-access bags can be used while standing near a recognized **Nomad Moogle** or **Pilgrim Moogle**, even though Windower's normal `get_bag_info(...).enabled` flag remains false.

### Proven supported bags

The following have been runtime-tested in both directions through the Nomad path:

- Mog Safe
- Mog Safe 2
- Mog Locker

`Storage` is intentionally excluded because Nomad/Pilgrim Moogles do not expose Mog Storage. Moving items into or out of Storage still requires a Mog House or Rent-a-Room.

The nearby Moogle must be within approximately 6 yalms. This is NPC-backed access, not zone-wide remote storage.

### Proven examples

```text
//get oxblood locker 1
//put oxblood locker 1
//get "Item Name" safe 1
//put "Item Name" safe2 1
//get "Storage Slip 06" locker 1
//put "Storage Slip 06" locker 1
```

The implementation silently interacts with the nearby Nomad/Pilgrim Moogle, suppresses the normal menu, and then performs the Itemizer movement.

### Nomad interaction-session behavior

Nomad/Pilgrim access is treated as an **interaction session**, not as a permanent capability that stays valid for the rest of the zone visit.

Itemizer 3.3.2.2 refreshes that session at the start of each `get` / `put` / `move` operation that needs Nomad-backed access. Multiple Mog-House bag checks within the same Itemizer operation share the fresh interaction, but a later command does not assume an older poke is still valid.

This matters because a Nomad interaction can become stale while the character remains in the same zone. Caching a single successful poke until `zone change` caused later bag-agnostic `get` / `gets` commands to report that items in Safe/Safe2 were not found.

Bag-agnostic searches also continue into Nomad-backed bags whenever the requested count has not yet been satisfied. In particular, `//gets <item>` now searches ordinary enabled bags **and** Safe/Safe2/Locker instead of skipping Nomad-backed copies merely because another copy was found in a normal bag.

The explicitly proven Nomad workflow is `get` / `put` through main Inventory. Direct `move` between arbitrary Nomad-backed bags should be verified before depending on it in an automated workflow.

### RemoteXI inventory workflow

For canonical operational rules, service-point behavior, post-move verification, Porter-slip handling, and automation safety, use `n0gr1p/RemoteXI` branch `master`:

- `docs/AI_INVENTORY_WORKFLOW.md`
- `docs/INVENTORY_INTERACTION_CAPABILITIES.md`
- `docs/INVENTORY_MAINTENANCE_RUNBOOK.md`
- `docs/AI_AUTOMATION_WORKFLOW.md`

The preferred high-coverage service position is in **Mhaura**, with the character standing where both the Nomad Moogle and Porter Moogle are in range. From there the custom Itemizer can service Safe/Safe2/Locker while the Porter tooling handles Porter Storage.

### Commands

```
itemizer delay <delay>
```

Sets the delay for pulling items into your inventory to `delay`. Default: 0.5.

```
itemizer autoitems
```

Shortened: `ai`. Toggles auto-pulling items from bags when not in Inventory. Default: true.

```
itemizer autostack
```

Shortened: `as`. Toggles auto-stacking items when moving items to non-Inventory bags. Default: true.

```
itemizer autoninjatools
```

Shortened: `ant`. Toggles automatically getting ninja tools. Specific tools are preferred before universal tools unless configured otherwise.

```
itemizer useuniversaltools <spell>
```

Shortened: `uut`. Toggles use of only universal tools for the given spell. Do not include `:ichi` or `:ni` suffixes.

Example:

```
itemizer uut katon
```

```
get <item> [bag] [count]
```

Retrieves the specified item from the specified bag. If `bag` is omitted, Itemizer searches accessible bags. If `count` is omitted, one item is fetched. If `count` is `all`, all matching items are fetched.

```
put <item> <bag> [count]
```

Places the specified item into the specified bag. If `count` is omitted, one item is moved. If `count` is `all`, all matching items are moved.

```
move <item> [source bag] <destination bag> [count]
```

Moves the specified item from the source bag to the destination bag. The source bag is optional.

```
gets <item> [bag]
puts <item> <bag>
moves <item> [source bag] <destination bag>
```

Equivalent to the `get`, `put`, and `move` variants with `all` specified for count.

### Notes

Both full bag names and abbreviated names are valid. Wildcards are allowed. For example, `//get *ore` fetches matching ores from accessible bags into Inventory.

### Examples

```
//put Mandau sack
//put Whirlpool mask satchel
//get Hct. Subligar +1 storage
//get Raider's armlets +2 safe
//get "HoPe toRQue" locker
//get earth crystal 5
//get *crystal
```
