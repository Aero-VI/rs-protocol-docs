# Interface System

Documentation for the RS 508 interface system including window panes, interface hashes, and sidebar tabs.

## Interface Hash

Interfaces use a **hash** that packs a parent ID and child ID into a single 32-bit integer:
```
interfaceHash = (parentId << 16) | childId
```
Example: parent 335, child 30 → `335 << 16 | 30` = `21954590`

## Window Panes

| Window Pane ID | Mode |
|----------------|------|
| 548 | Fixed-size client window |
| 746 | Resizable / HD client window |
| 549 | Unknown / secondary |

## Sidebar Tab Interface IDs (508)

| Tab Index | Interface ID | Description |
|-----------|--------------|-------------|
| 0 | 92 | Attack styles |
| 1 | 320 | Skills |
| 2 | 274 | Quests |
| 3 | 149 | Inventory |
| 4 | 387 | Equipment |
| 5 | 271 | Prayer |
| 6 | 192 | Magic spellbook |
| 8 | 550 | Friends list |
| 9 | 551 | Ignore list |
| 10 | 589 | Clan chat |
| 11 | 261 | Settings |
| 12 | 464 | Emotes |
| 13 | 187 | Music |
| 14 | 182 | Logout |

## Orb Interface IDs (Resizable/HD, parent 746)

| Child ID | Interface | Description |
|----------|-----------|-------------|
| 13 | 748 | HP orb |
| 14 | 749 | Prayer orb |
| 15 | 750 | Energy orb |
| 16 | 747 | Summoning orb |
| 18 | 751 | Settings / other |

## Access Masks

Access masks define what actions players can perform on interface children:

```java
sendAccessMask(int settingsBitmask, int interfaceHash, int startChildIdx, int endChildIdx)
```

- `settingsBitmask`: Bit flags defining allowed operations (e.g., 1278 allows various click options)
- `interfaceHash`: `(parentId << 16) | childId`
- `startChildIdx` / `endChildIdx`: Range of child components affected

### Common Bitmask Values
- `1278` - Standard inventory/bank item interaction
- Other values control specific action permissions

## Client Script System (CS2)

The 508 client uses a script system for interface behavior. Scripts are stored in the cache and executed by the client.

### RunClientScript Packet
Sends parameters to execute a cached client script:
```
Script 150 example with params "IviiiIssssssss":
- Characters read RIGHT-TO-LEFT
- 's' = String parameter
- 'i', 'I', 'v' = Integer parameter
```

Example for inventory interfaces:
- Integer params: `-1, 0, 7, 4, 90, 21954590`
  - `90` = cached inventory index
  - `21954590` = interface hash (parent 335, child 30)

### Script 116
Used to flash/highlight a sidebar tab:
- Parameter: tab ID (integer)

## Common Interface Operations

### Opening an Interface
1. Send `setWindowPane` packet (opcode 239) to set the main window (548 or 746)
2. Send `sendInterface` packet (opcode 0) with interface ID and window position
3. Optionally send `setString` packets (opcode 179) to set interface text
4. Send access masks if interface has clickable components

### Closing an Interface
- Client sends packet 108 (close interface) when X is clicked
- Server can force-close by opening a different interface in the same slot

### Interface Components
- Each interface consists of multiple components (children)
- Components can be text, buttons, item containers, models, etc.
- Components are referenced by their child ID within the parent interface

## Interface Configs (Varbits)

Configs control interface state using the varbit system:

### Configs (Varps — Variable Parameters)
Configs are client-side variables that control game state like prayer status, quest progress, etc.

- **setConfig1 (opcode 100)**: For values fitting in 1 byte
- **setConfig2 (opcode 161)**: For larger values (int-sized)

### Varbits (Variable Bits)
Varbits use bit manipulation within a single config integer to pack multiple boolean/small-value states:
- Each bit (or group of bits) in the integer represents a different state
- Example: Barrows doors — 17 game objects' open/closed states packed into a single integer using 1 bit per door
- This reduces 17 packets (≈204 bytes) down to 1 config update (4 bytes)