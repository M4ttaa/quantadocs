---
description: Configuration of script
---

# Configuration

## Mechanic Workshop Script

### Description

This script provides a mechanic workshop system for FiveM, allowing players to perform various repair, crafting, and management tasks within the workshop.

### Features

* Configurable mechanic workshop locations
* Crafting system
* Inventory stash
* Sponge cleaning system
* Job uniforms and cloakroom
* Garage system for work vehicles
* Custom notifications

### Configuration

The script settings are defined in `config.lua`. Below are the main configurable options:

#### General Settings

```lua
Config.Debug = true  -- Enable or disable debug mode
Config.Locale = 'en'  -- Set language locale
```

#### Items

Define the available items for use in the workshop:

```lua
Config.Items = {
    enginefixkit = "enginefixkit",
    bodyrepairkit = "bodyrepairkit",
    tyrerepkit = "tyrerepkit",
    sponge = "sponge",
    mehmembercard = "mehmembercard",
}
```

#### Progress Time

Set the duration (in milliseconds) for repair or cleaning actions:

```lua
Config.Progress = {
    sponge = 6000,
    tyrerepkit = 6000,
    body = 6000,
    engine = 6000
}
```

#### Workshops

Define mechanic workshop locations and functionalities:

```lua
Config.Workshops = {
    lscustoms = {
        job = "mechanic",  -- Required job to access the workshop
        label = 'Los Santos Customs',

        pos = vector3(-1621.1738, -827.9448, 9.9199), -- Workshop location
        
        blip = {
            sprite = 72,
            display = 4,
            color = 3,
            scale = 0.6,
        },
        }
}
```

Each feature inside a workshop can be disabled by setting it to `false`, for example:

```lua
craft = false  -- Disables crafting functionality
```

#### Available Functionalities

* **Duty**: Allows mechanics to clock in and out.
* **Crafting**: Mechanics can craft items if enabled.
* **Stash**: A storage system for workshop items.
* **Sponge Cleaning**: Players can use a sponge to clean vehicles.
* **Cloakroom**: Allows players to change into mechanic uniforms.
* **Garage**: Mechanic work vehicles can be spawned.

### Dependencies

* `ox_inventory`

### Usage

To use this script, add it to your FiveM resources and configure the settings in `config.lua` according to your needs. Restart your server for changes to take effect.

### Notifications

Notifications are handled using:

```lua
function Notify(title, desc, type)
    lib.notify({title = title, description = desc, type = type})
end
```

### License

This script is provided as-is. Modify and use it at your own discretion.

### Support

For support, please refer to the script's documentation or community forums.
