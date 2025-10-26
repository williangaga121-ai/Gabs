# Gabs-- LocalScript (StarterPlayerScripts)
-- GUI simples com botões e um "AutoFarm" toggle
-- USO: educativo. NÃO use para explorar/cheat em jogos de terceiros.

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "FuncoesGui"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Container
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 180)
frame.Position = UDim2.new(0, 20, 0, 80)
frame.BackgroundTransparency = 0.15
frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
frame.BorderSizePixel = 0
frame.Parent = gui

local uiList = Instance.new("UIListLayout")
uiList.Padding = UDim.new(0,8)
uiList.FillDirection = Enum.FillDirection.Vertical
uiList.Parent = frame
uiList.SortOrder = Enum.SortOrder.LayoutOrder

local title = Instance.new("TextLabel")
title.Text = "Painel de Funções"
title.Size = UDim2.new(1, -12, 0, 30)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = frame

local function makeButton(text)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -12, 0, 36)
    btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 16
    btn.Text = text
    btn.Parent = frame
    return btn
end

-- Estado do AutoFarm (cliente-side toggle)
local autoFarmEnabled = false
local autoFarmInterval = 0.6 -- segundos entre passos (ajuste conforme precisa)
local lastFarmTick = 0

-- PLACEHOLDER: função que representa um "passo" de farm.
-- Em um jogo real, EM VEZ de manipular objetos remotos do cliente aqui,
-- dispare um RemoteEvent para o servidor e deixe o servidor validar/realizar ação.
local function performFarmStep()
    -- Exemplo seguro: enviar um pedido ao servidor para "colecionar"
    -- local remote = game.ReplicatedStorage:FindFirstChild("FarmRemote")
    -- if remote then
    --     remote:FireServer("collect")
    -- end

    -- Aqui apenas feedback visual/demo local:
    print("performFarmStep chamado (somente demo/local).")
end

-- Loop que roda enquanto autoFarmEnabled for true
RunService.RenderStepped:Connect(function(delta)
    if not autoFarmEnabled then return end
    lastFarmTick = lastFarmTick + delta
    if lastFarmTick >= autoFarmInterval then
        lastFarmTick = 0
        -- Chame a função de passo — implemente comportamento seguro no servidor
        performFarmStep()
    end
end)

-- Botões
local btnAutoFarm = makeButton("AutoFarm: OFF")
btnAutoFarm.MouseButton1Click:Connect(function()
    autoFarmEnabled = not autoFarmEnabled
    if autoFarmEnabled then
        btnAutoFarm.Text = "AutoFarm: ON"
        lastFarmTick = 0
    else
        btnAutoFarm.Text = "AutoFarm: OFF"
    end
end)

local btnTeleport = makeButton("Exemplo: Teleportar (demo)")
btnTeleport.MouseButton1Click:Connect(function()
    -- Exemplo local: teleportar o personagem para cima (apenas se for seu jogo)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame + Vector3.new(0, 10, 0)
    end
end)

local btnClose = makeButton("Fechar GUI")
btnClose.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Dica: Para ações que alteram estado do mundo (dar xp, coletar item, etc),
-- use um RemoteEvent e faça validação no servidor:
-- Server-side (Script) verifica autorização/condições, aplica recompensa.
-- Client-side só envia pedidos
