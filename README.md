repeat
    wait()
until game:IsLoaded()

local function Teleporter()
    local PlaceID = game.PlaceId
    local AllIDs = {}
    local foundAnything = ""
    local actualHour = os.date("!*t").hour
    local Deleted = false
    function TPReturner()
        local Site
        if foundAnything == "" then
            Site =
                game.HttpService:JSONDecode(
                game:HttpGet(
                    "https://games.roblox.com/v1/games/" .. PlaceID .. "/servers/Public?sortOrder=Asc&limit=100"
                )
            )
        else
            Site =
                game.HttpService:JSONDecode(
                game:HttpGet(
                    "https://games.roblox.com/v1/games/" ..
                        PlaceID .. "/servers/Public?sortOrder=Asc&limit=100&cursor=" .. foundAnything
                )
            )
        end
        local ID = ""
        if Site.nextPageCursor and Site.nextPageCursor ~= "null" and Site.nextPageCursor ~= nil then
            foundAnything = Site.nextPageCursor
        end
        local num = 0
        for i, v in pairs(Site.data) do
            local Possible = true
            ID = tostring(v.id)
            if tonumber(v.maxPlayers) > tonumber(v.playing) then
                for _, Existing in pairs(AllIDs) do
                    if num ~= 0 then
                        if ID == tostring(Existing) then
                            Possible = false
                        end
                    else
                        if tonumber(actualHour) ~= tonumber(Existing) then
                            local delFile =
                                pcall(
                                function()
                                    delfile("NotSameServers.json")
                                    AllIDs = {}
                                    table.insert(AllIDs, actualHour)
                                end
                            )
                        end
                    end
                    num = num + 1
                end
                if Possible == true then
                    table.insert(AllIDs, ID)
                    wait()
                    pcall(
                        function()
                            writefile("NotSameServers.json", game:GetService("HttpService"):JSONEncode(AllIDs))
                            wait()
                            game:GetService("TeleportService"):TeleportToPlaceInstance(
                                PlaceID,
                                ID,
                                game.Players.LocalPlayer
                            )
                        end
                    )
                    wait(4)
                end
            end
        end
    end

    function Teleport()
        while wait() do
            pcall(
                function()
                    TPReturner()
                    if foundAnything ~= "" then
                        TPReturner()
                    end
                end
            )
        end
    end

    -- If you'd like to use a script before server hopping (Like a Automatic Chest collector you can put the Teleport() after it collected everything.
    Teleport(45)
end

if game.PlaceId == 735030788 then
    game:GetService("TeleportService"):Teleport(1765700510, LocalPlayer)
end

if game.PlaceId == 1765700510 then
spawn(
    function()
        while wait(30) do
            Teleporter()
        end
    end
)
    game.StarterGui:SetCore(
        "SendNotification",
        {
            Title = "Cool",
            Text = "ButWhoAsked?"
        }
    )
    game.StarterGui:SetCore(
        "SendNotification",
        {
            Title = "Credits",
            Text = "CharWar Serverhops, DekuDimz Aka the retard"
        }
    )

    while getgenv().FromTheGetGo do
        wait()
        pcall(
            function()
                for _, v in pairs(game:GetService("Workspace").CollectibleDiamonds:GetChildren()) do
                    if v:IsA("Part") and v.Transparency == 0 then
                        wait()
                        firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, v, 0) --0 is touch
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame =
                            game:GetService("Workspace").LocalContent.Triggers.Buildings.LectureHall.CFrame

                        game.Workspace.Gravity = 0

                        if v.Transparency ~= 0 or v:FindFirstChild("OldSound") then
                            Teleporter()
                        end
                    end
                end
            end
        )
    end
end
