# Server to Client Opcodes

Complete opcode mapping for packets sent from server to client in RS 508.

## Packet Size Array

The server→client packet sizes are stored in the client. The array has 256 entries where:
- **0 or positive** → fixed-size packet with that many payload bytes
- **-1** → variable byte-length packet (1-byte size header)
- **-2** → variable short-length packet (2-byte size header)

## Known Server → Client Packets

### Interface & Window Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 0 | sendInterface | FIXED (7) | Sends an interface. Structure: `addLEShort(interfaceId)`, `addLEInt(windowId << 16 \| childId)`, `addByteA(showId)` |
| 239 | setWindowPane | FIXED | Sets the main window pane (e.g., 548 for fixed, 746 for resizable) |
| 93 | setInterface | FIXED | Opens/attaches an interface |
| 59 | setInterfaceConfig | FIXED | Hides/shows interface components |
| 179 | setString | VAR_SHORT | Sets text on an interface component |
| 35 | itemOnInterface | FIXED | Places item display on certain interfaces |

### Player & World Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 142 | setMapRegion | VAR_SHORT | Sends map region data. Writes region coords, XTEA keys for map regions, height level, etc. |
| 216 | playerUpdate / movement | VAR_SHORT | Player update block / movement data |
| 57 | teleportOnMapData | FIXED (3) | Teleport within current map region. Writes: `byteS(height)`, `byteA(x)`, `byteA(y)` |
| 177 | sendCoords | FIXED | Sends coordinate/position reference point for region-based packets |

### Skill & Stats Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 217 | setSkillLevel | FIXED | Sends a skill level + XP to the client |
| 99 | setEnergy | FIXED | Sets the run energy value on the orb |

### Config Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 100 | setConfig1 | FIXED | Sends a client config (varp) — used for small values. Writes config ID + byte value. |
| 161 | setConfig2 | FIXED | Sends a client config (varp) — used for larger values. Writes config ID + int value. |

### Item Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 255 | setItems | VAR_SHORT | Sets items on an interface (e.g., inventory, bank). Variable-length: writes interface hash, item IDs and amounts. |
| 25 | createGroundItem | FIXED | Spawns a ground item |
| 201 | removeGroundItem | FIXED | Removes a ground item |

### Chat & Social Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 218 | sendMessage | VAR_BYTE | Sends a chat message to the client chatbox |
| 252 | setPlayerOption | VAR_BYTE | Sets right-click options on other players (e.g., "Trade with", "Follow") |
| 115 | connectToFriendServer | FIXED | Friend server connection signal |

### Object & World Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 30 | spawnObject | FIXED | Spawns/modifies a game object |
| 248 | graphicsOnPosition | FIXED | GFX/SpotAnim at absolute X & Y |

### Visual & Camera Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 112 | projectile | FIXED | Creates a projectile between two points |
| 227 | setHintIcon | FIXED | Displays a hint arrow icon |
| 40 | rotateCamera | FIXED | Rotates the camera |
| 12 | earthquake | FIXED | Camera shake / earthquake effect |
| 226 | blackoutCompass | FIXED | Blacks out the compass or minimap |

### Sound & Effects

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 119 | playSoundEffect | FIXED | Plays a sound effect |

### System Packets

| Opcode | Name | Size Type | Description |
|--------|------|-----------|-------------|
| 104 | logout | FIXED (0) | Forces the client to log out |
| 114 | systemUpdate | FIXED | System update countdown timer |
| 139 | checkClassesInClient | VAR_SHORT | Anti-cheat: checks client class integrity |
| 172 | writeIpOnWelcomeScreen | FIXED | Writes last login IP on the welcome screen |
| 223 | setBankOptions | FIXED | Sets bank interface options |

### Access Mask Packet
Used to define what actions are allowed on interface children (e.g., allowing item clicking in bank):
```
sendAccessMask(int bitRegisterValue, int interfaceHash, int startIdx, int endIdx)
```
- `interfaceHash` = `(parentId << 16) | childId`
- Example: `(336 << 16) | 30` = `22020096` (trade interface, child 30)

## Quick Reference: Opcode Summary

```
  0 = sendInterface           6 = NPC head on interface
 12 = earthquake             25 = createGroundItem
 30 = spawnObject            35 = itemOnInterface
 40 = rotateCamera           57 = teleportOnMapData
 59 = setInterfaceConfig     93 = setInterface
 99 = setEnergy             100 = setConfig1
104 = logout                112 = projectile
114 = systemUpdate          115 = connectToFriendServer
119 = playSoundEffect       139 = checkClasses (anticheat)
142 = setMapRegion (VAR_SHORT)
161 = setConfig2            172 = writeIpOnWelcomeScreen
177 = sendCoords            179 = setString (VAR_SHORT)
201 = removeGroundItem      216 = playerUpdate (VAR_SHORT)
217 = setSkillLevel         218 = sendMessage (VAR_BYTE)
223 = setBankOptions        226 = blackoutCompass/minimap
227 = setHintIcon           239 = setWindowPane
245 = setNpcHeadEmote       248 = gfxOnPosition
252 = setPlayerOption (VAR_BYTE)
255 = setItems (VAR_SHORT)
```

## Note

The exact packet structures and payload formats require client deobfuscation to fully document. This list represents the known opcodes and their general purposes based on community research.