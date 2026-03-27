# Configs and Varbits

Client-side variables that control game state, quest progress, interface behavior, and more.

## Configs (Varps — Variable Parameters)

Configs are client-side variables that control game state like prayer status, quest progress, etc. They are sent to the client via two different opcodes based on value size:

### Config Opcodes

| Opcode | Packet Name | Usage | Structure |
|--------|-------------|-------|-----------|
| 100 | setConfig1 | Small values (fits in 1 byte) | `writeShort(configId)`, `writeByte(value)` |
| 161 | setConfig2 | Larger values (int-sized) | `writeShort(configId)`, `writeInt(value)` |

### Common Config IDs

| Config ID | Description | Type | Usage |
|-----------|-------------|------|-------|
| 43 | Prayer Book Type | int | 0=Normal, 1=Ancients, 2=Curses |
| 102 | Auto Retaliate | byte | 0=Off, 1=On |
| 115 | Brightness | byte | 0-3 (Dark to Bright) |
| 166 | Music Volume | byte | 0-4 (Off to Loud) |
| 168 | Sound Effect Volume | byte | 0-4 (Off to Loud) |
| 169 | Area Sound Volume | byte | 0-4 (Off to Loud) |
| 287 | Private Chat | byte | 0=On, 1=Friends, 2=Off |
| 427 | Clan Chat Rank | byte | Various rank levels |

## Varbits (Variable Bits)

Varbits use bit manipulation within a single config integer to pack multiple boolean/small-value states. This is a space-efficient way to store many small values in one config.

### How Varbits Work

A single 32-bit config integer can store multiple boolean states or small values by allocating specific bit ranges to different game states.

**Example: Barrows Brothers Kill Status**
- Config ID: 1276
- Each brother's death status: 1 bit each (0=alive, 1=dead)
- 6 brothers = 6 bits total
- Bits 0-5: Brother kill flags
- Remaining bits: Other barrows-related states

### Varbit Structure

```java
// Setting a varbit
int configValue = getConfig(configId);
int mask = (1 << bitCount) - 1;  // Create mask for bit range
configValue = (configValue & ~(mask << startBit)) | ((value & mask) << startBit);
setConfig(configId, configValue);

// Reading a varbit  
int configValue = getConfig(configId);
int mask = (1 << bitCount) - 1;
int value = (configValue >> startBit) & mask;
```

### Common Varbit Examples

#### Barrows Doors (17 game objects)
- **Problem**: 17 door objects * 12 bytes per packet = 204 bytes
- **Solution**: Pack all door states into 1 config = 4 bytes (98% reduction)
- Each door: 1 bit (0=closed, 1=open)
- Total: 17 bits in a single integer

#### Quest Progress Tracking
- **Config**: Quest-specific config ID
- **Bits**: Different stages use different bit ranges
- **Example**: Recipe for Disaster uses multiple bits for different sub-quests

#### Skill Experience Counters
- **Problem**: Need to track small incremental values efficiently
- **Solution**: Use bit ranges for different counter types
- **Bits 0-7**: Small counter (0-255)
- **Bits 8-15**: Medium counter (0-255) 
- **Bits 16-31**: Large value storage

### Varbit vs Config Decision Matrix

| Data Type | Size | Frequency of Change | Best Choice |
|-----------|------|---------------------|-------------|
| Single boolean | 1 bit | Low | Varbit |
| Small counter (0-15) | 4 bits | Medium | Varbit |  
| Large counter | 32 bits | High | Dedicated Config |
| Complex state | Variable | Variable | Multiple Configs |

### Implementation Notes

1. **Bit Ordering**: LSB (Least Significant Bit) first
2. **Performance**: Varbits require bit manipulation but save network packets
3. **Debugging**: Use bit visualization tools to understand packed states
4. **Client Cache**: Configs persist across sessions, varbits are recalculated

### CS2 Script Integration

Varbits are heavily used by CS2 client scripts for interface logic:

```cs2
// CS2 script checking a varbit
if (getVarbit(varbitId) == 1) {
    showInterface(interfaceId);
} else {
    hideInterface(interfaceId);
}
```

### Packet Flow Example

```java
// Server side - setting barrows door states
int doorStates = 0;
doorStates |= (door1Open ? 1 : 0) << 0;   // Door 1: bit 0
doorStates |= (door2Open ? 1 : 0) << 1;   // Door 2: bit 1
// ... repeat for all 17 doors
player.sendConfig(1276, doorStates);  // Single packet instead of 17

// Client side - reading door state
boolean isDoor5Open = (getConfig(1276) >> 4) & 1 == 1;  // Check bit 4
```

This system allows the 508 client to efficiently manage thousands of game state variables while minimizing network traffic.