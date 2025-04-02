---
description: Crafting implementation
---

# Crafting Setup ox\_inventory

First Thanks to HRS for this crafting integration [HRS Docs](https://hrs-scripts.gitbook.io/hrs-scripts-doc/hrs-base-building/snippets/crafting-related/qb-esx-adding-ox_inventory-crafting-in-base-building)

1. **ox\_inventory\modules\crafting\server.lua**

```
lib.callback.register('ox_inventory:craftItem', function(source, id, index, recipeId, toSlot)
	local left, bench = Inventory(source), CraftingBenches[id]

	if bench then
		local groups = bench.groups
-- DELETE THIS	local coords = shared.target and bench.zones[index].coords or bench.points[index]

		if groups and not server.hasGroup(left, groups) then return end
-- DELETE THIS	if #(GetEntityCoords(GetPlayerPed(source)) - coords) > 10 then return end 
```

**ox\_inventory\modules\crafting\server.lua**

```
lib.callback.register('ox_inventory:openCraftingBench', function(source, id, index)
	local left, bench = Inventory(source), CraftingBenches[id]

	if not left then return end

	if bench then
		local groups = bench.groups
-- DELETE THIS  local coords = getCraftingCoords(source, bench, index)

-- DELETE THIS  if not coords then return end

		if groups and not server.hasGroup(left, groups) then return end
-- DELETE THIS  if #(GetEntityCoords(GetPlayerPed(source)) - coords) > 10 then return end

		if left.open and left.open ~= source then
			local inv = Inventory(left.open) --[[@as OxInventory]]

			-- Why would the player inventory open with an invalid target? Can't repro but whatever.
			if inv?.player then
				inv:closeInventory()
			end
		end

		left:openInventory(left)
	end

	return { label = left.label, type = left.type, slots = left.slots, weight = left.weight, maxWeight = left.maxWeight }
end)
```

**ox\_inventory\client.lua**

```
function client.openInventory(inv, data)
	if invOpen then
		if not inv and currentInventory.type == 'newdrop' then
			return client.closeInventory()
		end

		if IsNuiFocused() then
			if inv == 'container' and currentInventory.id == PlayerData.inventory[data].metadata.container then
				return client.closeInventory()
			end

			if currentInventory.type == 'drop' and (not data or currentInventory.id == (type(data) == 'table' and data.id or data)) then
				return client.closeInventory()
			end

			if inv ~= 'drop' and inv ~= 'container' then
				if (data?.id or data) == currentInventory?.id then
					-- Triggering exports.ox_inventory:openInventory('stash', 'mystash') twice in rapid succession is weird behaviour
					return warn(("script tried to open inventory, but it is already open\n%s"):format(Citizen.InvokeNative(`FORMAT_STACK_TRACE` & 0xFFFFFFFF, nil, 0, Citizen.ResultAsString())))
				else
					return client.closeInventory()
				end
			end
		end
	elseif IsNuiFocused() then
		-- If triggering event from another nui such as qtarget, may need to wait for focus to end
		Wait(100)

		if IsNuiFocused() then
			warn('other scripts have nui focus and may cause issues (e.g. disable focus, prevent input, overlap inventory window)')
		end
	end

	if inv == 'dumpster' and cache.vehicle then
		return lib.notify({ id = 'inventory_right_access', type = 'error', description = locale('inventory_right_access') })
	end

	if canOpenInventory() then
		local left, right

		if inv == 'player' then
			local targetId, targetPed

			if not data then
				targetId, targetPed = Utils.GetClosestPlayer()
				data = targetId and GetPlayerServerId(targetId)
			else
				local serverId = type(data) == 'table' and data.id or data

				if serverId == cache.serverId then return end

				targetId = serverId and GetPlayerFromServerId(serverId)
				targetPed = targetId and GetPlayerPed(targetId)
			end

			local targetCoords = targetPed and GetEntityCoords(targetPed)

			if not targetCoords or #(targetCoords - GetEntityCoords(playerPed)) > 1.8 or not (client.hasGroup(shared.police) or canOpenTarget(targetPed)) then
				return lib.notify({ id = 'inventory_right_access', type = 'error', description = locale('inventory_right_access') })
			end
		end

		if inv == 'shop' and invOpen == false then
			if cache.vehicle then
				return lib.notify({ id = 'cannot_perform', type = 'error', description = locale('cannot_perform') })
			end

			left, right = lib.callback.await('ox_inventory:openShop', 200, data)
		elseif inv == 'crafting' then
			if cache.vehicle then
				return lib.notify({ id = 'cannot_perform', type = 'error', description = locale('cannot_perform') })
			end

			left = lib.callback.await('ox_inventory:openCraftingBench', 200, data.id, data.index)

			if left then
				right = CraftingBenches[data.id]

				if not right?.items then return end

-- DELETE THIS 			local coords, distance

-- DELETE THIS 			if not right.zones and not right.points then
-- DELETE THIS 				coords = GetEntityCoords(cache.ped)
-- DELETE THIS 				distance = 2
-- DELETE THIS 			else
-- DELETE THIS 				coords = shared.target == 'ox_target' and right.zones and right.zones[data.index].coords or right.points and right.points[data.index]
-- DELETE THIS 				distance = coords and shared.target == 'ox_target' and right.zones[data.index].distance or 2
-- DELETE THIS 			end

				right = {
					type = 'crafting',
					id = data.id,
					label = right.label or locale('crafting_bench'),
					index = data.index,
					slots = right.slots,
					items = right.items,
-- DELETE THIS 				coords = coords,
-- DELETE THIS 				distance = distance
				}
			end
```

**ox\_inventory\client.lua**

```
AddEventHandler('ox_inventory:craftingEvent', function(id)
    exports.ox_inventory:openInventory('crafting', { id = id, index = id})
end) -------------- ADD THIS EVENT ON THE BOTTOM

--- the id will be the crafting table number in ox_inventory/data/crafting.lua
```

**ox\_inventory\data\crafting.lua (this are just examples of 3 crafting tables)**

```
return {
	{
		items = {
			{
				name = 'model_base_wood',
				ingredients = {
					wood = 5
				},
				duration = 5000,
				count = 2,
			},
			{
				name = 'model_door_wood',
				ingredients = {
					wood = 5
				},
				duration = 5000,
				count = 2,
			},
		},
		points = {
		},
		zones = {
		},
		blip = { id = 566, colour = 31, scale = 0.8 },
	}, -- INDEX = 1

	{
		items = {
			{
				name = 'bandage',
				ingredients = {
					cloth = 5
				},
				duration = 5000,
				count = 2,
			},
			{
				name = 'medikit',
				ingredients = {
					bandage = 2
				},
				duration = 5000,
				count = 2,
			},
		},
		points = {
		},
		zones = {
		},
		blip = { id = 566, colour = 31, scale = 0.8 },
	}, -- INDEX = 2

	{
		items = {
			{
				name = 'weapon_pistol',
				ingredients = {
					scrapmetal = 5,
					WEAPON_HAMMER = 0.05
				},
				duration = 5000,
				count = 2,
			},
			{
				name = 'weapon_knife',
				ingredients = {
					scrapmetal = 5,
					WEAPON_HAMMER = 0.05
				},
				duration = 5000,
				count = 2,
			},
		},
		points = {
		},
		zones = {
		},
		blip = { id = 566, colour = 31, scale = 0.8 },
	}, -- INDEX = 3

}
```
