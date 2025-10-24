local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Flee The Facility - Cloud Hub",
    TabWidth = 160, 
    Size = UDim2.fromOffset(580, 460),
    Theme = "Darker"
})

local Tab = {
    ESP = Window:AddTab({ Title = "ESP", Icon = "eye"}),
    Fog = Window:AddTab({ Title = "Fog", Icon = "cloud-fog"}),
    Visual = Window:AddTab({ Title = "Visual", Icon = "view"}),
    Universal = Window:AddTab({ Title = "Universal", Icon = "globe"}),
    Shadow = Window:AddTab({ Title = "Shadow", Icon = "square"}),
    Aimbot = Window:AddTab({ Title = "Aimbot", Icon = "crosshair"})
}

-- Variables
local playertoggle = false
local pctoggle = false
local podstoggle = false
local exitstoggle = false
local doortoggle = false
local neverfailtoggle = false
local infinitejumptoggle = false
local infinitejumpconnection = nil
local hitboxToggle = false

-- Helper Functions
local function getBeast()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("Hammer") then
            return player
        end
    end
    return nil
end

local function reloadESP()
    -- Placeholder function for ESP reload
end

-- Player ESP
Tab.ESP:AddToggle("Player ESP", 
{
    Title = "Player ESP", 
    Description = "ESP para os players",
    Default = false,
    Callback = function(state)
        playertoggle = state
        reloadESP()
        spawn(function()
            local players = game.Players:GetPlayers()
            for i=1, #players do
                if players[i] ~= game.Players.LocalPlayer and players[i].Character ~= nil then
                    local character = players[i].Character
                    if character:FindFirstChild("Highlight") and not playertoggle then
                        character.Highlight:Destroy()
                    end
                    if playertoggle and not character:FindFirstChild("Highlight") then
                        local a = Instance.new("Highlight", character)
                        a.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        a.FillTransparency = 1
                        a.OutlineColor = Color3.fromRGB(0,130,0)
                        spawn(function()
                            repeat
                                wait(0.1)
                                if players[i] == getBeast() then
                                    a.OutlineColor = Color3.fromRGB(170,0,0)
                                else
                                    a.OutlineColor = Color3.fromRGB(0,170,0)
                                end
                            until character == nil or a == nil or players[i] == nil
                        end)
                    end
                end
            end
        end)
    end 
})

-- Computer ESP
Tab.ESP:AddToggle("Computer ESP", 
{
    Title = "Computer ESP", 
    Description = "ESP para os computadores",
    Default = false,
    Callback = function(state)
        pctoggle = state
        reloadESP()
        spawn(function()
            if game.ReplicatedStorage:FindFirstChild("CurrentMap") and game.ReplicatedStorage.CurrentMap.Value then
                local map = game.ReplicatedStorage.CurrentMap.Value
                local mapstuff = map:GetChildren()
                for i=1,#mapstuff do
                    if mapstuff[i].Name == "ComputerTable" then
                        if mapstuff[i]:FindFirstChild("Highlight") and not pctoggle then
                            mapstuff[i].Highlight:Destroy()
                        end
                        if pctoggle and not mapstuff[i]:FindFirstChild("Highlight") then
                            local a = Instance.new("Highlight", mapstuff[i])
                            a.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                            a.FillTransparency = 1
                            a.OutlineColor = Color3.fromRGB(0, 0, 130)
                            spawn(function()
                                repeat 
                                    if mapstuff[i]:FindFirstChild("Screen") then
                                        local screenColor = mapstuff[i].Screen.Color
                                        local r, g, b = screenColor.R, screenColor.G, screenColor.B
                                        local maxVal = math.max(r, g, b)
                                        
                                        if maxVal > 0 then
                                            if r >= g and r >= b then
                                                a.OutlineColor = Color3.fromRGB(130, 0, 0)
                                            elseif g >= r and g >= b then
                                                a.OutlineColor = Color3.fromRGB(0, 130, 0)
                                            else
                                                a.OutlineColor = Color3.fromRGB(0, 0, 130)
                                            end
                                        end
                                    end
                                    wait(1)
                                until mapstuff[i] == nil or a == nil
                            end)
                        end
                    end
                end
            end
        end)
    end
})

