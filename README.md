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
- math
- table 
- coroutine 
- utf8
- bit32

**You need to be really careful about what scripts you run if you turn off OS SAFE MODE in the menu.**

## OS Utils
```lua
-- Gets the current OS tick count in milliseconds.
u32 get_current_time_ms()

-- Checks if a file exists (full path)
bool does_file_exist(full_path)

-- Checks if a directory exists (full path)
bool does_dir_exist(full_path)

-- Returns the full path to the menu directory. Ends with \
string get_menu_dir()

-- Returns the full path to the scripts directory. Ends with \
string get_scripts_dir()
```

## Logging
```lua
-- Logs a message to the Rebound console.
void log_info(message)
-- Logs an error to the Rebound console.
void log_error(message)
```

## Game Classes

These classes will generally take an entity that already exists (a ped, a vehicle, a generic entity like an object) and will let you construct an object that simplifies and wraps around the game's natives.

# Vector3
```lua
Vector3 Vector3.new(float x, float y, float z)
Vector3 construct_vector3(address)
address allocate_vector3()
```

# entity_t
```lua
-- Constructs a new entity_t object.
entity_t entity_t.new(Entity)

-- Returns the health of the entity.
int entity_t:get_health()

-- Sets the health of the entity.
void entity_t:set_health(int)

-- Sets the entity as invincible.
void entity_t:set_invincible(bool)

-- Requests control of the entity.
void entity_t:request_control()

-- Deletes the entity.
void entity_t:delete_entity()

-- Pushes the entity away from a specified point.
void entity_t:push_away_from(float, float, float)

-- Checks if the entity exists.
bool entity_t:exists()

-- Checks if the entity is touching another entity.
bool entity_t:is_touching(entity_t)

-- Returns the collision normal of the entity.
vector3 entity_t:get_collision_normal()

-- Checks if the entity is a pedestrian.
bool entity_t:is_ped()

-- Checks if the entity is a vehicle.
bool entity_t:is_vehicle()

-- Checks if the entity is on foot.
bool entity_t:is_on_foot()

-- Checks if the entity is visible.
bool entity_t:is_visible()

-- Sets the alpha of the entity.
void entity_t:set_alpha(int)

-- Fades the entity in.
void entity_t:fade_in()

-- Fades the entity out.
void entity_t:fade_out()

-- Checks if the entity is in water.
bool entity_t:is_in_water()

-- Returns the coordinates of the entity.
Vector3 entity_t:get_coords()

-- Sets the coordinates of the entity.
void entity_t:set_coords(Vector3)

-- Sets the coordinates of the entity and keeps the vehicle.
void entity_t:set_coords_keep_vehicle(Vector3)

-- Returns the heading of the entity.
float entity_t:get_heading()

-- Sets the heading of the entity.
void entity_t:set_heading(float)

-- Returns the rotation of the entity.
vector3 entity_t:get_rotation()

-- Sets the rotation of the entity.
void entity_t:set_rotation(Vector3)

-- Returns the world coordinates of the entity.
vector3 entity_t:get_entity_world_coords(Vector3 offset)

-- Sets the velocity of the entity.
void entity_t:set_velocity(Vector3)

-- Sets the maximum speed of the entity.
void entity_t:set_max_speed(float)

-- Freezes the position of the entity.
void entity_t:freeze_position(bool)

-- Sets the collision of the entity.
void entity_t:set_collision(bool)

-- Maximizes the mechanical condition of the entity.
void entity_t:max_mech()

-- Maximizes the outer condition of the entity.
void entity_t:max_out(bool mech)

-- Fully downgrades the entity if it's a vehicle.
void entity_t:fully_downgrade()

-- Randomizes the entity.
void entity_t:randomize()

-- Converts the entity to a pedestrian.
ped entity_t:to_ped()

-- Converts the entity to a vehicle.
vehicle entity_t:to_vehicle()

-- Returns the underlying game entity.
Entity entity_t:get_entity()
```

# ped_t

This class inherits from entity_t.

