# Server to Client Packets

Server→Client packets in RS 508 are handled differently than Client→Server packets. The server code shows frame creation methods but doesn't explicitly show opcode mappings like the client packets.

## Known Server→Client Operations

Based on the server code analysis, here are the types of packets the server sends:

### Player Update Packets
- Player movement updates
- Player appearance updates  
- Player animation frames
- Player graphics effects

### Interface Packets
- Show interface (various IDs)
- Show chatbox interface
- Remove interface
- Set interface text
- Animate interface
- Set interface model

### Inventory/Item Packets  
- Send inventory items
- Update single inventory slot
- Send container items (bank, shop, etc)

### Game Message Packets
- Send chat message
- Send private message received
- Send private message sent confirmation

### Configuration Packets
- Set config value
- Set client setting

### Map/Region Packets
- Load map region
- Create ground item
- Remove ground item  
- Create object
- Remove object

### Combat Packets
- Deal damage
- Show hitpoints bar
- Play sound effect
- Create projectile

### Common Frame Creation Methods

From analyzing the code, here are common server packet creation methods:

```java
// Send message
frames.sendMessage(Player p, String message)

// Show interface  
frames.showInterface(Player p, int interfaceId)

// Set interface text
frames.setString(Player p, String text, int interfaceId, int childId)

// Send inventory
frames.sendInventory(Player p)

// Create ground item
frames.createGroundItem(Player p, int itemId, int amount, int x, int y)

// Animation
frames.animateInterfaceId(Player p, int animId, int interfaceId, int childId)

// Send private message
frames.sendReceivedPrivateMessage(Player p, long sender, int rights, String message)

// Configuration
frames.setConfig(Player p, int id, int value)
```

## Packet Structure Notes

Unlike client packets which have explicit opcodes, server packets are created through frame building methods that handle the low-level packet construction internally. The actual opcodes are not exposed in the Java server code.

## Frame Types

Based on code analysis, these are the main frame types the server uses:

1. **Fixed Size Frames** - Have a predetermined size
2. **Variable Byte Frames** - Size sent as 1 byte
3. **Variable Short Frames** - Size sent as 2 bytes

## Common Operations

### Login Response
- Sends player index
- Sends membership status
- Loads saved player data
- Initializes interfaces

### Logout
- Saves player data
- Cleans up server state
- Sends logout packet

### Region Loading
- Sends map region coordinates
- Loads objects in region
- Loads ground items
- Updates NPCs in region

### Player Updating
- Sends local player updates
- Sends other players in view
- Updates appearances
- Handles animations/graphics

## Note

The exact packet opcodes and structures for server→client communication are embedded in the client and would require client reverse engineering or packet capture analysis to fully document. This documentation represents what can be inferred from the server-side code.