# Instalation

1. Download latest release from keymaster/cfx.re [portal](https://portal.cfx.re/assets/granted-assets?page=1\&sort=asset.updated_at\&direction=asc)
2. Extract that in your resource folder
3. Ensure it after ox\_lib and all depencies: `ensure q-mechanicjob`
4.  Add items into ox\_invenory/data/items.lua:

    ```lua
    	['enginefixkit'] = {
    		label = "Engine Repair Kit",
    		weight = 500,
    		stack = true,
    		close = true,
    	},

    	['bodyrepairkit'] = {
    		label = "Body Repair Kit",
    		weight = 500,
    		stack = true,
    		close = true,
    	},

    	['tyrerepkit'] = {
    		label = "Tyre Repair Kit",
    		weight = 500,
    		stack = true,
    		close = true,
    	},

    	['sponge'] = {
    		label = "Sponge",
    		weight = 100,
    		stack = true,
    		close = true,
    	},

    	['mehmembercard'] = {
    		label = "Mechanic Membership",
    		degrade = 43200,
    		weight = 50,
    		stack = false,
    	},
    ```
5. You are all done

### &#x20; <a href="#in-hrs_base_building" id="in-hrs_base_building"></a>