```lua
-- Constructs a new pedestrian (explicit, recommended)
-- Returns: ped_t
ped_t construct_ped(Ped ped)

-- Constructs a new pedestrian.
-- Returns: ped_t
ped_t ped_t.new(Ped ped)

-- Returns nearby pedestrians.
-- Returns: table
table ped_t:get_nearby_peds()

-- Returns the nearest pedestrian.
-- Returns: ped_t
ped_t ped_t:get_nearest_ped()

-- Clones the pedestrian.
-- Returns: ped_t
ped_t ped_t:clone()

-- Returns the model of the pedestrian.
-- Returns: Hash
Hash ped_t:get_model()

-- Resurrects the pedestrian.
void ped_t:resurrect()

-- Checks if the pedestrian is in a vehicle.
-- Returns: bool
bool ped_t:is_in_vehicle()

-- Checks if the pedestrian is getting in a vehicle.
-- Returns: bool
bool ped_t:getting_in_vehicle()

-- Returns the vehicle the pedestrian is in.
-- Returns: vehicle_t
vehicle_t ped_t:get_vehicle()

-- Sets the pedestrian into a vehicle.
void ped_t:set_into_vehicle(vehicle_t, int)

-- Sets the seatbelt of the pedestrian.
void ped_t:set_seatbelt(bool)

-- Checks if the pedestrian is dead.
-- Returns: bool
bool ped_t:is_dead()

-- Checks if the pedestrian is aiming.
-- Returns: bool
bool ped_t:is_aiming()

-- Checks if the pedestrian is shooting.
-- Returns: bool
bool ped_t:is_shooting()

-- Checks if the pedestrian is a player.
-- Returns: bool
bool ped_t:is_player()

-- Draws a marker on the pedestrian (to be called every frame).
void ped_t:draw_marker()

-- Returns the zone the pedestrian is in.
-- Returns: string
string ped_t:get_zone()

-- Returns the ammo of the pedestrian.
-- Returns: int
int ped_t:get_ammo()

-- Gives the pedestrian a weapon.
void ped_t:give_weapon(Hash hash, int ammo, bool force)
void ped_t:give_weapon(string model, int ammo, bool force)

-- Returns the weapon of the pedestrian.
-- Returns: Hash
void ped_t:get_weapon()

-- Sets the current weapon of the pedestrian.
void ped_t:set_current_weapon(Hash hash)

-- Sets infinite ammo for the pedestrian.
void ped_t:set_infinite_ammo(bool status)

-- Shoots a bullet between two coordinates.
void ped_t:shoot_bullet_between_coords(Vector3 source, Vector3 target, Hash weapon, int damage, bool audible, bool visible, float speed)

-- Shoots at the specified coordinates.
void ped_t:shoot_at_coords(Vector3 coords)

-- Returns the impact coordinates of the pedestrian's last shot.
-- Returns: Vector3
Vector3 ped_t:get_impact_coords()

-- Returns the coordinates of the specified bone on the pedestrian.
-- Returns: Vector3
Vector3 ped_t:get_bone_coords(int bone, float offsetX, float offsetY, float offsetZ)

-- Clears all tasks of the pedestrian immediately.
void ped_t:clear_tasks_immediately()

-- Makes the pedestrian ragdoll.
void ped_t:ragdoll(int time1, int time2, int type)

-- Performs a crash action.
void ped_t:action_crash()

-- Starts a particle effect on the specified bone of the pedestrian.
void ped_t:start_particle_fx_on_bone(string name, int bone)

-- Plays an animation on the pedestrian.
void ped_t:play_animation(string dict, string name)

-- Performs an object crash action.
void ped_t:object_crash()

-- Returns the pedestrian.
-- Returns: Ped
Ped ped_t:get_ped()

-- Checks if the pedestrian has a weapon in hand.
-- Returns: bool
bool ped_t:has_weapon_in_hand()

-- Returns the weapon in the pedestrian's hand.
-- Returns: Hash
Hash ped_t:get_weapon_in_hand()

-- Refills all weapons' ammo in a ped's inventory
void ped_t:ammo_refill()

-- Refills a single weapon's ammo in a ped's inventory
void ped_t:fill_weapon_ammo_if_present(hash)
```

# vehicle_t

This class inherits from entity_t.

