# Client to Server Packets

All packets sent from the RS 508 client to the server.

## Packet Listing

| Opcode | Name | Size | Handler | Purpose |
|--------|------|------|---------|---------|
| 0 | Unknown | -3 | - | Undocumented |
| 1 | Unknown | -3 | - | Undocumented |
| 2 | Remove Ignore | 8 | PacketManager | Remove player from ignore list |
| 3 | Item Equip | 8 | | Equip an item |
| 4-6 | Unknown | -3 | - | Undocumented |
| 7 | NPC Option 1 | 2 | | First right-click option on NPC |
| 8-20 | Unknown | -3 | - | Undocumented |
| 21 | Button Click | 6 | | Interface button clicks |
| 22 | Update Request | 4 | PacketManager | Sent when updateReq is true |
| 23-29 | Unknown | -3 | - | Undocumented |
| 30 | Add Friend | 8 | PacketManager | Add player to friends list |
| 31-36 | Unknown | -3 | - | Undocumented |
| 37 | Player Option 2 | 2 | | Second right-click option on player |
| 38 | Item Examine | 2 | PacketManager | Examine an item |
| 39-41 | Unknown | -3 | - | Undocumented |
| 42 | Clan Chat Join/Leave | Unknown | PacketManager | Join or leave clan chat |
| 43 | Input Integer | Unknown | PacketManager | Integer input from dialog |
| 44-46 | Unknown | -3 | - | Undocumented |
| 47 | Idle | 0 | PacketManager | Keep-alive/idle packet |
| 48 | Unknown | -3 | - | Undocumented |
| 49 | Walk Main | -1 | | Main map walking |
| 50-51 | Unknown | -3 | - | Undocumented |
| 52 | NPC Option 2 | 2 | | Second right-click option on NPC |
| 53-58 | Unknown | -3 | - | Undocumented |
| 59 | Mouse Click | 6 | PacketManager | Sent on every mouse click |
| 60 | New Map Region | 0 | PacketManager | Entered new map region |
| 61 | Add Ignore | 8 | PacketManager | Add player to ignore list |
| 62 | Object Spawn | Unknown | PacketManager | Request object spawning |
| 63 | Dialog Continue | Unknown | PacketManager | Continue through dialog |
| 64-69 | Unknown | -3 | - | Undocumented |
| 70 | Magic on Player | 8 | | Cast spell on player |
| 71-83 | Unknown | -3 | - | Undocumented |
| 84 | Object Examine | 2 | PacketManager | Examine an object |
| 85-87 | Unknown | -3 | - | Undocumented |
| 88 | NPC Examine | 2 | PacketManager | Examine an NPC |
| 89-93 | Unknown | -3 | - | Undocumented |
| 94 | Object Guide | Unknown | PacketManager | Guide option on objects |
| 95-98 | Unknown | -3 | - | Undocumented |
| 99 | Unknown | 4 | PacketManager | Unknown purpose |
| 100-106 | Unknown | -3 | - | Undocumented |
| 107 | Command | -1 | | Chat commands (::) |
| 108 | Interface Close | 0 | PacketManager | Close interface |
| 109-112 | Unknown | -3 | - | Undocumented |
| 113 | Interface Button | 4 | | Interface button clicks |
| 114 | Unknown | -3 | - | Undocumented |
| 115 | Ping | 0 | PacketManager | Ping packet |
| 116 | Unknown | -3 | - | Undocumented |
| 117 | Unknown Bytes | -1 | PacketManager | Sends unknown bytes |
| 118 | Unknown | -3 | - | Undocumented |
| 119 | Walk Minimap | -1 | | Minimap walking |
| 120-122 | Unknown | -3 | - | Undocumented |
| 123 | NPC Attack | 2 | | Attack an NPC |
| 124-126 | Unknown | -3 | - | Undocumented |
| 127 | Input String | Unknown | PacketManager | String input from dialog |
| 128-130 | Unknown | -3 | - | Undocumented |
| 131 | Item Give | 7 | | Give item (trade/etc) |
| 132 | Remove Friend | 8 | PacketManager | Remove from friends list |
| 133-137 | Unknown | -3 | - | Undocumented |
| 138 | Walk Other | -1 | | Other walk clicking |
| 139-151 | Unknown | -3 | - | Undocumented |
| 152 | Item Option 1 | Unknown | | First item option |
| 153-157 | Unknown | -3 | - | Undocumented |
| 158 | Object Option 1 | 6 | | First object option |
| 159 | Unknown | -3 | - | Undocumented |
| 160 | Player Option 1 | 2 | | First player option |
| 161-164 | Unknown | -3 | - | Undocumented |
| 165 | Settings Button | 4 | PacketManager | Settings buttons (volume etc) |
| 166 | Unknown | -3 | - | Undocumented |
| 167 | Switch Items | 9 | | Switch items in inventory |
| 168 | Unknown | -3 | - | Undocumented |
| 169 | Button Click | 6 | | Interface button clicks |
| 170-172 | Unknown | -3 | - | Undocumented |
| 173 | Button Click | Unknown | | Interface button clicks |
| 174-177 | Unknown | -3 | - | Undocumented |
| 178 | Private Message | -1 | PacketManager | Send private message |
| 179 | Item Index Switch | 12 | | Switch item positions |
| 180-185 | Unknown | -3 | - | Undocumented |
| 186 | Item Operate | 8 | | Operate item |
| 187-188 | Unknown | -3 | - | Undocumented |
| 189 | Input Long | Unknown | PacketManager | Long (qword) input |
| 190 | Construction | Unknown | PacketManager | Construction build/options |
| 191-198 | Unknown | -3 | - | Undocumented |
| 199 | NPC Option 3 | Unknown | | Third NPC option |
| 200 | Clan Kick | Unknown | PacketManager | Kick from clan chat |
| 201 | Pickup Item | 6 | | Pick up ground item |
| 202 | Unknown | -3 | - | Undocumented |
| 203 | Item Option 1 | 8 | | First item option |
| 204-210 | Unknown | -3 | - | Undocumented |
| 211 | Drop Item | 8 | | Drop an item |
| 212-219 | Unknown | -3 | - | Undocumented |
| 220 | Item Select | 8 | | Eat/drink/use item |
| 221 | Unknown | -3 | - | Undocumented |
| 222 | Public Chat | -1 | | Public chat text |
| 223 | Unknown | -3 | - | Undocumented |
| 224 | Item on Object | Unknown | | Use item on object |
| 225-226 | Unknown | -3 | - | Undocumented |
| 227 | Player Option 3 | 2 | | Third player option |
| 228 | Object Option 2 | 6 | | Second object option |
| 229-231 | Unknown | -3 | - | Undocumented |
| 232 | Button Click | 6 | | Interface button clicks |
| 233 | Button Click | 6 | | Interface button clicks |
| 234-246 | Unknown | -3 | - | Undocumented |
| 247 | Unknown | 4 | PacketManager | Unknown purpose |
| 248 | Unknown | 1 | PacketManager | Unknown purpose |
| 249-252 | Unknown | -3 | - | Undocumented |
| 253 | Trade Player | Unknown | PacketManager | Initiate trade |
| 254-255 | Unknown | -3 | - | Undocumented |

