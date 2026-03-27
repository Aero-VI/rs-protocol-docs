# Login Protocol

The RS 508 login process is a multi-stage handshake between client and server.

## Login Stages

### Stage 0: Initial Connection

**Client → Server:**
- Connection type (1 byte)
  - 14 = Game login
  - 15 = Update server request
- Name hash (1 byte) - unused

**Server → Client:**
- Response code (1 byte) - Always 0 for success
- Server session key (8 bytes) - Random 64-bit value

### Stage 1: Login Type

**Client → Server:**
- Login type (1 byte)
  - 14 = Normal login (unused in 508)
  - 16 = New connection
  - 18 = Reconnection

### Stage 2: Login Packet

**Client → Server:**
- Packet size (2 bytes)
- Client version (4 bytes) - Must be 508, 800, or 900
- Low memory flag (1 byte)
- Cache CRC values:
  - 6 shorts (12 bytes total) - Various cache indices
  - 24 bytes - Individual cache file versions
- Junk string (variable)
- 29 ints (116 bytes) - Unknown/junk values  
- Display mode (1 byte)
  - 10 = HD (High Detail)
  - Other = SD (Standard Detail)
- RSA block marker (1 byte) - Must be 10 or 64
- Client session key (8 bytes)
- Server session key (8 bytes) - Must match server's key
- Username (8 bytes) - Encoded as long
- Password (variable string)

**Server → Client:**
- Return code (1 byte)
  - 2 = Success
  - 3 = Invalid username/password
  - 4 = Account banned
  - 5 = Already logged in
  - 7 = World full
  - 9 = Login server offline
  - Other = Could not complete login
- Player rights (1 byte)
  - 0 = Normal player
  - 1 = Player moderator
  - 2 = Administrator
- Flagged (1 byte) - 0 or 1
- Player index (2 bytes) - if successful

## Login Process Flow

1. Client connects to server on port 43594
2. Client sends connection type 14 (game login)
3. Server responds with session key
4. Client sends login type (16 for new, 18 for reconnect)  
5. Client sends full login packet with credentials
6. Server validates:
   - Client version (must be 508/800/900)
   - Username format (letters, digits, spaces only)
   - Password match
   - Not banned (IP or character)
   - Not already online
7. Server sends return code
8. If successful, game session begins

## Return Codes

| Code | Meaning | Description |
|------|---------|-------------|
| 2 | Success | Login successful |
| 3 | Invalid credentials | Wrong username or password |
| 4 | Banned | Account or IP is banned |
| 5 | Already online | Account already logged in |
| 7 | World full | Server at capacity |
| 9 | Login server offline | Cannot verify credentials |
| Other | Generic error | Could not complete login |

## Security Notes

- Passwords are sent as plaintext strings (no hashing)
- Server validates character names (alphanumeric + spaces only)
- IP bans checked via file: `data/characters/bannedhosts/{ip}.txt`
- Character bans via: `data/characters/bannedchars/{username}.txt`
- RSA encryption markers (10/64) are checked but RSA not implemented

## Session Keys

- Server generates random 64-bit session key
- Client must echo this key back in login packet
- Used to verify packet integrity but not for encryption

## Character Loading

After successful login validation:
1. Load character save file
2. Initialize skill levels from experience
3. Set player coordinates
4. Configure equipment and inventory
5. Send initial game packets