local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local PlaceID = game.PlaceId
local currentJobId = game.JobId

local function teleportToNewServer()
    local servers = {}
    local cursor

    repeat
        local url = ("https://games.roblox.com/v1/games/%d/servers/Public?sortOrder=Asc&limit=100"):format(PlaceID)
        if cursor then
            url = url .. "&cursor=" .. cursor
        end

        local response = game:HttpGet(url)
        local data = HttpService:JSONDecode(response)
        servers = data.data
        cursor = data.nextPageCursor

        for _, server in ipairs(servers) do
            if server.playing < server.maxPlayers and server.id ~= currentJobId then
                TeleportService:TeleportToPlaceInstance(PlaceID, server.id, game.Players.LocalPlayer)
                return true
            end
        end
    until not cursor

    return false
end

while true do
    local success = teleportToNewServer()
    if not success then
        print("Nenhum servidor disponível para troca no momento.")
    end
    wait(180) -- espera 3 minutos
end
