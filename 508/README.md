# RuneScape 508 Protocol Documentation

This repository contains comprehensive documentation for the RuneScape 508 protocol, compiled from multiple sources including DavidScape server code, RSPS community research, and client analysis.

## Overview

The RS 508 protocol uses a binary packet-based communication system between the client and server. Each packet has:
- An opcode (0-255) identifying the packet type
- A size (fixed or variable)
- A payload containing the packet data

Communication consists of three phases:
1. **Handshake** — connection type identification
2. **Login** — authentication and session key exchange (RSA encrypted)
3. **Game Protocol** — ISAAC-ciphered packet exchange

## Documentation Structure

### Core Protocol
- [`client-to-server.md`](client-to-server.md) - All inbound packets sent from client to server
- [`server-to-client.md`](server-to-client.md) - Server→Client packet analysis
- [`server-to-client-opcodes.md`](server-to-client-opcodes.md) - Complete server→client opcode reference
- [`login-protocol.md`](login-protocol.md) - Login handshake and authentication flow
- [`packet-structures.md`](packet-structures.md) - Detailed byte-level packet structures and data types

### Advanced Topics
- [`player-updating.md`](player-updating.md) - Player movement and update system
- [`interface-system.md`](interface-system.md) - Interface hashes, window panes, and CS2 scripts
- [`map-region-packet.md`](map-region-packet.md) - Map loading and XTEA encryption
- [`obfuscation-patterns.md`](obfuscation-patterns.md) - Client obfuscation and deobfuscation techniques
- [`constants.md`](constants.md) - Important protocol constants and values

## Packet Structure

### Fixed-Size Packets
```
[opcode: 1 byte] [payload: N bytes]
```
Size is known in advance by both client and server.

### Variable-Size Packets (Byte Header)
```
[opcode: 1 byte] [size: 1 byte] [payload: size bytes]
```
Maximum payload: 255 bytes. Size = -1 in packet array.

### Variable-Size Packets (Short Header)
```
[opcode: 1 byte] [size: 2 bytes] [payload: size bytes]
```
Maximum payload: 65535 bytes. Size = -2 in packet array.

## Data Types

### Primitive Types
- **Byte/UByte**: 8-bit signed/unsigned integer
- **Short/UShort**: 16-bit signed/unsigned integer  
- **Int/DWord**: 32-bit signed integer
- **Long/QWord**: 64-bit signed integer

### Transformation Modifiers
- **ByteA**: `value - 128` on write
- **ByteC**: `-value` (negation) on write
- **ByteS**: `128 - value` on write
- **LEShort**: Little-endian short
- **LEInt**: Little-endian integer

See [`packet-structures.md`](packet-structures.md) for complete reference.

## Quick Reference

### Most Common Client→Server Packets

| Opcode | Name | Size | Purpose |
|--------|------|------|---------|
| 3 | Item Equip | 8 | Equip an item |
| 7 | NPC Option 1 | 2 | First right-click option on NPC |
| 49 | Walk Main | -1 | Main map walking |
| 107 | Command | -1 | Chat commands (::) |
| 119 | Walk Minimap | -1 | Minimap walking |
| 160 | Player Option 1 | 2 | First right-click option on player |
| 222 | Public Chat | -1 | Public chat messages |

### Key Server→Client Packets

| Opcode | Name | Purpose |
|--------|------|---------|
| 0 | sendInterface | Display interface |
| 142 | setMapRegion | Load new map area |
| 216 | playerUpdate | Update players |
| 217 | setSkillLevel | Update skill/XP |
| 218 | sendMessage | Chat message |

## Sources

This documentation was compiled from:
- DavidScape 508 server source code
- RSPS Fandom Wiki (508 Protocol page)
- Rune-Server community research
- Client deobfuscation analysis