-- FreezePod ESP
Tab.ESP:AddToggle("FreezePod ESP", 
{
    Title = "FreezePod ESP", 
    Description = "ESP para as cápsulas",
    Default = false,
    Callback = function(state)
        podstoggle = state
        local mapstuff = game.Workspace:GetDescendants()
        for i = 1, #mapstuff do
            if mapstuff[i].Name == "FreezePod" then
                if mapstuff[i]:findFirstChild("Highlight") and not podstoggle then
                    mapstuff[i].Highlight:remove()
                end
                if podstoggle and not mapstuff[i]:findFirstChild("Highlight") then
                    local a = Instance.new("Highlight", mapstuff[i])
                    a.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    a.FillTransparency = 1
                    a.OutlineColor = Color3.fromRGB(0,170,220)
                end
            end
        end
    end 
})

-- Exit Door ESP
Tab.ESP:AddToggle("ExitDoor ESP", 
{
    Title = "ExitDoor ESP", 
    Description = "ESP para as portas de saídas",
    Default = false,
    Callback = function(state)
        exitstoggle = state
        local mapstuff = game.Workspace:GetDescendants()
        for i = 1, #mapstuff do
            if mapstuff[i].Name == "ExitDoor" then
                if mapstuff[i]:findFirstChild("Highlight") and not exitstoggle then
                    mapstuff[i].Highlight:remove()
                end
                if exitstoggle and not mapstuff[i]:findFirstChild("Highlight") then
                    local a = Instance.new("Highlight", mapstuff[i])
                    a.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    a.FillTransparency = 1
                    a.OutlineColor = Color3.fromRGB(180,180,0)
                end
            end
        end
    end 
})

-- Door ESP
Tab.ESP:AddToggle("Door ESP", 
{
    Title = "Door ESP", 
    Description = "ESP para as portas",
    Default = false,
    Callback = function(state)
        doortoggle = state
        
        if doortoggle then
            local cachedDoors = {}
            
            for _, door in pairs(workspace:GetDescendants()) do
                if door.Name == "SingleDoor" or door.Name == "DoubleDoor" then
                    table.insert(cachedDoors, door)
                end
            end
            
            spawn(function()
                repeat
                    wait(0.1)
                    for _, door in pairs(cachedDoors) do
                        if door and door.Parent then
                            if not door:FindFirstChild("Highlight") then
                                local highlight = Instance.new("Highlight")
                                highlight.Parent = door
                                highlight.Adornee = door
                                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                                highlight.FillTransparency = 0.7
                                highlight.OutlineTransparency = 1
                                highlight.OutlineColor = Color3.fromRGB(180, 180, 180)
                            end
                            local highlight = door:FindFirstChild("Highlight")
                            if highlight and door:FindFirstChild("DoorTrigger") then
                                local doorTrigger = door.DoorTrigger
                                if doorTrigger:FindFirstChild("ActionSign") then
                                    local actionValue = doorTrigger.ActionSign.Value
                                    if actionValue == 11 then
                                        highlight.FillColor = Color3.fromRGB(0, 180, 0)
                                    elseif actionValue == 10 then
                                        highlight.FillColor = Color3.fromRGB(180, 0, 0)
                                    end
                                end
                            end
                        end
                    end
                until not doortoggle
                
                for _, door in pairs(cachedDoors) do
                    if door and door:FindFirstChild("Highlight") then
                        door.Highlight:Destroy()
                    end
                end
            end)
        end
    end 
})

