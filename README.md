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
The stock LUA libraries rebound implements are:
- base
- string
- io
- os
- math 

**You need to be really careful about what scripts you run.**

## Logging
```lua
-- Logs a message to the rebound console.
void log_info(message)
-- Logs an error to the rebound console.
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

-- Registers a function to be called every time rebound
-- receives a script event.
--
-- Return false to deny the event, return true to let it through.
-- 
-- You can build custom protections with this.
--
-- Example function that prints all event IDs received:
-- 
-- function script_event_hook(player_index, event_parameters, amount_of_parameters) 
--     event_id = event_parameters[1]
--     target = event_parameters[2] -- Usually equal to PLAYER_PLAYER_ID
--     -- The maximum parameter you can acess is event_parameters[amount_of_parameters]
--     log_info(event_id)
--     -- return false to stop the event from getting receieved, true lets it through
--     return true
-- end
-- 
void add_script_event_callback(hook)

-- Registers a function to be called every time rebound
-- receives a net event.
--
-- Return false to deny the event, return true to let it through.
-- 
-- You can build custom protections with this.
--
-- Example function:
-- 
-- function net_event_hook(sender_index, receiver_index, net_event_id)
--     -- return false to stop the event from getting through
--     return true
-- end
-- 
void add_net_event_callback(hook)

-- Registers a function to be called every time rebound
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

Types of net events (they start at 0, with OBJECT_ID_FREED_EVENT being 0, and increment by one per-line):
Credit to Fivem for these.
```lua
OBJECT_ID_FREED_EVENT,
OBJECT_ID_REQUEST_EVENT,
ARRAY_DATA_VERIFY_EVENT,
SCRIPT_ARRAY_DATA_VERIFY_EVENT,
REQUEST_CONTROL_EVENT,
GIVE_CONTROL_EVENT,
WEAPON_DAMAGE_EVENT,
REQUEST_PICKUP_EVENT,
REQUEST_MAP_PICKUP_EVENT,
GAME_CLOCK_EVENT,    // 2372: Removed (index now empty placeholder)
GAME_WEATHER_EVENT,  // 2372: Removed (index now empty placeholder)
RESPAWN_PLAYER_PED_EVENT,
GIVE_WEAPON_EVENT,
REMOVE_WEAPON_EVENT,
REMOVE_ALL_WEAPONS_EVENT,
VEHICLE_COMPONENT_CONTROL_EVENT,
FIRE_EVENT,
EXPLOSION_EVENT,
START_PROJECTILE_EVENT,
UPDATE_PROJECTILE_TARGET_EVENT,
REMOVE_PROJECTILE_ENTITY_EVENT,
BREAK_PROJECTILE_TARGET_LOCK_EVENT,
ALTER_WANTED_LEVEL_EVENT,
CHANGE_RADIO_STATION_EVENT,
RAGDOLL_REQUEST_EVENT,
PLAYER_TAUNT_EVENT,
PLAYER_CARD_STAT_EVENT,
DOOR_BREAK_EVENT,
SCRIPTED_GAME_EVENT,
REMOTE_SCRIPT_INFO_EVENT,
REMOTE_SCRIPT_LEAVE_EVENT,
MARK_AS_NO_LONGER_NEEDED_EVENT,
CONVERT_TO_SCRIPT_ENTITY_EVENT,
SCRIPT_WORLD_STATE_EVENT,
CLEAR_AREA_EVENT,
CLEAR_RECTANGLE_AREA_EVENT,
NETWORK_REQUEST_SYNCED_SCENE_EVENT,
NETWORK_START_SYNCED_SCENE_EVENT,
NETWORK_STOP_SYNCED_SCENE_EVENT,
NETWORK_UPDATE_SYNCED_SCENE_EVENT,
INCIDENT_ENTITY_EVENT,
GIVE_PED_SCRIPTED_TASK_EVENT,
GIVE_PED_SEQUENCE_TASK_EVENT,
NETWORK_CLEAR_PED_TASKS_EVENT,
NETWORK_START_PED_ARREST_EVENT,
NETWORK_START_PED_UNCUFF_EVENT,
NETWORK_SOUND_CAR_HORN_EVENT,
NETWORK_ENTITY_AREA_STATUS_EVENT,
NETWORK_GARAGE_OCCUPIED_STATUS_EVENT,
PED_CONVERSATION_LINE_EVENT,
SCRIPT_ENTITY_STATE_CHANGE_EVENT,
NETWORK_PLAY_SOUND_EVENT,
NETWORK_STOP_SOUND_EVENT,
NETWORK_PLAY_AIRDEFENSE_FIRE_EVENT,
NETWORK_BANK_REQUEST_EVENT,
REQUEST_DOOR_EVENT,
NETWORK_TRAIN_REPORT_EVENT,
NETWORK_TRAIN_REQUEST_EVENT,
NETWORK_INCREMENT_STAT_EVENT,
MODIFY_VEHICLE_LOCK_WORD_STATE_DATA,
MODIFY_PTFX_WORD_STATE_DATA_SCRIPTED_EVOLVE_EVENT,
REQUEST_PHONE_EXPLOSION_EVENT,
REQUEST_DETACHMENT_EVENT,
KICK_VOTES_EVENT,
GIVE_PICKUP_REWARDS_EVENT,
NETWORK_CRC_HASH_CHECK_EVENT,
BLOW_UP_VEHICLE_EVENT,
NETWORK_SPECIAL_FIRE_EQUIPPED_WEAPON,
NETWORK_RESPONDED_TO_THREAT_EVENT,
NETWORK_SHOUT_TARGET_POSITION,
VOICE_DRIVEN_MOUTH_MOVEMENT_FINISHED_EVENT,
PICKUP_DESTROYED_EVENT,
UPDATE_PLAYER_SCARS_EVENT,
NETWORK_CHECK_EXE_SIZE_EVENT,
NETWORK_PTFX_EVENT,
NETWORK_PED_SEEN_DEAD_PED_EVENT,
REMOVE_STICKY_BOMB_EVENT,
NETWORK_CHECK_CODE_CRCS_EVENT,
INFORM_SILENCED_GUNSHOT_EVENT,
PED_PLAY_PAIN_EVENT,
CACHE_PLAYER_HEAD_BLEND_DATA_EVENT,
REMOVE_PED_FROM_PEDGROUP_EVENT,
REPORT_MYSELF_EVENT,
REPORT_CASH_SPAWN_EVENT,
ACTIVATE_VEHICLE_SPECIAL_ABILITY_EVENT,
BLOCK_WEAPON_SELECTION,
NETWORK_CHECK_CATALOG_CRC,
```

## Example
Here's an example that uses just about everything the api has to offer, including an example of an infinite loop that can end at the press of a button, a script event hook to log all script events, and a few examples on how to add features to the menu.

```lua
function print_position(index)
    name = PLAYER_GET_PLAYER_NAME(index)
    log_info(name)
    vector = ENTITY_GET_ENTITY_COORDS(PLAYER_GET_PLAYER_PED_SCRIPT_INDEX(index), 0)
    log_info(tostring(vector.x))
    log_info(tostring(vector.y))
    log_info(tostring(vector.z))
