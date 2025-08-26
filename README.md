local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Flee The Facility",
    TabWidth = 160, 
    Size = UDim2.fromOffset(580, 460),
    Theme = "Darker"
})

local Tab = {
    ESP = Window:AddTab({ Title = "ESP", Icon = "eye"}),
    Fog = Window:AddTab({ Title = "Fog", Icon = "cloud-fog"}),
    Shadow = Window:AddTab({ Title = "Shadow", Icon = "square"}),
    Aimbot = Window:AddTab({ Title = "Aimbot", Icon = "crosshair"})
}

-- Player ESP
local Toggle = Tab.ESP:AddToggle("Player ESP", 
{
    Title = "Player ESP", 
    Description = "ESP para os players",
    Default = false,
    Callback = function(state)
        if state then
            local Players = game:GetService("Players")
            local LocalPlayer = Players.LocalPlayer
            local BeastPlayer = nil

            local function createHighlight(character, color)
                local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart and character ~= LocalPlayer.Character then
                    if character:FindFirstChild("Highlight") then
                        character.Highlight:Destroy()
                    end
                    local highlight = Instance.new("Highlight")
                    highlight.Adornee = character
                    highlight.Parent = character
                    highlight.FillTransparency = 1
                    highlight.OutlineColor = color
                end
            end

            local function identifyBeast()
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        local hasHammer = player.Character:FindFirstChild("Hammer")
                        if hasHammer then
                            BeastPlayer = player
                            return player
                        end
                    end
                end
                return nil
            end

            local function createPlayerESP(player)
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
                    local isBeast = player == BeastPlayer
                    local color = isBeast and Color3.fromRGB(170, 0, 0) or Color3.fromRGB(0, 170, 0)
                    createHighlight(player.Character, color)
                end
            end

            local function removePlayerESP(player)
                if player.Character and player.Character:FindFirstChild("Highlight") then
                    player.Character.Highlight:Destroy()
                end
                if player == BeastPlayer then
                    BeastPlayer = nil
                end
            end

            BeastPlayer = identifyBeast()

            for _, player in pairs(Players:GetPlayers()) do
                createPlayerESP(player)
            end

            Players.PlayerAdded:Connect(function(player)
                player.CharacterAdded:Connect(function()
                    BeastPlayer = identifyBeast()
                    createPlayerESP(player)
                end)
            end)

            Players.PlayerRemoving:Connect(removePlayerESP)

        else
            for _, player in pairs(game:GetService("Players"):GetPlayers()) do
                if player.Character and player.Character:FindFirstChild("Highlight") then
                    player.Character.Highlight:Destroy()
                end
            end
        end
    end 
})

-- Computer ESP
Tab.ESP:AddToggle("Computer ESP", 
{
    Title = "Computer ESP", 
    Description = "ESP para os computadores",
    Default = false,
    Callback = function(state)
        if state then
            local function createComputerESP()
                for _, Pc in pairs(game.Workspace:GetDescendants()) do
                    if Pc.Name == "ComputerTable" then
                        if Pc:FindFirstChild("Highlight") then
                            Pc.Highlight:Destroy()
                        end

                        local highlight = Instance.new("Highlight")
                        highlight.Adornee = Pc
                        highlight.Parent = Pc
                        highlight.FillTransparency = 1
                        highlight.OutlineColor = Color3.fromRGB(0, 0, 130)
                    end
                end
            end

            createComputerESP()
        else
            for _, Pc in pairs(game.Workspace:GetDescendants()) do
                if Pc.Name == "ComputerTable" and Pc:FindFirstChild("Highlight") then
                    Pc.Highlight:Destroy()
                end
            end
        end
    end 
})

-- FreezePod ESP
Tab.ESP:AddToggle("FreezePod ESP", 
{
    Title = "FreezePod ESP", 
    Description = "ESP para as cápsulas",
    Default = false,
    Callback = function(state)
        if state then
            local function createPodsESP()
                for _, pod in pairs(game.Workspace:GetDescendants()) do
                    if pod.Name == "FreezePod" then
                        if pod:FindFirstChild("Highlight") then
                            pod.Highlight:Destroy()
                        end

                        local highlight = Instance.new("Highlight")
                        highlight.Adornee = pod
                        highlight.Parent = pod
                        highlight.FillTransparency = 1
                        highlight.OutlineColor = Color3.fromRGB(0, 170, 255)
                    end
                end
            end

            createPodsESP()
        else
            for _, pod in pairs(game.Workspace:GetDescendants()) do
                if pod.Name == "FreezePod" and pod:FindFirstChild("Highlight") then
                    pod.Highlight:Destroy()
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
        if state then
            local function createExitDoorESP()
                for _, door in pairs(game.Workspace:GetDescendants()) do
                    if door.Name == "ExitDoor" then
                        if door:FindFirstChild("Highlight") then
                            door.Highlight:Destroy()
                        end

                        local highlight = Instance.new("Highlight")
                        highlight.Adornee = door
                        highlight.Parent = door
                        highlight.FillTransparency = 1
                        highlight.OutlineColor = Color3.fromRGB(200, 200, 0)
                    end
                end
            end

            createExitDoorESP()
        else
            for _, door in pairs(game.Workspace:GetDescendants()) do
                if door.Name == "ExitDoor" and door:FindFirstChild("Highlight") then
                    door.Highlight:Destroy()
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
    Title = "Remove Fog (remove a neblina inteiro do mapa)", 
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
    Title = "Aimbot (serve para a mira grudar no sobrevivente)", 
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
ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.BorderSizePixel = 0
ToggleButton.Position = UDim2.new(0, 10, 0.5, -19)
ToggleButton.Size = UDim2.new(0, 80, 0, 38)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.Text = "FTF"
ToggleButton.TextColor3 = Color3.fromRGB(0, 0, 0)
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
