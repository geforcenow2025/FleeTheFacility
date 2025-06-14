local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Flee The Facility", "DarkTheme")

local Tab = Window:NewTab("Home")
local Section = Tab:NewSection("Home")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ScreenGui"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
 
local Toggle = Instance.new("TextButton")
Toggle.Name = "Toggle"
Toggle.Parent = ScreenGui
Toggle.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Toggle.Position = UDim2.new(0, 0, 0.454706937, 0)
Toggle.Size = UDim2.new(0, 90, 0, 38)
Toggle.Font = Enum.Font.SourceSans
Toggle.Text = "FTF"
Toggle.TextColor3 = Color3.fromRGB(248, 248, 248)
Toggle.TextSize = 28.000
Toggle.Draggable = true
Toggle.MouseButton1Click:connect(function()
    Library:ToggleUI()
end)

local Corner = Instance.new("UICorner")
Corner.Parent = Toggle

-- Player ESP
Section:NewToggle("Player ESP", "Player ESP", function(state)
    if state then
        print("Player ESP Ativado")

        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local BeastPlayer = nil -- Armazena o jogador que é a besta

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
                    -- O primeiro jogador identificado como besta se torna o target único
                    -- Ajuste conforme necessário com base nas características do jogo
                    local hasHammer = player.Character:FindFirstChild("Hammer") -- Exemplo: verifica se o jogador tem um item especial
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
                local color = isBeast and Color3.fromRGB(170, 0, 0) or Color3.fromRGB(0, 170, 0) -- Define cor com base no target
                createHighlight(player.Character, color)
            end
        end

        local function removePlayerESP(player)
            if player.Character and player.Character:FindFirstChild("Highlight") then
                player.Character.Highlight:Destroy()
            end
            if player == BeastPlayer then
                BeastPlayer = nil -- Remove a besta se sair do jogo
            end
        end

        -- Identifica a besta no início
        BeastPlayer = identifyBeast()

        -- Aplica ESP em jogadores já presentes
        for _, player in pairs(Players:GetPlayers()) do
            createPlayerESP(player)
        end

        -- Atualiza a besta quando novos jogadores entram
        Players.PlayerAdded:Connect(function(player)
            player.CharacterAdded:Connect(function()
                BeastPlayer = identifyBeast()
                createPlayerESP(player)
            end)
        end)

        -- Remove ESP caso o jogador saia do jogo
        Players.PlayerRemoving:Connect(removePlayerESP)

    else
        print("Player ESP Desativado")
        for _, player in pairs(game:GetService("Players"):GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Highlight") then
                player.Character.Highlight:Destroy()
            end
        end
    end
end)

-- Computer ESP
Section:NewToggle("Computer ESP", "Computer ESP", function(state) 
    if state then 
        print("Computer ESP Ativado")

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
                    highlight.OutlineColor = Color3.fromRGB(0, 0, 130) -- Azul escuro
                end
            end
        end

        createComputerESP()
    else
        print("Computer ESP Desativado")
        for _, Pc in pairs(game.Workspace:GetDescendants()) do
            if Pc.Name == "ComputerTable" and Pc:FindFirstChild("Highlight") then
                Pc.Highlight:Destroy()
            end
        end
    end
end)

-- FreezePod ESP
Section:NewToggle("FreezePod ESP", "FreezePod ESP", function(state)
    if state then
        print("FreezePod ESP Ativado")

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
                    highlight.OutlineColor = Color3.fromRGB(0, 170, 255) -- Azul claro
                end
            end
        end

        createPodsESP()
    else
        print("FreezePod ESP Desativado")
        for _, pod in pairs(game.Workspace:GetDescendants()) do
            if pod.Name == "FreezePod" and pod:FindFirstChild("Highlight") then
                pod.Highlight:Destroy()
            end
        end
    end
end)

-- Exit Door ESP
Section:NewToggle("Exit Door ESP", "Exit Door ESP", function(state)
    if state then
        print("Exit Door ESP Ativado")

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
                    highlight.OutlineColor = Color3.fromRGB(200, 200, 0) -- Amarelo
                end
            end
        end

        createExitDoorESP()
    else
        print("Exit Door ESP Desativado")
        for _, door in pairs(game.Workspace:GetDescendants()) do
            if door.Name == "ExitDoor" and door:FindFirstChild("Highlight") then
                door.Highlight:Destroy()
            end
        end
    end
end)

local Tab = Window:NewTab("Fog")
local Section = Tab:NewSection("Fog")

-- Fog/Neblina/Névoa

Section:NewButton("Fog Cinza Escuro", "fog cinza escuro", function() local lighting = game:GetService("Lighting")

local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

if atmosphere then
    atmosphere.Color = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Glare = 1.64
    atmosphere.Haze = 2.46
    atmosphere.Decay = Color3.fromRGB(255, 255, 255) -- Branco
    atmosphere.Density = 0.9
    atmosphere.Offset = 0
end

local sky = lighting:FindFirstChildOfClass("Sky")

if sky then
    sky.MoonAngularSize = 10
    sky.StarCount = 0
end end)

Section:NewButton("Fog Preto", "fog preto", function() local lighting = game:GetService("Lighting")

local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

if atmosphere then
    atmosphere.Color = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Glare = 0
    atmosphere.Haze = 10
    atmosphere.Decay = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Density = 0.85
    atmosphere.Offset = 0
end

local sky = lighting:FindFirstChildOfClass("Sky")

if sky then
    sky.MoonAngularSize = 10
    sky.StarCount = 0
end end)

Section:NewButton("Remove Fog (precisa de calibrador)", "remove fog (precisa de calibrador)", function() local lighting = game:GetService("Lighting")

local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

if atmosphere then
    atmosphere.Color = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Glare = 0
    atmosphere.Haze = 10
    atmosphere.Decay = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Density = 0.77
    atmosphere.Offset = 0
end

local sky = lighting:FindFirstChildOfClass("Sky")

if sky then
    sky.MoonAngularSize = 10
    sky.StarCount = 0
end end)
    
Section:NewButton("Remove Fog (remove a neblina inteira do mapa)", "remove fog (remove a neblina inteira do mapa)", function() local lighting = game:GetService("Lighting")

local atmosphere = lighting:FindFirstChildOfClass("Atmosphere")

if atmosphere then
    atmosphere.Color = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Glare = 0
    atmosphere.Haze = 10
    atmosphere.Decay = Color3.fromRGB(0, 0, 0) -- Preto
    atmosphere.Density = 0
    atmosphere.Offset = 0
end

local sky = lighting:FindFirstChildOfClass("Sky")

if sky then
    sky.MoonAngularSize = 10
    sky.StarCount = 0
end end)

local Tab = Window:NewTab("Aimbot") local Section = Tab:NewSection("Aimbot")

Section:NewButton("Aimbot (serve para a mira grudar no sobrevivente)", "aimbot (serve para a mira grudar no sobrevivente)", function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Camlock-mobile-da-hood-20401"))() end)