-- Fog Tab
Tab.Fog:AddButton({ 
    Title = "Fog Cinza Escuro", 
    Callback = function() 
        local lighting = game:GetService("Lighting")
        local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

        if atmosphere then
            atmosphere.Color = Color3.fromRGB(0, 0, 0)
            atmosphere.Glare = 1.64
            atmosphere.Haze = 2.46
            atmosphere.Decay = Color3.fromRGB(255, 255, 255)
            atmosphere.Density = 0.9
            atmosphere.Offset = 0
        end

        local sky = lighting:FindFirstChildOfClass("Sky")
        if sky then
            sky.MoonAngularSize = 10
            sky.StarCount = 0
        end
    end 
})

Tab.Fog:AddButton({ 
    Title = "Fog Preto", 
    Callback = function() 
        local lighting = game:GetService("Lighting")
        local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

        if atmosphere then
            atmosphere.Color = Color3.fromRGB(0, 0, 0)
            atmosphere.Glare = 0
            atmosphere.Haze = 10
            atmosphere.Decay = Color3.fromRGB(0, 0, 0)
            atmosphere.Density = 0.85
            atmosphere.Offset = 0
        end

        local sky = lighting:FindFirstChildOfClass("Sky")
        if sky then
            sky.MoonAngularSize = 10
            sky.StarCount = 0
        end
    end 
})

Tab.Fog:AddButton({ 
    Title = "Remove Fog (precisa de calibrador)", 
    Callback = function() 
        local lighting = game:GetService("Lighting")
        local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

        if atmosphere then
            atmosphere.Color = Color3.fromRGB(0, 0, 0)
            atmosphere.Glare = 0
            atmosphere.Haze = 10
            atmosphere.Decay = Color3.fromRGB(0, 0, 0)
            atmosphere.Density = 0.77
            atmosphere.Offset = 0
        end

        local sky = lighting:FindFirstChildOfClass("Sky")
        if sky then
            sky.MoonAngularSize = 10
            sky.StarCount = 0
        end
    end 
})

Tab.Fog:AddButton({ 
    Title = "Remove Fog (remove a neblina inteira do mapa)", 
    Callback = function() 
        local lighting = game:GetService("Lighting")
        local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

        if atmosphere then
            atmosphere.Color = Color3.fromRGB(0, 0, 0)
            atmosphere.Glare = 0
            atmosphere.Haze = 10
            atmosphere.Decay = Color3.fromRGB(0, 0, 0)
            atmosphere.Density = 0
            atmosphere.Offset = 0
        end

        local sky = lighting:FindFirstChildOfClass("Sky")
        if sky then
            sky.MoonAngularSize = 10
            sky.StarCount = 0
        end
    end 
})

-- Never Fail Hacking
Tab.Visual:AddToggle("Never Fail Hacking", {
    Title = "Never Fail Hacking", 
    Description = "Serve pra nunca errar os PCS",
    Default = false,
    Callback = function(state)
        if state then
            neverfailtoggle = true
            spawn(function()
                local mt = getrawmetatable(game)
                local old = mt.__namecall
                setreadonly(mt,false)
                mt.__namecall = newcclosure(function(self, ...)
                    local args = {...}
                    if getnamecallmethod() == 'FireServer' and args[1] == 'SetPlayerMinigameResult' and neverfailtoggle then
                        args[2] = true
                    end
                    return old(self, unpack(args))
                end)
            end)
        else
            neverfailtoggle = false
        end
    end 
})

-- Inf Jump
Tab.Universal:AddToggle("Inf Jump", 
{
    Title = "Inf Jump", 
    Description = "Permite pular infinitamente",
    Default = false,
    Callback = function(state)
        infinitejumptoggle = state
        
        if infinitejumptoggle then
            infinitejumpconnection = game:GetService("UserInputService").JumpRequest:Connect(function()
                if infinitejumptoggle then
                    local player = game.Players.LocalPlayer
                    if player and player.Character and player.Character:FindFirstChild("Humanoid") then
                        player.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                    end
                end
            end)
        else
            if infinitejumpconnection then
                infinitejumpconnection:Disconnect()
                infinitejumpconnection = nil
            end
        end
    end 
})