```lua
-- Constructs a new vehicle (explicit, recommended)
-- Returns: vehicle_t
vehicle_t construct_vehicle(Vehicle vehicle)

-- Constructs a new vehicle.
-- Returns: vehicle_t
vehicle_t vehicle_t:new(Vehicle)

-- Spawns a vehicle by its hash.
vehicle_t vehicle_t:spawn_by_hash(Hash)

-- Returns the vehicle.
-- Returns: Vehicle
Vehicle vehicle_t:get_vehicle()

-- Returns the pedestrian in the specified seat of the vehicle.
-- Returns: ped_t
ped_t vehicle_t:get_ped_in_seat(int)

-- Returns the first available seat in the vehicle.
-- Returns: int
int vehicle_t:get_free_seat()

-- Returns the model of the vehicle.
-- Returns: Hash
Hash vehicle_t:get_model()

-- Applies a force to the vehicle.
void vehicle_t:apply_force(Vector3, Vector3, ForceType)
void vehicle_t:apply_force(Vector3, ForceType)

-- Sets the forward speed of the vehicle.
void vehicle_t:set_forward_speed(float)

-- Sets the engine power of the vehicle.
void vehicle_t:set_engine_power(float)

-- Sets the engine torque of the vehicle.
void vehicle_t:set_engine_torque(float)

-- Sets the gravity of the vehicle.
void vehicle_t:set_gravity(float)

-- Drives the vehicle to the specified coordinates.
void vehicle_t:drive_to(Vector3)

-- Drives the vehicle to kill the specified pedestrian.
void vehicle_t:drive_to_kill(ped_t)

-- Returns the maximum speed of the vehicle.
-- Returns: float
float vehicle_t:get_max_speed()

-- Returns the current speed of the vehicle.
-- Returns: float
float vehicle_t:get_speed()

-- Checks if the vehicle is on all wheels.
-- Returns: bool
bool vehicle_t:is_on_all_wheels()

-- Drives the vehicle.
void vehicle_t:drive()

-- Repairs the vehicle.
void vehicle_t:repair()

-- Sets the friction override of the vehicle.
void vehicle_t:set_friction_override(float)

-- Sets the vehicle on the ground.
void vehicle_t:set_on_ground(bool)

-- Checks if the vehicle is upright.
-- Returns: bool
bool vehicle_t:is_upright()

-- Sets the dirtiness of the vehicle.
void vehicle_t:set_dirtiness(float)

-- Burns the shell of the vehicle.
void vehicle_t:burn_shell()

-- Activates the anti-lock system of the vehicle.
void vehicle_t:anti_lock()

-- Empties the vehicle.
void vehicle_t:empty()

-- Clones the vehicle.
-- Returns: vehicle_t
vehicle_t vehicle_t:clone()

-- Flips the vehicle.
void vehicle_t:flip()

-- Performs a kickflip with the vehicle.
void vehicle_t:kickflip()

-- Cycles through smoke particles of the vehicle.
void vehicle_t:smoke_cycle()

-- Ticks the rainbow effect of the vehicle.
void vehicle_t:rainbow_tick()

-- Turns on the engine of the vehicle.
void vehicle_t:engine_on()

-- Boosts the horn of the vehicle.
void vehicle_t:horn_boost()

-- Performs a front flip with the vehicle.
void vehicle_t:front_flip()

-- Performs a back flip with the vehicle.
void vehicle_t:back_flip()

-- Performs a kick flip with the vehicle.
void vehicle_t:kick_flip()

-- Performs a heel flip with the vehicle.
void vehicle_t:heel_flip()

-- Performs a bunny hop with the vehicle.
void vehicle_t:bunny_hop()

-- Sets the invincibility status of the vehicle.
void vehicle_t:set_invincible(bool)

-- Sets the invisibility status of the vehicle.
void vehicle_t:set_invisibility(bool)

-- Reduces the grip of the vehicle.
void vehicle_t:reduce_grip()

-- Returns the ID of the vehicle.
-- Returns: int
int vehicle_t:get_id()
```

## Classes Example
```lua
-- Kill your own ped
local entity = entity_t.new(PLAYER_GET_PLAYER_PED_SCRIPT_INDEX(PLAYER_PLAYER_ID()))
entity:set_health(0)
```

## Memory
```lua
-- Allocates a block of memory with the relative size
uint64_t allocate_memory(uint64_t size)
-- Frees a block of memory given the address
void free_memory(uint64_t address)

-- Writes a byte to the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- value: byte, the value to be written
void write_byte(string moduleName, uint32_t offset, uint8_t value)

-- Reads a byte from the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- return: byte, the value read from the address
byte read_byte(string moduleName, uint32_t offset)

-- Writes an int to the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- value: integer, the value to be written
void write_int(string moduleName, uint32_t offset, int value)

-- Reads an int from the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- return: int, the value read from the address
int read_int(string moduleName, uint32_t offset)

-- Writes a float to the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- value: floating, the value to be written
void write_float(string moduleName, uint32_t offset, float value)

-- Reads a float from the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- return: float, the value read from the address
float read_float(string moduleName, uint32_t offset)

-- Writes a bool to the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- value: boolean, the value to be written
void write_bool(string moduleName, uint32_t offset, bool value)

-- Reads a bool from the specified module at a given offset.
-- moduleName: string, the name of the module
-- offset: uint32_t, the offset from the module base address
-- return: bool, the value read from the address
bool read_bool(string moduleName, uint32_t offset)

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
void set_local_int(script_hash, index, value)

-- Writes a floating point value to a local's address
void set_local_float(script_hash, index, value)

-- Sends a script event to another player (First argument must be the event, other arguments are the event's parameters)
void trigger_script_event(int player_index, int arguments_amount, int[] arguments)

-- Gets the index of the current session host.
int get_session_host()

-- Checks if the player at the given index is the session host
bool is_session_host(index)

-- Breakup kicks the target
void breakup(player_id)

-- Master kicks the target
void master_kick(player_id)

-- Master crashes the target
void master_crash(player_id)
```

