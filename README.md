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
    Shadow = Window:AddTab({ Title = "Shadow", Icon = "square"}),
    Aimbot = Window:AddTab({ Title = "Aimbot", Icon = "crosshair"})
}

-- Variables
local playertoggle = false
local pctoggle = false
local podstoggle = false
local exitstoggle = false
local neverfailtoggle = false

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
                        a.FillTransparency = 1 -- Remove o preenchimento, só mostra bordas
                        a.OutlineColor = Color3.fromRGB(0,130,0) -- verde escuro para bordas
                        spawn(function()
                            repeat
                                wait(0.1)
                                if players[i] == getBeast() then
                                    -- Só bordas, sem preenchimento
                                    a.OutlineColor = Color3.fromRGB(170,0,0) -- vermelho escuro para bordas
                                else
                                    -- Só bordas, sem preenchimento
                                    a.OutlineColor = Color3.fromRGB(0,170,0) -- verde escuro para bordas
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
                            a.FillTransparency = 1 -- Remove o preenchimento, só mostra bordas
                            a.OutlineColor = Color3.fromRGB(0, 0, 130) -- azul escuro inicial
                            spawn(function()
                                repeat 
                                    if mapstuff[i]:FindFirstChild("Screen") then
                                        local screenColor = mapstuff[i].Screen.Color
                                        -- Encontra qual componente de cor é o dominante e aplica intensidade 130
                                        local r, g, b = screenColor.R, screenColor.G, screenColor.B
                                        local maxVal = math.max(r, g, b)
                                        
                                        if maxVal > 0 then
                                            -- Se for principalmente vermelho
                                            if r >= g and r >= b then
                                                a.OutlineColor = Color3.fromRGB(130, 0, 0)
                                            -- Se for principalmente verde  
                                            elseif g >= r and g >= b then
                                                a.OutlineColor = Color3.fromRGB(0, 80, 0)
                                            -- Se for principalmente azul
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

-- Aimbot Tab
Tab.Aimbot:AddButton({ 
    Title = "Aimbot (serve pra a mira grudar no sobrevivente)", 
    Callback = function() 
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Camlock-mobile-da-hood-20401"))()
    end 
})

-- TOGGLE BUTTON
wait(1)

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
    if ScreenGui then
        ScreenGui:Destroy()
    end
end

spawn(function()
    while Window and Window.Root and Window.Root.Parent do
        wait(0.1)
    end
    removeToggleButton()
end)

spawn(function()
    local FluentGui = game.Players.LocalPlayer.PlayerGui:WaitForChild("ScreenGui", 5)
    if FluentGui then
        FluentGui.AncestryChanged:Connect(function()
            if not FluentGui.Parent then
                wait(0.1)
                removeToggleButton()
            end
        end)
    end
end)
