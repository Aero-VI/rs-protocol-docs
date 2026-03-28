# Framework Reference

Reference implementations and frameworks that demonstrate 508 protocol usage.

## RS2HD 508

Open source, stable 508 framework that serves as the primary reference implementation.

### Key Features
- **Networking**: Based on Apache MINA for robust networking
- **Configuration**: XStream/XPP for XML configuration management  
- **Dual Mode**: Supports both HD and non-HD clients
- **Complete Systems**: Player updating, NPC system, item management, banking
- **Social Features**: Friends/ignore lists, clan chat

### Architecture Overview

```
RS2HD Server Structure:
├── net/
│   ├── codec/          # Packet encoding/decoding (ISAAC, login)
│   ├── packet/         # PacketHandler implementations  
│   └── ActionSender    # Server→Client packet construction
├── model/
│   ├── player/         # Player entity management
│   ├── npc/            # NPC system
│   └── item/           # Item system
├── game/
│   ├── content/        # Game content (quests, minigames)
│   └── skills/         # Skill implementations
```

### Key Classes

#### ActionSender (Server→Client)
Handles all outgoing packets from server to client:

```java
// Interface packets
sendInterface(int interfaceId, int windowId, int childId, boolean showId)
sendWindowPane(int windowPaneId) 
sendInterfaceConfig(int interfaceHash, boolean hidden)

// Player state packets  
sendSkillLevel(int skillId, int level, int experience)
sendConfig(int configId, int value)
sendMessage(String message, int type)

// World packets
sendMapRegion(int regionX, int regionY, int[][] xteaKeys)
sendCoordinates(int x, int y)
sendGroundItem(int itemId, int amount, int x, int y)

// Item packets
sendItems(int interfaceId, Item[] items)
sendInventory()
sendEquipment()
```

#### PacketHandlers (Client→Server)
Processes incoming packets from client.


### Codec Implementation

#### ISAAC Integration
```java
public class IsaacCipher {
    private int[] keys = new int[4];
    private ISAAC incomingISAAC;  // Decrypt client packets
    private ISAAC outgoingISAAC;  // Encrypt server packets
    
    public void initializeSession(int[] sessionKeys) {
        // Add +50 to each key before seeding (508 specific)
        for (int i = 0; i < 4; i++) {
            keys[i] = sessionKeys[i] + 50;
        }
        incomingISAAC = new ISAAC(keys);
        outgoingISAAC = new ISAAC(keys);
    }
    
    public int decryptOpcode(int opcode) {
        return (opcode - incomingISAAC.getNext()) & 0xFF;
    }
    
    public int encryptOpcode(int opcode) {
        return (opcode + outgoingISAAC.getNext()) & 0xFF;
    }
}
```

#### Packet Size Arrays
```java
public static final int[] CLIENT_PACKET_SIZES = {
    // Client→Server packet sizes (256 entries)
    -3, -3, 8, 8, -3, -3, -3, 2, -3, -3,     // 0-9
    -3, -3, -3, -3, -3, -3, -3, -3, -3, -3,  // 10-19
    -3, 6, 4, -3, -3, -3, -3, -3, -3, -3,    // 20-29
    8, -3, -3, -3, -3, -3, -3, 2, 2, -3,     // 30-39
    // ... continues for all 256 opcodes
};

public static final int[] SERVER_PACKET_SIZES = {
    // Server→Client packet sizes (256 entries)  
    7, -3, -3, -3, 6, -3, 6, -3, -3, -3,     // 0-9
    -3, -3, 4, -3, -3, -3, -3, -3, -3, -3,   // 10-19
    -3, -3, -3, -3, -3, 3, -3, -3, -3, -3,   // 20-29
    6, -3, -3, -3, -3, 8, -3, -3, -3, -3,    // 30-39
    // ... continues for all 256 opcodes
};
```

## Other Notable Implementations

### Hyperion
- Event-driven architecture using Netty
- Modular plugin system
- Focus on code quality and documentation

### Apollo  
- Modern Kotlin implementation
- Reactive programming patterns
- Advanced caching and performance optimization

### Elvarg
- Lightweight, educational codebase
- Clear separation of concerns
- Good for learning 508 protocol basics

## Common Implementation Patterns

### Packet Construction Pattern
```java
// Standard pattern for variable-length packets
public void sendVariablePacket(int opcode, Consumer<PacketBuilder> writer) {
    PacketBuilder builder = new PacketBuilder(opcode, PacketType.VAR_SHORT);
    writer.accept(builder);
    player.send(builder.toPacket());
}

// Usage
sendVariablePacket(142, builder -> {
    builder.writeShortA(regionX);
    builder.writeShortBigEndianA(playerY);  
    builder.writeShortA(playerX);
    
    for (int[] xteaKeys : allRegionKeys) {
        builder.writeInt(xteaKeys[0]);
        builder.writeInt(xteaKeys[1]);
        builder.writeInt(xteaKeys[2]);
        builder.writeInt(xteaKeys[3]);
    }
    
    builder.writeByteC(height);
    builder.writeShort(regionY);
});
```

### Event System Pattern
```java
// Game event handling
public class PlayerLoginEvent extends Event {
    private final Player player;
    
    @Override
    public void execute() {
        // Send initial packets
        player.sendWindowPane(player.isHD() ? 746 : 548);
        player.sendMapRegion();
        player.sendInventory();
        player.sendSkills();
        player.sendConfigs();
        
        // Initialize sidebar tabs
        for (int tab = 0; tab < 15; tab++) {
            if (SIDEBAR_INTERFACES[tab] != -1) {
                player.sendSidebarInterface(tab, SIDEBAR_INTERFACES[tab]);
            }
        }
    }
}
```

### Buffer Handling Pattern
```java
public class StreamBuffer {
    private byte[] buffer;
    private int position;
    
    // Write methods with 508 modifiers
    public void writeByteA(int value) {
        buffer[position++] = (byte) (value + 128);
    }
    
    public void writeByteC(int value) {
        buffer[position++] = (byte) (-value);
    }
    
    public void writeByteS(int value) {
        buffer[position++] = (byte) (128 - value);
    }
    
    public void writeShortA(int value) {
        buffer[position++] = (byte) (value >> 8);
        buffer[position++] = (byte) (value + 128);
    }
    
    public void writeLEShort(int value) {
        buffer[position++] = (byte) (value);        // Low byte first
        buffer[position++] = (byte) (value >> 8);   // High byte second
    }
}
```

## Development Guidelines

### Code Organization
1. **Separate concerns**: Keep network, game logic, and content separate
2. **Use builders**: Packet construction should be fluent and type-safe
3. **Cache lookup tables**: Packet sizes, interface mappings, constants
4. **Event-driven**: Use events for game state changes
5. **Plugin architecture**: Allow content to be modular and extensible

### Testing Strategies
1. **Packet validation**: Test all packet structures against known working clients
2. **ISAAC testing**: Verify encryption/decryption matches client expectations  
3. **Login simulation**: Automated testing of login handshake
4. **Interface testing**: Verify all interface interactions work correctly
5. **Performance testing**: Load testing with many concurrent players

The RS2HD framework remains the most complete and stable reference for 508 protocol implementation.