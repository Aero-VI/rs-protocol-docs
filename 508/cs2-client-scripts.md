# CS2 Client Scripts

The 508 client uses a script system (CS2) for interface behavior, animations, and client-side logic. Scripts are stored in the cache and executed by the client.

## RunClientScript Packet

The server sends a RunClientScript packet to execute cached client scripts with parameters.

### Parameter Types

CS2 scripts expect parameters in a specific format string, read **RIGHT-TO-LEFT**:

| Character | Type | Description |
|-----------|------|-------------|
| `s` | String | String parameter |
| `i` | Integer | Standard integer parameter |
| `I` | Integer | Alternative integer format |
| `v` | Integer | Variable/value integer |

### Script 150 Example

```java
// Parameter string: "IviiiIssssssss"  (read right-to-left)
// Breakdown:
// s s s s s s s s - 8 string parameters
// I               - 1 integer parameter  
// i i i i         - 4 integer parameters
// v               - 1 variable integer
// I               - 1 integer parameter

// Example values for inventory interface:
int[] intParams = {-1, 0, 7, 4, 90, 21954590};
String[] stringParams = {"", "", "", "", "", "", "", ""};

// Where:
// 90 = cached inventory index
// 21954590 = interface hash (parent 335, child 30)
// 21954590 = (335 << 16) | 30
```

### Common Script IDs

| Script ID | Purpose | Parameter Format | Description |
|-----------|---------|------------------|-------------|
| 116 | Tab Flash | `i` | Flash/highlight a sidebar tab |
| 150 | Inventory Setup | `IviiiIssssssss` | Initialize inventory interface |
| 229 | Equipment Stats | `ii` | Update equipment stat bonuses |
| 317 | Bank Interface | `IviiiIssssssss` | Initialize bank interface |
| 695 | Trade Interface | `IviiiI` | Setup trade screen |

## Script 116 - Tab Flashing

Used to flash/highlight a sidebar tab to draw player attention:

```java
// Server side
player.runClientScript(116, tabId);

// Client side effect
// - Tab icon flashes/pulses
// - Usually yellow/orange highlight
// - Continues until tab is clicked
```

### Tab IDs for Script 116

| Tab ID | Interface | Tab Name |
|--------|-----------|----------|
| 0 | 92 | Attack styles |
| 1 | 320 | Skills |
| 2 | 274 | Quests |  
| 3 | 149 | Inventory |
| 4 | 387 | Equipment |
| 5 | 271 | Prayer |
| 6 | 192 | Magic |
| 8 | 550 | Friends |
| 9 | 551 | Ignore |
| 10 | 589 | Clan Chat |
| 11 | 261 | Settings |
| 12 | 464 | Emotes |
| 13 | 187 | Music |
| 14 | 182 | Logout |

## Interface Hash Calculation

CS2 scripts frequently use interface hashes to identify specific interface components:

```java
int interfaceHash = (parentId << 16) | childId;

// Examples:
// Parent 335, Child 30 → (335 << 16) | 30 = 21954590
// Parent 336, Child 0  → (336 << 16) | 0  = 22020096  
// Parent 548, Child 1  → (548 << 16) | 1  = 35913729
```

## Parameter Ordering

**Critical**: Parameters are processed in reverse order. The parameter string describes the order that parameters should be **consumed** by the script, not the order they're sent.

```java
// Script expecting: "sii" (1 string, 2 ints)
// Server sends: [int1, int2, string1]
// Client reads: string1, int2, int1 (right-to-left)

// Implementation:
int[] intParams = {value1, value2};        // Sent first
String[] stringParams = {stringValue};     // Sent last
runClientScript(scriptId, intParams, stringParams);
```

## Common CS2 Usage Patterns

### Inventory Interfaces

```java
// Setup inventory interface (Script 150)
int[] intParams = {
    -1,           // Unknown flag
    0,            // Start index  
    7,            // Unknown
    4,            // Unknown
    90,           // Inventory index
    interfaceHash // Calculated hash
};
String[] stringParams = new String[8]; // Usually empty
runClientScript(150, intParams, stringParams);
```

### Bank Interfaces

```java
// Setup bank interface
int[] intParams = {
    -1,           // Bank mode flag
    0,            // Start slot
    bankSize,     // Number of items
    6,            // Columns
    495,          // Bank index
    interfaceHash // Bank interface hash  
};
runClientScript(317, intParams, stringParams);
```

### Equipment Stats

```java
// Update equipment stats display
int[] intParams = {
    playerId,     // Player reference
    equipSlot     // Equipment slot updated
};
runClientScript(229, intParams, null);
```

## Script Execution Context

### Client-Side Variables Available to Scripts
- Player stats (levels, experience, combat stats)
- Interface component states
- Config and varbit values  
- Item information (names, stats, descriptions)
- Animation and graphics state
- Sound and music settings

### Common Script Operations
- Show/hide interface components
- Update text on interfaces
- Change interface colors/styles
- Play sounds or music
- Validate user input
- Calculate derived values (combat level, etc.)
- Handle drag/drop operations
- Manage scrollable lists

## Performance Considerations

1. **Network Efficiency**: Scripts execute client-side, reducing server load
2. **Parameter Limits**: Keep parameter counts reasonable to avoid packet bloat
3. **Script Cache**: Scripts are cached client-side, only IDs are sent over network
4. **Error Handling**: Invalid parameters can crash the client, validate server-side

## Debugging CS2 Scripts

### Client Console Commands
```
::cs2 <scriptId>           # Execute script manually
::dumpcache scripts        # List all cached scripts  
::interfacedebug           # Show interface component IDs
```

### Common Issues
- **Parameter mismatch**: Wrong parameter string format
- **Invalid interface hash**: Incorrect parent/child calculation
- **Script not cached**: Client doesn't have the script file
- **Wrong parameter order**: Remember right-to-left processing

The CS2 system provides powerful client-side logic capabilities while maintaining server authority over game state.