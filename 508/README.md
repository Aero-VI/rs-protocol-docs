# RuneScape 508 Protocol Documentation

This repository contains comprehensive documentation for the RuneScape 508 protocol, extracted from the DavidScape Java server source code.

## Overview

The RS 508 protocol uses a binary packet-based communication system between the client and server. Each packet has:
- An opcode (0-255) identifying the packet type
- A size (fixed or variable)
- A payload containing the packet data

## Documentation Structure

- [`client-to-server.md`](client-to-server.md) - All inbound packets sent from client to server
- [`server-to-client.md`](server-to-client.md) - All outbound packets sent from server to client (Note: Server→Client packets are handled differently and not directly visible in this codebase)
- [`login-protocol.md`](login-protocol.md) - Login handshake and authentication flow
- [`packet-structures.md`](packet-structures.md) - Detailed byte-level packet structures

## Packet Size Types

- **Fixed size**: The packet always has the same number of bytes
- **Variable size (-1)**: The client sends the size as the first byte after the opcode
- **Unknown size (-3)**: Either undocumented or unused packet ID

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

## Source

This documentation was extracted from the DavidScape 508 server source code, specifically from:
- `/DavidScape/io/Packets.java` - Packet size definitions
- `/DavidScape/io/PacketManager.java` - Packet routing
- `/DavidScape/io/packets/*.java` - Individual packet handlers