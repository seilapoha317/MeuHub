-- Carregar biblioteca de UI
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Blox Fruits - Script", "Ocean")

-- Criar guia do menu
local AutoFarmTab = Window:NewTab("Auto Farm")
local AutoFarmSection = AutoFarmTab:NewSection("Configurações")

local OtherTab = Window:NewTab("Outros")
local OtherSection = OtherTab:NewSection("Extras")

-- Variáveis
_G.AutoFarm = false
_G.AutoSpinFruit = false
_G.ESPFrutas = false
_G.TeleportFruta = false
_G.TrocaServidor = false

-- Auto Farm Melhorado
function AutoFarm()
    while _G.AutoFarm do
        task.wait()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local root = character and character:FindFirstChild("HumanoidRootPart")

        if root then
            for _, enemy in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
                if enemy:FindFirstChild("HumanoidRootPart") and enemy.Humanoid.Health > 0 then
                    root.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
                    task.wait(0.1)
                    game:GetService("VirtualUser"):CaptureController()
                    game:GetService("VirtualUser"):Button1Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
                end
            end
        end
    end
end

AutoFarmSection:NewToggle("Ativar Auto Farm", "Liga/desliga Auto Farm", function(state)
    _G.AutoFarm = state
    if state then AutoFarm() end
end)

-- ESP de Frutas
function ESPFrutas()
    while _G.ESPFrutas do
        task.wait(1)
        for _, fruit in pairs(game:GetService("Workspace"):GetChildren()) do
            if fruit:IsA("Tool") and string.find(fruit.Name, "Fruit") then
                if not fruit:FindFirstChild("ESP") then
                    local billboard = Instance.new("BillboardGui", fruit)
                    billboard.Name = "ESP"
                    billboard.Size = UDim2.new(5, 0, 5, 0)
                    billboard.AlwaysOnTop = true
                    local text = Instance.new("TextLabel", billboard)
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.Text = fruit.Name
                    text.TextColor3 = Color3.fromRGB(255, 0, 0)
                    text.BackgroundTransparency = 1
                end
            end
        end
    end
end

AutoFarmSection:NewToggle("Ativar ESP Frutas", "Liga/desliga ESP de frutas", function(state)
    _G.ESPFrutas = state
    if state then ESPFrutas() end
end)

-- Teleporte para Frutas Spawnadas
function TeleportFruta()
    while _G.TeleportFruta do
        task.wait(5)
        for _, fruit in pairs(game:GetService("Workspace"):GetChildren()) do
            if fruit:IsA("Tool") and string.find(fruit.Name, "Fruit") then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = fruit.Handle.CFrame
                task.wait(2)
            end
        end
    end
end

AutoFarmSection:NewToggle("Ativar Teleporte para Frutas", "Liga/desliga Teleporte para frutas", function(state)
    _G.TeleportFruta = state
    if state then TeleportFruta() end
end)

-- Auto Spin Fruit
function AutoSpinFruit()
    while _G.AutoSpinFruit do
        task.wait(10)
        local args = {[1] = "Cousin", [2] = "Buy"}
        game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))
    end
end

OtherSection:NewToggle("Ativar Auto Spin Fruit", "Liga/desliga Auto Spin Fruit", function(state)
    _G.AutoSpinFruit = state
    if state then AutoSpinFruit() end
end)

-- Troca Automática de Servidor
function TrocaServidor()
    while _G.TrocaServidor do
        task.wait(60) -- Checa a cada 60 segundos
        local Http = game:GetService("HttpService")
        local Servers = Http:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/2753915549/servers/Public?sortOrder=Asc&limit=100"))
        
        for _, server in pairs(Servers.data) do
            if server.playing < server.maxPlayers and server.id ~= game.JobId then
                game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, server.id)
                task.wait(5)
            end
        end
    end
end

OtherSection:NewToggle("Troca Automática de Servidor", "Liga/desliga troca de servidor", function(state)
    _G.TrocaServidor = state
    if state then TrocaServidor() end
end)

print("Script carregado com sucesso!")
