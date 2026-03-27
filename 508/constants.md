# Important Constants

Key protocol constants for RS 508 implementation.

## Connection Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Default Port | 43594 | Main game server port |
| Client revision | 508 | Expected by server during login |
| Cache CRC count | 27 | Number of CRC checksums sent during login |
| Login connection type | 14 | Byte sent to initiate login |
| Update connection type | 15 | Byte sent for JS5/cache updates |

## Login Constants

| Constant | Value | Description |
|----------|-------|-------------|
| RSA magic number | 10 | First byte of RSA block |
| ISAAC key offset | +50 | Added to each session key int before ISAAC seeding |
| Normal login opcode | 16 | Login type for normal login |
| Reconnect login opcode | 18 | Login type for reconnection |
| Login packet header size | 43 | Bytes before RSA block |

## Buffer Limits

| Constant | Value | Description |
|----------|-------|-------------|
| Max incoming buffer | 5000 | Client reads max 5000 bytes per server flush |
| Max packet size (byte) | 255 | Maximum for variable-byte packets |
| Max packet size (short) | 65535 | Maximum for variable-short packets |

## Interface Constants  

| Constant | Value | Description |
|----------|-------|-------------|
| Fixed window pane | 548 | Interface ID for fixed-mode window |
| Resizable window pane | 746 | Interface ID for resizable/HD window |
| Secondary window pane | 549 | Unknown/secondary window pane |

## World Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Region size | 64 | Tiles per region (64×64) |
| Loaded regions | 13×13 | Grid of regions loaded around player |
| Loaded area | 104×104 | Total tiles loaded (13×13 regions) |
| Max height level | 3 | Planes 0-3 (ground to roof) |

## Packet Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Opcode count | 256 | Total possible opcodes (0-255) |
| Fixed size marker | ≥0 | Packet has fixed size |
| Var-byte marker | -1 | Packet has 1-byte size header |
| Var-short marker | -2 | Packet has 2-byte size header |

## Player Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Max local players | 255 | Maximum players visible at once |
| Max player index | 2047 | Maximum total players in world |
| Player index offset | 33024 | Subtracted from player index in some packets |

## Skill Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Skill count | 25 | Total number of skills |
| Max skill level | 99 | Maximum level per skill (except some) |
| Max total level | 2496 | Maximum combined skill level |

## Item Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Inventory size | 28 | Standard inventory slots |
| Equipment slots | 14 | Total equipment slots |
| Bank size | 516 | Maximum bank slots (508 era) |

## Chat Constants

| Constant | Value | Description |
|----------|-------|-------------|
| Max message length | 80 | Characters in public chat |
| Chat effects mask | 0xFF00 | Upper byte for effects |
| Chat color mask | 0x00FF | Lower byte for color |

## Common Bit Flags

### Window Modes
- Fixed mode: `displayMode != 10`
- HD/Resizable: `displayMode == 10`

### Player Rights  
- 0 = Normal player
- 1 = Player moderator
- 2 = Administrator

### Walking Flags
- 0 = Walk
- 1 = Run (CTRL held)

## XTEA Constants

| Constant | Value | Description |
|----------|-------|-------------|
| XTEA key count | 4 | Keys per region |
| XTEA rounds | 32 | Encryption/decryption rounds |
| Delta | 0x9E3779B9 | XTEA golden ratio |