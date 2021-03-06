## Introduction

The Rebound LUA API, unlike most other LUA apis, implements 99% of the game's natives. It is a lot more extensive, meaning you can write **full menu bases** with it.

If you want to find documentation on a game native, you're gonna have to browse: http://www.dev-c.com/nativedb/

The syntax to call natives is very, very simple.

To call a native, simply take its namespace, add an underscore, and then its name, like so:

```lua
function get_local_ped()
   return PLAYER_GET_PLAYER_PED_SCRIPT_INDEX(PLAYER_PLAYER_ID())
end
```

## Documentation
Now on to the actual Rebound API that exposes the menu's functions so that you may add features or do things that the natives won't let you do.

## Stock LUA libraries
The stock LUA libraries Rebound implements are:
- base
- string
- io
- os
- math 

**You need to be really careful about what scripts you run.**

## Logging
```lua
-- Logs a message to the Rebound console.
void log_info(message)
-- Logs an error to the Rebound console.
void log_error(message)
```

## Memory
```lua
-- Writes a byte to  a given address.
void write_byte(address, byte)
-- Reads a byte at a given address.
byte read_byte(address)
-- Writes an int to a given address.
void write_int(address, integer)
-- Reads an int from a given address.
int read_int(address)
-- Writes a float to a given address.
void write_float(address, floating)
-- Reads a float from a given address.
float read_float(address)
-- Writes a bool to a given address.
void write_bool(address, boolean)
-- Reads a bool from a given address.
void read_bool(address)
-- Returns the game's base (So you can use offsets)
uint64 get_game_base()
```
## Scripts
You can use these APIs to interact with the game's scripts.

```lua
-- Gets a global's integer value at a given index
int get_global_int(index)
-- Gets a global's floating point value at a given index
float get_global_float(index)
-- Writes an integer value to a global's address at a given index
void set_global_int(index, value)
-- Writes a floating point value to a global's address at a given index
void set_global_float(index, value)
-- Gets a local's integer value
int get_local_int(script_hash, index)
-- Gets a local's floating point value
float get_local_float(script_hash, index)
-- Writes an integer value to a local's address
void set_local_int(script_hash, index)
-- Writes a floating point value to a local's address
void set_local_float(script_hash, index)
-- Sends a script event to another player (First argument must be the event, other arguments are the event's parameters)
void trigger_script_event(int player_index, int arguments_amount, int[] arguments)
-- Gets the index of the current session host.
int get_session_host()
-- Checks if the player at the given index is the session host
bool is_session_host(index)
```

## Input
You can use these APIs to gather input information.

```lua
-- Returns true if a virtual key code is being pressed
-- You can find virtual key codes here: https://docs.microsoft.com/en-us/windows/win32/inputdev/virtual-key-codes
bool is_key_pressed(virtual_key_code)
-- Pops up the game keyboard to gather input
string get_keyboard_input()
```

## Output
You can use these APIs to send information to the player.

```lua
-- This will send a notification using Rebound's custom notifications framework.
void notify(title, content)
```

## Network
You can use these APIs to interact with network players.

```lua
-- Start impersonating a player in the chat, call it again with the same index to turn it off.
void impersonate(index)
-- Returns true if you're impersonating a given player index
bool is_impersonating(index)
-- Sends a message in the game chat (Takes into account impersonation settings as well)
void send_chat_message(message)
```

## Menu
You can use these APIs to add features to the menu.

```lua
-- Adds a feature to the "Script Features" submenu of network players.
-- Make sure the feature function takes an index as input, example of a valid feature:
--
--    function print_position(index)
--        name = PLAYER_GET_PLAYER_NAME(index)
--        log_info(name)
--        vector = ENTITY_GET_ENTITY_COORDS(PLAYER_GET_PLAYER_PED_SCRIPT_INDEX(index), 0)
--        log_info(tostring(vector.x))
--        log_info(tostring(vector.y))
--        log_info(tostring(vector.z))
--    end
--
void add_player_function(submenu, name, feature)

-- Does the exact same thing as the function above, except it's a per-player toggle,
-- and when it's on the function is called every tick with the player's index.
void add_player_toggle_function(submenu, name, feature)

-- Like the add_player_function, except the function doesn't take any parameters.
-- This is a regular on click feature.
void add_function(submenu, name, feature)

-- Like the add_function, except this function uses a toggle, and is called every
-- time the toggle is on.
void add_toggle_function(submenu, name, feature)

-- Registers a function to be called every time Rebound
-- receives a script event.
--
-- Return false to deny the event, return true to let it through.
-- 
-- You can build custom protections with this.
--
-- Example function that prints all event IDs received:
-- 
-- function script_event_hook(event_id, raw_event_parameters, amount_of_parameters, sender_index) 
--     log_info(event_id)
--     -- return false to stop the event from getting receieved, true lets it through
--     return true
-- end
-- 
void add_script_event_callback(hook)

-- Registers a function to be called every time Rebound
-- receives a net event.
--
-- Return false to deny the event, return true to let it through.
-- 
-- You can build custom protections with this.
--
-- Example function:
-- 
-- function net_event_hook(sender_index, receiver_index, net_event_name)
--     -- return false to stop the event from getting through
--     return true
-- end
-- 
void add_net_event_callback(hook)

-- Registers a function to be called every time Rebound
-- receives an object sync.
--
-- Return false to deny the sync, return true to let it through.
-- 
-- You can build custom protections with this.
--
-- Example function:
-- 
-- function object_hook(player_index, object_type, object_hash) 
--     -- return false to stop the object from getting synced
--     return true
-- end
-- 
void add_object_callback(hook)

-- Registers a function to be called every time Rebound
-- receives a message in the game's chat.
--
-- You can build custom chat filters with this, or your own
-- implementation of chat commands.
-- 
-- Example function:
--
-- function chat_hook(player_index, message)
--   log_info(message) -- Logs the message that was sent in the game's chat
-- end
--
void add_chat_callback(hook)
```

## Natives
99% of natives from http://www.dev-c.com/nativedb/ are available, and callable with the calling convention explained above, go wild.

## Notes
If you ever find yourself using while true loops or blocking the fiber completely with a while loop, make sure you call

```lua
void SYSTEM_WAIT()
```

at the end of the loop's body, to ensure you don't clog up the game.

## Types
The lua API uses types and enums to describe several features within the game.

Types of objects:
```lua
CAR = 0,
BIKE = 1,
BOAT = 2,
DOOR = 3,
HELI = 4,
OBJECT = 5,
PED = 6,
PICKUP = 7,
PICKUP_PLACEMENT = 8,
PLANE = 9,
SUBMARINE = 10,
PLAYER = 11,
TRAILER = 12,
TRAIN = 13,
```