end

function print_hello()
    -- No need to check whether the feature is on or off, this is only
    -- called when it's on, Rebound handles that for you.
    log_info("Hello")
end

function object_hook(player_index, object_type, object_hash) 
    -- return false to stop the object from getting synced
    return true
end

function script_event_hook(player_index, event_parameters, amount_of_parameters) 
    event_id = event_parameters[1]
    target = event_parameters[2] -- Usually equal to PLAYER_PLAYER_ID
    -- The maximum parameter you can acess is event_parameters[amount_of_parameters]
    log_info(event_id)
    -- return false to stop the event from getting receieved, true lets it through
    return true
end

function net_event_hook(sender_index, receiver_index, net_event_id)
    -- return false to stop the event from getting through
    return true
end

-- Register a one-click player feature
add_player_function("Test", "Print position", print_position)
-- Register a toggleable player feature, make sure the function takes an index as parameter!
add_player_toggle_function("Test", "Print position but more", print_position)

-- Register a one-click feature
add_function("Test", "Print hello", print_hello)
-- Register a toggleable feature
add_toggle_function("Test", "Print hello but more", print_hello)

-- Register the script event hook to catch script events
add_script_event_callback(script_event_hook)
-- Register the net event hook to catch net events
add_net_event_callback(net_event_hook)
-- Register the object hook to catch all objects
add_object_callback(object_hook)

log_info("Lua features registered!")

while (true) 
do
    -- If you do plan on unloading your script,
    -- please make sure you exit first!

    -- You do not want to see your desktop. :)
    if (is_key_pressed(0x70)) then
        log_info("loop stopped!")
        return
    end

    log_info("on loop!")

    SYSTEM_WAIT()
end
```