-- Shadow Tab
Tab.Shadow:AddButton({ 
    Title = "Ativar Sombras", 
    Callback = function() 
        local Lighting = game:GetService("Lighting")
        Lighting.GlobalShadows = true
    end 
})

Tab.Shadow:AddButton({ 
    Title = "Desativar Sombras", 
    Callback = function() 
        local Lighting = game:GetService("Lighting")
        Lighting.GlobalShadows = false
    end 
})

-- Aimbot Toggle
Tab.Aimbot:AddToggle("Aimbot", {
    Title = "Aimbot",
    Description = "Serve pra a mira grudar no sobrevivente, se você for pc, pressione F pra a mira grudar no sobrevivente e pra parar de grudar clica F de novo.",
    Default = false,
    Callback = function(state)
        if state then
            local Players = game:GetService("Players")
            local UserInputService = game:GetService("UserInputService")
            local RunService = game:GetService("RunService")
            local LocalPlayer = Players.LocalPlayer
            local Camera = workspace.CurrentCamera

            _G.CamlockTarget = nil
            _G.CamlockConnection = nil
            _G.CamlockActive = false
            _G.KeyConnection = nil
            _G.PlayerRemovingConnection = nil

            local existingGui = LocalPlayer.PlayerGui:FindFirstChild("CamlockToggle")
            if existingGui then
                existingGui:Destroy()
            end

            local ScreenGui = Instance.new("ScreenGui")
            ScreenGui.Name = "CamlockToggle"
            ScreenGui.ResetOnSpawn = false
            ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

            local ToggleButton = Instance.new("TextButton")
            ToggleButton.Name = "Toggle"
            ToggleButton.Parent = ScreenGui
            ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
            ToggleButton.BackgroundTransparency = 0.3
            ToggleButton.BorderSizePixel = 0
            ToggleButton.Position = UDim2.new(0, 10, 0.5, -19)
            ToggleButton.Size = UDim2.new(0, 80, 0, 38)
            ToggleButton.Font = Enum.Font.GothamBold
            ToggleButton.Text = "OFF"
            ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            ToggleButton.TextSize = 16
            ToggleButton.Active = true
            ToggleButton.Draggable = true

            local Corner = Instance.new("UICorner")
            Corner.CornerRadius = UDim.new(0, 8)
            Corner.Parent = ToggleButton

            local function stopCamlock()
                if _G.CamlockConnection then
                    _G.CamlockConnection:Disconnect()
                    _G.CamlockConnection = nil
                end
                _G.CamlockTarget = nil
                _G.CamlockActive = false
                
                if ToggleButton and ToggleButton.Parent then
                    ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
                    ToggleButton.Text = "OFF"
                end
            end

            local function startCamlock()
                if _G.CamlockConnection then
                    _G.CamlockConnection:Disconnect()
                end
                
                _G.CamlockActive = true
                
                _G.CamlockConnection = RunService.RenderStepped:Connect(function()
                    if _G.CamlockTarget and _G.CamlockTarget.Character and _G.CamlockTarget.Character:FindFirstChild("Head") then
                        local head = _G.CamlockTarget.Character.Head
                        Camera.CFrame = CFrame.new(Camera.CFrame.Position, head.Position)
                    else
                        stopCamlock()
                    end
                end)
                
                if ToggleButton and ToggleButton.Parent then
                    ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 255, 50)
                    ToggleButton.Text = "ON"
                end
            end

            local function cleanupAll()
                stopCamlock()
                
                if _G.KeyConnection then
                    _G.KeyConnection:Disconnect()
                    _G.KeyConnection = nil
                end
                if _G.PlayerRemovingConnection then
                    _G.PlayerRemovingConnection:Disconnect()
                    _G.PlayerRemovingConnection = nil
                end
                
                if ScreenGui and ScreenGui.Parent then
                    ScreenGui:Destroy()
                end
            end

            _G.KeyConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                
                if input.KeyCode == Enum.KeyCode.F then
                    if _G.CamlockActive then
                        stopCamlock()
                        return
                    end
                    
                    local closestPlayer = nil
                    local shortestDistance = math.huge
                    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                    
                    for _, player in pairs(Players:GetPlayers()) do
                        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                            local head = player.Character.Head
                            local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
                            
                            if onScreen then
                                local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                                if distance < shortestDistance then
                                    shortestDistance = distance
                                    closestPlayer = player
                                end
                            end
                        end
                    end
                    
                    if closestPlayer then
                        _G.CamlockTarget = closestPlayer
                        startCamlock()
                    end
                end
            end)

            ToggleButton.MouseButton1Click:Connect(function()
                ToggleButton:TweenSize(
                    UDim2.new(0, 75, 0, 35),
                    Enum.EasingDirection.Out,
                    Enum.EasingStyle.Quad,
                    0.05,
                    true,
                    function()
                        ToggleButton:TweenSize(
                            UDim2.new(0, 80, 0, 38),
                            Enum.EasingDirection.Out,
                            Enum.EasingStyle.Quad,
                            0.05
                        )
                    end
                )
                
                if _G.CamlockActive then
                    stopCamlock()
                else
                    local closestPlayer = nil
                    local shortestDistance = math.huge
                    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                    
                    for _, player in pairs(Players:GetPlayers()) do
                        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                            local head = player.Character.Head
                            local screenPos, onScreen = Camera:WorldToScreenPoint(head.Position)
                            
                            if onScreen then
                                local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                                if distance < shortestDistance then
                                    shortestDistance = distance
                                    closestPlayer = player
                                end
                            end
                        end
                    end
                    
                    if closestPlayer then
                        _G.CamlockTarget = closestPlayer
                        startCamlock()
                    end
                end
            end)

            _G.PlayerRemovingConnection = Players.PlayerRemoving:Connect(function(player)
                if player == _G.CamlockTarget then
                    stopCamlock()
                end
            end)

            task.spawn(function()
                repeat
                    task.wait(0.5)
                until not Window or not Window.Root or not Window.Root.Parent
                cleanupAll()
            end)

            task.spawn(function()
                local FluentGui = LocalPlayer.PlayerGui:WaitForChild("ScreenGui", 10)
                if FluentGui then
                    local connection
                    connection = FluentGui.AncestryChanged:Connect(function()
                        if not FluentGui.Parent then
                            task.wait(0.1)
                            cleanupAll()
                            if connection then
                                connection:Disconnect()
                            end
                        end
                    end)
                end
            end)

        else
            if _G.CamlockConnection then
                _G.CamlockConnection:Disconnect()
                _G.CamlockConnection = nil
            end
            if _G.KeyConnection then
                _G.KeyConnection:Disconnect()
                _G.KeyConnection = nil
            end
            if _G.PlayerRemovingConnection then
                _G.PlayerRemovingConnection:Disconnect()
                _G.PlayerRemovingConnection = nil
            end
            
            _G.CamlockTarget = nil
            _G.CamlockActive = false
            
            local gui = game.Players.LocalPlayer.PlayerGui:FindFirstChild("CamlockToggle")
            if gui then
                gui:Destroy()
            end
        end
    end
})

