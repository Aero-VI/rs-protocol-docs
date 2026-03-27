# Login Protocol

The RS 508 login process is a multi-stage handshake between client and server.

## Connection Types

Every connection to the main gateway server begins with a **single byte** identifying the connection type:

| Value | Connection Type |
|-------|----------------|
| 14 | Login connection |
| 15 | Update / JS5 / On-demand connection |

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

The client now constructs and sends the full login packet:

#### RSA-Encrypted Block (inner)
Written first, encrypted, then appended:

| Order | Type | Description |
|-------|------|-------------|
| 1 | Byte | Magic number: `10` |
| 2 | Long (8 bytes) | Client session key |
| 3 | Long (8 bytes) | Server session key (echo) |
| 4 | Long (8 bytes) | Username packed to 64-bit |
| 5 | C-String | Password (NUL-terminated) |

#### Outer Login Packet
| Order | Type | Description |
|-------|------|-------------|
| 1 | Byte | Login type opcode (16 = normal, 18 = reconnect) |
| 2 | Short | Total packet size |
| 3 | Int | Client revision (508) |
| 4 | Byte | Unknown (always 0, possibly low/high mem flag) |
| 5 | Byte | Unknown (always 0) |
| 6 | Byte | Unknown (always 0) |
| 7 | Short | Applet width (pixels) |
| 8 | Short | Applet height (pixels) |
| 9 | Byte | UID (unique identifier) |
| 10 | C-String | Settings string (applet parameter) |
| 11 | Int | Affiliate identifier |
| 12 | Int | Unknown flags (22 bits used, various client flags) |
| 13 | Int × 27 | Cache CRC32 checksums (27 reference table indices for 508) |
| 14 | Bytes | RSA-encrypted login block (appended at end) |

### Stage 3: ISAAC Seeding
After login data is sent:
1. ISAAC ciphers are seeded from the session keys
2. **Important**: Each int of the 4-int session key array has `50` added to it before seeding the ISAAC cipher used for packet opcode masking
3. The client re-reads a **status code** from the server

**Server → Client:**
- Return code (1 byte) - See table below
- Player rights (1 byte) - if successful
  - 0 = Normal player
  - 1 = Player moderator
  - 2 = Administrator
- Flagged (1 byte) - 0 or 1
- Player index (2 bytes) - if successful

### Login Response Codes
| Code | Meaning |
|------|---------|
| 0 | Exchange session keys (initial handshake) |
| 1 | Wait, retry in ~2 seconds |
| 2 | Login successful |
| 3 | Invalid username or password |
| 4 | Account banned |
| 5 | Account already logged in |
| 6 | Client updated / version mismatch |
| 7 | World full |
| 8 | Login server offline |
| 9 | Login limit exceeded |
| 10 | Bad session ID |
| 11 | Login server rejected session |
| 12 | Members-only world |
| 13 | Could not complete login |
| 14 | Server being updated |
| 15-24 | Various additional error states |

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