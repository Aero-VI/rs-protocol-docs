# Map Region Packet

The map region packet (opcode 142, VAR_SHORT) loads a new map region and provides XTEA keys for decrypting map data.

## Packet Structure

```
writeShortA(mapRegionX)       // Region X coordinate
writeShortBigEndianA(currentY) // Player's Y within region
writeShortA(currentX)          // Player's X within region

// For each 8×8 chunk in the 13×13 region grid:
//   If not in a blacklisted area:
    writeInt(xteaKey[0])       // XTEA key 0 for this region
    writeInt(xteaKey[1])       // XTEA key 1
    writeInt(xteaKey[2])       // XTEA key 2
    writeInt(xteaKey[3])       // XTEA key 3

writeByteC(heightLevel)        // Current height plane
writeShort(mapRegionY)         // Region Y coordinate
```

## Region System

RuneScape's world is divided into regions:
- Each region is 64×64 tiles
- Regions are identified by X,Y coordinates
- The client loads a 13×13 grid of regions (104×104 tiles total)
- Player is positioned in the center of this grid

## XTEA Encryption

The XTEA keys are used to decrypt map data files from the cache:
- Each region has 4 32-bit XTEA keys
- Keys are sent for all regions in the 13×13 grid
- Blacklisted/restricted areas may have null keys
- Map data files are encrypted with these keys

## Coordinate System

### Global Coordinates
- Absolute position in the game world
- Format: X and Y coordinates

### Regional Coordinates  
- Position within a specific region
- X and Y range from 0-63

### Local Coordinates
- Position within the loaded 104×104 area
- X and Y range from 0-103
- Used for most game operations

## Conversion Formulas

```
globalX = regionX * 64 + localX
globalY = regionY * 64 + localY

localX = globalX - (baseRegionX * 64)
localY = globalY - (baseRegionY * 64)
```

## Height Levels

- 0 = Ground level
- 1 = First floor  
- 2 = Second floor
- 3 = Third floor/roof

## Usage

When a player:
1. Logs in
2. Teleports far away
3. Enters certain areas (dungeons, instances)

The server sends this packet to:
1. Update the player's region
2. Provide XTEA keys for map decryption
3. Load new map data from cache
4. Clear old region data

## Example

Player at global coordinates (3222, 3222):
- Region: 50, 50 (3222 / 64 = 50)
- Local within region: 22, 22 (3222 % 64 = 22)
- Height: 0 (ground level)

The packet would load regions 44-56 in both X and Y directions (13×13 grid centered on 50,50).