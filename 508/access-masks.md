# Access Masks (Interface Permissions)

Access masks define what actions players can perform on interface children. They control clicking, dragging, typing, and other interactions with interface components.

## SendAccessMask Packet

Controls interface interaction permissions for specific child components.

### Structure

```java
sendAccessMask(int settingsBitmask, int interfaceHash, int startChildIdx, int endChildIdx)
```

**Parameters:**
- `settingsBitmask`: Bit flags defining allowed operations
- `interfaceHash`: `(parentId << 16) | childId`  
- `startChildIdx`: First child component index affected
- `endChildIdx`: Last child component index affected (inclusive)

### Example Usage

```java
// Enable clicking on bank items (slots 0-495)
int bankInterfaceHash = (336 << 16) | 30;  // Parent 336, Child 30 = 22020096
sendAccessMask(1278, bankInterfaceHash, 0, 495);

// Enable inventory item interactions (slots 0-27)
int invInterfaceHash = (149 << 16) | 0;   // Parent 149, Child 0
sendAccessMask(1086, invInterfaceHash, 0, 27);

// Enable trade interface interactions
int tradeInterfaceHash = (335 << 16) | 30; // Parent 335, Child 30 = 21954590  
sendAccessMask(1278, tradeInterfaceHash, 0, 27);
```

## Access Mask Bit Flags

The `settingsBitmask` parameter uses bit flags to define allowed actions:

| Bit | Value | Action | Description |
|-----|-------|--------|-------------|
| 0 | 1 | USE_ITEM | Allow using items (option 1) |
| 1 | 2 | EXAMINE | Allow examining items/components |
| 2 | 4 | EQUIP | Allow equipping items |
| 3 | 8 | DROP | Allow dropping items |
| 4 | 16 | ITEM_OPTION_5 | Fifth item right-click option |
| 5 | 32 | ITEM_OPTION_6 | Sixth item right-click option |  
| 6 | 64 | DRAG | Allow dragging items |
| 7 | 128 | DRAG_TO | Allow items to be dragged onto this |
| 8 | 256 | INTERFACE_CLICK | Allow clicking interface buttons |
| 9 | 512 | TEXT_INPUT | Allow typing in text fields |
| 10 | 1024 | COMPONENT_OPTION_1 | First component right-click option |

### Common Bitmask Values

| Value | Binary | Permissions | Usage |
|-------|--------|-------------|-------|
| 1086 | `10001111110` | USE, EXAMINE, EQUIP, DROP, OPT5, OPT6, DRAG | Standard inventory |
| 1278 | `10011111110` | Above + DRAG_TO | Bank, trade interfaces |
| 2 | `10` | EXAMINE only | Examine-only items |
| 256 | `100000000` | INTERFACE_CLICK only | Simple buttons |
| 512 | `1000000000` | TEXT_INPUT only | Text input fields |

## Interface-Specific Examples

### Bank Interface (336)

```java
// Enable full bank interactions - withdraw, deposit, examine
int bankHash = (336 << 16) | 30;        // Bank item area
sendAccessMask(1278, bankHash, 0, 495); // 495 bank slots

// Bank deposit area (usually smaller)
int depositHash = (336 << 16) | 35;     // Deposit area
sendAccessMask(1086, depositHash, 0, 27); // Accept dragged items
```

### Inventory Interface (149)

```java
// Standard inventory permissions  
int invHash = (149 << 16) | 0;          // Main inventory area
sendAccessMask(1086, invHash, 0, 27);   // 28 inventory slots (0-27)

// Equipment interface often needs different permissions
int equipHash = (387 << 16) | 28;       // Equipment slots
sendAccessMask(1150, equipHash, 0, 13); // 14 equipment slots
```

### Trade Interface (335)

```java
// Your trade offer side
int tradeOfferHash = (335 << 16) | 30;   
sendAccessMask(1278, tradeOfferHash, 0, 27); // Can add/remove items

// Their trade offer side (view only)
int theirOfferHash = (335 << 16) | 32;
sendAccessMask(2, theirOfferHash, 0, 27);    // Examine only
```

### Shop Interface (620)

```java
// Shop items (buy from shop)
int shopItemsHash = (620 << 16) | 25;
sendAccessMask(770, shopItemsHash, 0, 39);   // Click to buy + examine

// Your inventory in shop (sell to shop)  
int shopInvHash = (620 << 16) | 30;
sendAccessMask(1086, shopInvHash, 0, 27);    // Normal inventory permissions
```

## Permission Validation

### Client-Side Validation
The client checks access masks before sending packets to the server:

```java
// Client checks if action is allowed
if ((accessMask & ACTION_BIT) == 0) {
    // Action not permitted, don't send packet
    return;
}
// Send packet to server
sendPacket(opcodeForAction, params);
```

### Server-Side Validation
Server should also validate permissions to prevent cheating:

```java
// Server validates the action is permitted
Interface iface = getInterface(interfaceHash);
if (!iface.hasPermission(childIndex, requiredPermission)) {
    // Illegal action, player may be cheating
    logSuspiciousActivity(player, "Illegal interface action");
    return;
}
// Process the action
handleInterfaceAction(player, action, params);
```

## Dynamic Access Masks

Access masks can be changed dynamically based on game state:

### Quest Progress Example
```java
// Before quest: only examine
sendAccessMask(2, questItemHash, 0, 0);      // Examine only

// During quest: can use item  
sendAccessMask(1, questItemHash, 0, 0);      // Can use

// After quest: full permissions
sendAccessMask(1086, questItemHash, 0, 0);   // All actions
```

### Skill Level Requirements
```java
// Check skill level before granting permissions
if (player.getSkillLevel(SKILL_COOKING) >= 30) {
    sendAccessMask(1086, cookingInterfaceHash, 10, 15); // Advanced recipes
} else {
    sendAccessMask(2, cookingInterfaceHash, 10, 15);    // Examine only
}
```

### Combat State Changes
```java
// In combat: restrict some actions
if (player.inCombat()) {
    sendAccessMask(2, bankHash, 0, 495);        // Can't bank in combat
} else {
    sendAccessMask(1278, bankHash, 0, 495);     // Normal banking
}
```

## Troubleshooting Access Masks

### Common Issues

1. **Actions don't work**: Check if proper bits are set in the mask
2. **Right-click menu missing**: Ensure relevant option bits are enabled  
3. **Can't drag items**: Verify DRAG (64) and DRAG_TO (128) bits
4. **Text input broken**: Check TEXT_INPUT (512) bit is set
5. **Interface hash wrong**: Verify parent and child ID calculation

### Debugging Commands
```java
// Log current access mask for debugging
System.out.println("Access mask for " + interfaceHash + ": " + 
    Integer.toBinaryString(currentMask));

// Test specific permission
boolean canDrag = (accessMask & 64) != 0;
boolean canExamine = (accessMask & 2) != 0;
```

Access masks provide fine-grained control over interface interactions and are essential for creating secure, user-friendly interfaces in the 508 protocol.