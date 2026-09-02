# Itemizer

Provides a chat/console interface for moving items around between bags. Optionally also automatically fetches items into the main inventory before item usage, including regular item usage and Ninjutsu tools.

## Inventory Gremlin deployment note

The RemoteXI inventory-maintenance workflow uses this repository, but **Nomad/Pilgrim Moogle support is not currently on `main`**.

For the custom build used by the FFXI Inventory Gremlin workflow, use:

- repository: `n0gr1p/itemizer`
- branch: `nomad-moogle-support`

That branch adds proven `get` / `put` access to Mog Safe, Mog Safe 2, and Mog Locker while standing near a Nomad Moogle or Pilgrim Moogle. `Storage` remains excluded because Nomad/Pilgrim Moogles do not expose Mog Storage.

The canonical operational documentation lives in `n0gr1p/RemoteXI` on `master`:

- `docs/AI_INVENTORY_WORKFLOW.md`
- `docs/INVENTORY_INTERACTION_CAPABILITIES.md`
- `docs/INVENTORY_MAINTENANCE_RUNBOOK.md`

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
