local repairing = false
local inZone = false
local uiOpen = false

-- OKOK TEXT UI
local function ShowUI(text)
    if not uiOpen then
        exports.okokTextUI:Open(text, 'darkblue', 'right')
        uiOpen = true
    end
end

local function HideUI()
    if uiOpen then
        exports.okokTextUI:Close()
        uiOpen = false
    end
end

-- Create blips and markers
Citizen.CreateThread(function()
    for k, zone in pairs(Config.RepairZones) do
        if zone.blip then
            local blip = AddBlipForCoord(zone.coords.x, zone.coords.y, zone.coords.z)
            SetBlipSprite(blip, zone.blipSprite)
            SetBlipDisplay(blip, 4)
            SetBlipScale(blip, 0.8)
            SetBlipColour(blip, zone.blipColor)
            SetBlipAsShortRange(blip, true)
            BeginTextCommandSetBlipName("STRING")
            AddTextComponentString(zone.name)
            EndTextCommandSetBlipName(blip)
        end
    end

    while true do
        Citizen.Wait(500)
        local ped = PlayerPedId()
        local pedCoords = GetEntityCoords(ped)
        inZone = false

        for k, zone in pairs(Config.RepairZones) do
            local dist = #(pedCoords - zone.coords)
            if dist <= zone.radius then
                inZone = true

                -- SHOW UI WHEN IN ZONE
                ShowUI("Type /repair to repair your vehicle")

                if zone.marker then
                    DrawMarker(1, zone.coords.x, zone.coords.y, zone.coords.z - 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
                        zone.radius * 2, zone.radius * 2, 0.5, 0, 150, 255, 100, false, false, 2, nil, nil, false)
                end
                break
            end
        end

        -- HIDE UI WHEN LEAVING ZONE
        if not inZone then
            HideUI()
        end
    end
end)

RegisterCommand('repair', function()
    if repairing then
        exports['okokNotify']:Alert("Repair", "You are already repairing a vehicle!", 5000, 'error')
        return
    end

    if not inZone then
        exports['okokNotify']:Alert("Repair", "You must be at a mechanic shop to repair your vehicle!", 5000, 'error')
        return
    end

    local ped = PlayerPedId()
    if not IsPedInAnyVehicle(ped, false) then
        exports['okokNotify']:Alert("Repair", "You must be in a vehicle to repair it!", 5000, 'error')
        return
    end

    local vehicle = GetVehiclePedIsIn(ped, false)
    if GetPedInVehicleSeat(vehicle, -1) ~= ped then
        exports['okokNotify']:Alert("Repair", "You must be in the driver's seat!", 5000, 'error')
        return
    end

    TriggerServerEvent('custom_repair:attemptRepair')
end, false)

RegisterNetEvent('custom_repair:startRepair', function()
    repairing = true

    local ped = PlayerPedId()
    local vehicle = GetVehiclePedIsIn(ped, false)

    -- Freeze player and vehicle immediately
    FreezeEntityPosition(ped, true)
    FreezeEntityPosition(vehicle, true)
    SetVehicleEngineOn(vehicle, true, true, false)

    -- Lock vehicle in place
    SetVehicleHandbrake(vehicle, true)

    -- Disable vehicle controls
    DisableControlAction(0, 71, true)  -- Accelerate
    DisableControlAction(0, 72, true)  -- Brake/Reverse
    DisableControlAction(0, 59, true)  -- Steering left/right
    DisableControlAction(0, 60, true)  -- Steering up/down

    -- Wait for repair time
    Citizen.Wait(Config.RepairTime)

    -- Repair vehicle
    SetVehicleFixed(vehicle)
    SetVehicleDeformationFixed(vehicle)
    SetVehicleDirtLevel(vehicle, 0.0)
    SetVehicleEngineHealth(vehicle, 1000.0)
    SetVehicleBodyHealth(vehicle, 1000.0)

    -- Unfreeze vehicle and player
    FreezeEntityPosition(vehicle, false)
    FreezeEntityPosition(ped, false)
    SetVehicleHandbrake(vehicle, false)

    -- Re-enable controls
    EnableControlAction(0, 71, true)
    EnableControlAction(0, 72, true)
    EnableControlAction(0, 59, true)
    EnableControlAction(0, 60, true)

    -- Blue notification
    exports['okokNotify']:Alert("Repair", "Vehicle Repaired Successfully!", 5000, 'info')

    repairing = false
end)