## Objects
```lua
-- Spawns a world object
-- hash (numeric model value)
-- location (Vector3)
-- is_networked (whether others can see it)
Object spawn_world_object(hash, location, is_networked)
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
-- Internally calls the STAT_GET_type native and retrieves the value from memory for you.
int stat_get_int(int statHash)
bool stat_get_bool(int statHash)
float stat_get_float(int statHash)

-- Calls STAT_SET_MASKED_INT with the specified parameters
bool stat_set_masked_int(int statHash, int data, int shift, int bitNumber, bool save)

-- Calls STAT_SET_BOOL_MASKED with the specified parameters
bool stat_set_bool_masked(int statHash, bool value, int mask, bool save)

-- Sets a packed bool stat, overrideCharacterSlot should always be -1 for
-- the current character. It can be 0 for the first MP character
-- and 1 for the second MP character.
void set_packed_stat_bool(int statHash, int value, int overrideCharacterSlot)

-- Sets a packed int stat, overrideCharacterSlot should always be -1 for
-- the current character. It can be 0 for the first MP character
-- and 1 for the second MP character.
void set_packed_stat_int(int statHash, bool value, int overrideCharacterSlot)

-- Sends a message in the game chat (Takes into account impersonation settings as well)
void send_chat_message(message)
```

## Internet
You can use these APIs to communicate with the internet.

They will only work if the internet safe mode has been disabled.

By default, the menu will keep internet functionality disabled by enabling the safe mode.

The requests will be sent in a separate thread, and the current script fiber will be put to sleep in order to avoid game freezes while a response is received from the server.

```lua
-- Sends a GET request, returns an object containing two properties: status_code and result.
-- The status code represents the status code returned by the server.
-- The result represents the body of the server response.
response_result send_get(url, body)

-- Sends a POST request, returns an object containing two properties: status_code and result.
-- The status code represents the status code returned by the server.
-- The result represents the body of the server response.
response_result send_post(url, body)
```

## Graphics
```lua
-- Will load a custom YTD, the name should be a full texture name with .ytd included, 
-- whereas the full texture path should contain the full path to the .ytd file to load.
bool load_texture(string full_texture_path, string texture_name)
```

## Entities
```lua
-- Will return all vehicles / peds / objects / pickups in the specified range, starting from the specified coordinates.
table get_all_vehicles(float range, Vector3 coordinates)
table get_all_peds(float range, Vector3 coordinates)
table get_all_objects(float range, Vector3 coordinates)
table get_all_pickups(float range, Vector3 coordinates)

-- Will return all entities without any differentiation in the specified range, starting from the specified coordinates.
table get_all_entities(float range, Vector3 coordinates)

-- Will take any raw entity as parameter (Vehicle / Ped / Object / Entity) and will return the raw pointer to the
-- in-memory representation of the entity. Useful for advanced scripts modifying fields in entity classes.
uint64_t handle_to_ptr(int entity)
```

## Threading
```lua
-- Registers a function to be called before the script is unloaded
void register_on_unload(function);

-- Registers a function to be called on tick in the menu's fiber pools
void register_on_tick(function);

-- Start a new fiber to run your code in, remember to SYSTEM_WAIT() at the end of every while (true) loop.
void create_fiber(function);

-- Queues a job in the rebound fibers to be executed without blocking the current script.
void queue_job(function);

-- A variant of the wait native, allows the current fiber to sleep for a fixed amount of milliseconds.
void SYSTEM_WAIT_MS(milliseconds);
```

## Tunables
```lua
-- Will access the tunables and return the requested value.
bool tunables.get_bool(hash)
int tunables.get_int(hash)
float tunables.get_float(hash)

-- Will access the tunables and set their values (Will return the original value).
bool tunables.set_bool(hash, value)
int tunables.set_int(hash, value)
float tunables.set_float(hash, value)
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
UNK = 14
```