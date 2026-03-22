RegisterServerEvent('custom_repair:attemptRepair')
AddEventHandler('custom_repair:attemptRepair', function()
    local src = source

    -- Generic way to get player inventory count (works with ox_inventory, qs, ps, etc.)
    local moneyCount = exports.ox_inventory:GetItemCount(src, Config.MoneyItem) 
    -- If not using ox_inventory, some inventories use: exports['qb-inventory']:GetItemCount(src, Config.MoneyItem) or similar

    if moneyCount and moneyCount >= Config.RepairCost then
        -- Remove the money item
        exports.ox_inventory:RemoveItem(src, Config.MoneyItem, Config.RepairCost)
        
        TriggerClientEvent('custom_repair:startRepair', src)
    else
        TriggerClientEvent('okokNotify:Alert', src, "Repair", 
            "You need $" .. Config.RepairCost .. " in cash (inventory) to repair your vehicle!", 5000, 'error')
    end
end)