-- Hitbox Expander Toggle
Tab.Aimbot:AddToggle("Hitbox Expander", {
    Title = "Hitbox Expander",
    Description = "Aumenta a hitbox dos sobreviventes para facilitar acertar hits.",
    Default = false,
    Callback = function(state)
        hitboxToggle = state
        
        if not _G.originalSizes then
            _G.originalSizes = {}
        end
        
        local function expandHitbox(character)
            if not character then return end
            
            local parts = {"Head", "Torso", "HumanoidRootPart", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}
            
            for _, partName in pairs(parts) do
                local part = character:FindFirstChild(partName)
                if part and part:IsA("BasePart") then
                    local partKey = character.Name .. "_" .. partName
                    
                    if hitboxToggle then
                        if not _G.originalSizes[partKey] then
                            _G.originalSizes[partKey] = {
                                Size = part.Size,
                                Transparency = part.Transparency,
                                CanCollide = part.CanCollide
                            }
                        end
                        
                        part.Size = Vector3.new(5, 5, 5)
                        part.Transparency = 0.4
                        part.CanCollide = false
                    else
                        if _G.originalSizes[partKey] then
                            part.Size = _G.originalSizes[partKey].Size
                            part.Transparency = _G.originalSizes[partKey].Transparency
                            part.CanCollide = _G.originalSizes[partKey].CanCollide
                            
                            _G.originalSizes[partKey] = nil
                        end
                    end
                end
            end
        end
        
        spawn(function()
            local players = game.Players:GetPlayers()
            for i = 1, #players do
                if players[i] ~= game.Players.LocalPlayer and players[i].Character then
                    expandHitbox(players[i].Character)
                end
            end
        end)
        
        if hitboxToggle and not _G.hitboxConnections then
            _G.hitboxConnections = {}
            
            _G.hitboxConnections.playerAdded = game.Players.PlayerAdded:Connect(function(player)
                _G.hitboxConnections[player.UserId] = player.CharacterAdded:Connect(function(character)
                    wait(1)
                    if player ~= game.Players.LocalPlayer and hitboxToggle then
                        expandHitbox(character)
                    end
                end)
            end)
        elseif not hitboxToggle and _G.hitboxConnections then
            for _, connection in pairs(_G.hitboxConnections) do
                if typeof(connection) == "RBXScriptConnection" then
                    connection:Disconnect()
                end
            end
            _G.hitboxConnections = nil
        end
    end 
})

