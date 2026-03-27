# Player Updating

Player updating is a complex packet (opcode 216, VAR_SHORT) that uses **bit-level access** to efficiently describe player movements and appearance changes.

## Movement Types

Movement information is packed into bits within the update mask:

| Type | Bits | Description |
|------|------|-------------|
| 0 | 1 bit | No movement (idle). 1 bit for update-required flag. |
| 1 | 3 + 1 bits | Walk: 3 bits for direction (0–7), 1 bit for update required |
| 2 | 3 + 3 + 1 bits | Run: 3 bits for last direction, 3 bits for current direction, 1 bit for update required |
| 3 | 2 + 1 + 7 + 7 + 1 bits | Teleport: 2 bits for plane (0–3), 1 bit for clear waypoint queue, 7 bits for local X, 7 bits for local Y, 1 bit for update required |

## Direction Encoding (3-bit)

| Value | Direction |
|-------|-----------|
| 0 | North-West |
| 1 | North |
| 2 | North-East |
| 3 | West |
| 4 | East |
| 5 | South-West |
| 6 | South |
| 7 | South-East |

## Player Update Process

The player update packet consists of two main sections:

### 1. Movement Updates
- Updates the position of the local player
- Updates positions of other players in view
- Packed using bit-level access for efficiency

### 2. Appearance/State Updates
For players with the "update required" flag set:
- Update masks indicate which aspects changed
- Each mask corresponds to specific player state:
  - Graphics (animations)
  - Animation
  - Forced chat
  - Chat
  - Face entity
  - Appearance
  - Face coordinate
  - Hit damage
  - Hit damage 2

## Bit Packing Example

For a player walking north (direction 1) with no other updates:
```
Movement type: 1 (walk) = 2 bits
Direction: 1 (north) = 3 bits  
Update required: 0 (no) = 1 bit
Total: 6 bits
```

This efficient packing allows hundreds of player positions to be updated in a single packet.

## Local vs Global Player Lists

The client maintains two player lists:
1. **Local players** - Players currently visible on screen
2. **Global players** - All players the client knows about

The update process:
1. Update local player list (add/remove based on distance)
2. Update movement for all local players
3. Process appearance updates for flagged players

## Update Masks

When a player has the "update required" flag set, a mask byte follows indicating what changed:

| Bit | Update Type |
|-----|-------------|
| 0x1 | Graphics |
| 0x2 | Animation |
| 0x4 | Forced chat |
| 0x8 | Chat |
| 0x10 | Face entity |
| 0x20 | Appearance |
| 0x40 | Face coordinate |
| 0x80 | Primary hit |

Additional masks may use a second byte for extended updates.

## Appearance Block

The appearance block contains:
- Gender
- Head icons (prayer, skull)
- Equipment appearance IDs
- Clothing colors
- Animation IDs (stand, walk, run, etc.)
- Username
- Combat level
- Total skill level
- Hidden status