## Detailed Packet Structures

### Packet 3: Item Equip
```
Size: 8 bytes
Structure:
  [0-1] Item ID (short)
  [2-3] Item slot (short)
  [4-7] Interface ID (int)
```

### Packet 7: NPC Option 1  
```
Size: 2 bytes
Structure:
  [0-1] NPC index (short)
```

### Packet 21: Button Click
```
Size: 6 bytes
Structure:
  [0-1] Button ID 2 (short)
  [2-3] Button ID (short)
  [4-5] Unknown (short)
```

### Packet 49: Walk Main
```
Size: Variable
Structure:
  [0] Path length (byte)
  [1..n] Path data (variable)
```

### Packet 107: Command
```
Size: Variable  
Structure:
  [0..n] Command string (null-terminated)
```

### Packet 160: Player Option 1
```
Size: 2 bytes
Structure:
  [0-1] Player index (short)
```

### Packet 167: Switch Items
```
Size: 9 bytes
Structure:
  [0-1] From slot (short)
  [2-3] To slot (short)  
  [4-7] Interface ID (int)
  [8] Unknown (byte)
```

### Packet 211: Drop Item
```
Size: 8 bytes
Structure:
  [0-1] Item ID (short)
  [2-3] Item slot (short)
  [4-7] Interface ID (int)
```

### Packet 222: Public Chat
```
Size: Variable
Structure:
  [0] Text effects (byte)
  [1] Text color (byte)
  [2] Text length (byte)
  [3..n] Encrypted text (variable)
```

## Detailed Packet Structures

### Walking Packets (Opcodes 49, 119, 138)

All walking packets use variable size (-1) and contain waypoint data:

```
Structure:
[Opcode: 1 byte] [Size: 1 byte] [Payload: size bytes]

Payload Format:
[Number of waypoints: 1 byte] [Run flag: 1 byte] [Waypoints: variable]

Each waypoint:
[Delta X: 1 byte signed] [Delta Y: 1 byte signed]
```

**Walk Types:**
- **Opcode 49**: Main map walking (map click)
- **Opcode 119**: Minimap walking (minimap click)  
- **Opcode 138**: Other walking (object interaction, ground item pickup)

**Run Flag:**
- 0 = Walk normally
- 1 = Run (CTRL held or run enabled)

### Interface Button Packets

Multiple opcodes handle interface interactions:

| Opcode | Handler | Usage |
|--------|---------|-------|
| 21 | | Primary button clicks |
| 113 | | Secondary actions |
| 169 | | Fourth option |
| 232 | | Fifth option |
| 233 | | Alternative primary |

**Structure (6 bytes fixed):**
```
[Interface Hash: 4 bytes] [Child ID: 2 bytes]
Interface Hash = (parentId << 16) | childId
```

### Player Interaction Packets

**Structure (2 bytes fixed):**
```
[Player Index: 2 bytes]
```

**Opcodes:**
- 160: First option (usually Attack)
- 37: Second option (usually Follow/Trade)
- 227: Third option
- 253: Fourth option / accept trade

### Item Action Packets

**Structure (8 bytes fixed):**
```
[Interface Hash: 4 bytes] [Slot: 2 bytes] [Item ID: 2 bytes]
```

**Common Item Actions:**
- 3: Equip item
- 203: First item option (usually "Use")
- 220: Eat/drink consumables
- 186: Operate (fifth option)
- 211: Drop item

## Notes

- Packet sizes of -3 indicate undocumented or unused packets
- Variable size packets (-1) send their size as the first byte after the opcode
- Some packets have multiple handlers for the same opcode (e.g., buttons)
- Interface hash calculation: `(parentId << 16) | childId`
- Player indices are typically 1-2047 range
- Item IDs are cache-dependent but follow consistent patterns