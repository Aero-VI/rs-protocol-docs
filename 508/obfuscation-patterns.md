# Client Obfuscation Patterns

The 508 client is heavily obfuscated. Here are key patterns for identifying opcodes when reverse engineering:

## XOR Obfuscation Pattern
```java
if ((Class49.opCode ^ 0xffffffff) == -13) { ... }
```
To decode: `-13` → remove negative → `13` → subtract 1 → **opcode 12**

## Direct Comparison Pattern
```java
if (Class49.opCode == 178) { ... }
```
This is simply **opcode 178**.

## Tilde Obfuscation Pattern
```java
if (~Class36_Sub21.currentOpcode == -106)
```
`~x == -106` means `x == 105` (bitwise NOT)

## Decoding Steps

### For XOR Pattern (`^ 0xffffffff`)
1. Take the negative comparison value (e.g., `-13`)
2. Remove the negative sign → `13`
3. Subtract 1 → `12`
4. Result: opcode `12`

### For Tilde Pattern (`~`)
1. Take the negative comparison value (e.g., `-106`)  
2. Remove negative and subtract 1 → `105`
3. Result: opcode `105`

### For Direct Comparison
- The value IS the opcode

## Common Obfuscation Techniques

### Class Name Obfuscation
- Meaningful names replaced with generic ones
- Example: `PlayerUpdatePacket` → `Class49`
- Example: `PacketBuffer` → `Stream`

### Method Name Obfuscation  
- Methods renamed to single letters or nonsense
- Example: `readPacket()` → `a()`
- Example: `decodeOpcode()` → `fg()`

### Field Obfuscation
- Fields renamed to single letters
- Example: `currentOpcode` → `b`
- Example: `packetSize` → `h`

### Control Flow Obfuscation
- Unnecessary conditionals and jumps
- Dead code injection
- Complex boolean expressions

## Deobfuscation Tips

1. **Search for Magic Numbers**
   - Login magic: `10`
   - RSA block marker: `10` or `64`
   - Client revision: `508`

2. **Identify Packet Arrays**
   - Look for 256-element arrays (packet sizes)
   - Arrays accessed with `& 0xFF` (opcode masking)

3. **Find ISAAC Usage**
   - Look for `+ isaac.nextInt()` patterns
   - ISAAC seed offset: `+50`

4. **Trace Network Streams**
   - Socket connections on port 43594
   - Buffer read/write operations

## Example Deobfuscation

Original obfuscated code:
```java
if ((k.a ^ 0xffffffff) == -217) {
    int i = buffer.fg();
    int j = buffer.h() + 128;
    client.b(i, j);
}
```

Deobfuscated meaning:
```java
if (opcode == 216) { // Player update packet
    int playerId = buffer.readShort();
    int updateSize = buffer.readByte() + 128;
    client.updatePlayer(playerId, updateSize);
}
```

## Packet Size Array Location

The packet size arrays are usually found by:
1. Looking for 256-element int/byte arrays
2. Finding references near packet decoding loops
3. Arrays with mix of positive, -1, and -2 values

Client→Server sizes: Often in main packet handling class
Server→Client sizes: Usually in packet decoder class