-- TOGGLE BUTTON (espera a Window estar pronta)
task.wait(1)

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FTFToggle"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
 
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "Toggle"
ToggleButton.Parent = ScreenGui
ToggleButton.BackgroundColor3 = Color3.fromRGB(5, 5, 5)
ToggleButton.BackgroundTransparency = 0.3
ToggleButton.BorderSizePixel = 0
ToggleButton.Position = UDim2.new(0, 10, 0.5, -19)
ToggleButton.Size = UDim2.new(0, 80, 0, 38)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.Text = "FTF"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.TextSize = 16
ToggleButton.Draggable = true

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 8)
Corner.Parent = ToggleButton

local Gradient = Instance.new("UIGradient")
Gradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(240, 240, 240))
}
Gradient.Rotation = 90
Gradient.Parent = ToggleButton

local UIVisible = true

ToggleButton.MouseButton1Click:Connect(function()
    UIVisible = not UIVisible
    
    ToggleButton:TweenSize(
        UDim2.new(0, 75, 0, 35),
        Enum.EasingDirection.Out,
        Enum.EasingStyle.Quad,
        0.05,
        true,
        function()
            ToggleButton:TweenSize(
                UDim2.new(0, 80, 0, 38),
                Enum.EasingDirection.Out,
                Enum.EasingStyle.Quad,
                0.05
            )
        end
    )
    
    if Window then
        Window:Minimize()
    end
    
    if UIVisible then
        ToggleButton.BackgroundColor3 = Color3.fromRGB(5, 5, 5)
        ToggleButton.BackgroundTransparency = 0.3
    else
        ToggleButton.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
        ToggleButton.BackgroundTransparency = 0.3
    end
end)

local function removeToggleButton()
    if ScreenGui and ScreenGui.Parent then
        ScreenGui:Destroy()
    end
end

task.spawn(function()
    repeat
        task.wait(0.5)
    until not Window or not Window.Root or not Window.Root.Parent
    removeToggleButton()
end)

task.spawn(function()
    local FluentGui = game.Players.LocalPlayer.PlayerGui:WaitForChild("ScreenGui", 10)
    if FluentGui then
        local connection
        connection = FluentGui.AncestryChanged:Connect(function()
            if not FluentGui.Parent then
                task.wait(0.1)
                removeToggleButton()
                if connection then
                    connection:Disconnect()
                end
            end
        end)
    end
end)
