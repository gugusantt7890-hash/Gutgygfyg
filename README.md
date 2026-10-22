
-- FUGA PRO | Menu mobile
-- Para seu próprio jogo no Roblox Studio

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "FugaPro"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

local vermelho = Color3.fromRGB(255, 25, 45)
local preto = Color3.fromRGB(12, 12, 16)
local branco = Color3.fromRGB(245, 245, 250)

local distancia = 25
local forcaFrente = 75
local forcaCima = 40
local fugaAtiva = false
local vooAtivo = true
local ultimoImpulso = 0

local function criar(className, props, parent)
    local obj = Instance.new(className)
    for k, v in pairs(props) do
        obj[k] = v
    end
    obj.Parent = parent
    return obj
end

local abrir = criar("TextButton", {
    Size = UDim2.fromOffset(58, 48),
    Position = UDim2.new(0, 12, 0.35, 0),
    BackgroundColor3 = vermelho,
    Text = "F",
    TextColor3 = branco,
    TextScaled = true,
    Font = Enum.Font.GothamBold
}, gui)

local painel = criar("Frame", {
    Size = UDim2.new(0.92, 0, 0.72, 0),
    Position = UDim2.new(0.04, 0, 0.14, 0),
    BackgroundColor3 = preto,
    BorderSizePixel = 0
}, gui)

criar("UICorner", {CornerRadius = UDim.new(0, 16)}, painel)
criar("UIStroke", {Color = vermelho, Thickness = 2}, painel)

local titulo = criar("TextLabel", {
    Size = UDim2.new(1, -60, 0, 48),
    Position = UDim2.fromOffset(12, 5),
    BackgroundTransparency = 1,
    Text = "FUGA PRO",
    TextColor3 = vermelho,
    TextScaled = true,
    Font = Enum.Font.GothamBlack,
    TextXAlignment = Enum.TextXAlignment.Left
}, painel)

local fechar = criar("TextButton", {
    Size = UDim2.fromOffset(42, 42),
    Position = UDim2.new(1, -48, 0, 5),
    BackgroundColor3 = Color3.fromRGB(40, 20, 25),
    Text = "X",
    TextColor3 = branco,
    TextScaled = true
}, painel)

local conteudo = criar("ScrollingFrame", {
    Size = UDim2.new(1, -20, 1, -65),
    Position = UDim2.fromOffset(10, 55),
    BackgroundTransparency = 1,
    BorderSizePixel = 0,
    ScrollBarThickness = 4,
    CanvasSize = UDim2.new(0, 0, 0, 410)
}, painel)

criar("UIListLayout", {
    Padding = UDim.new(0, 9),
    SortOrder = Enum.SortOrder.LayoutOrder
}, conteudo)

local function botao(texto, ordem, callback)
    local b = criar("TextButton", {
        Size = UDim2.new(1, -5, 0, 48),
        BackgroundColor3 = Color3.fromRGB(28, 20, 25),
        Text = texto,
        TextColor3 = branco,
        TextScaled = true,
        Font = Enum.Font.GothamBold,
        LayoutOrder = ordem
    }, conteudo)
    criar("UICorner", {CornerRadius = UDim.new(0, 10)}, b)
    criar("UIStroke", {Color = vermelho, Thickness = 1}, b)
    b.Activated:Connect(function()
        callback(b)
    end)
    return b
end

local function rotulo(texto, ordem)
    return criar("TextLabel", {
        Size = UDim2.new(1, -5, 0, 30),
        BackgroundTransparency = 1,
        Text = texto,
        TextColor3 = branco,
        TextScaled = true,
        Font = Enum.Font.Gotham,
        LayoutOrder = ordem
    }, conteudo)
end

botao("FUGA AUTOMÁTICA: OFF", 1, function(b)
    fugaAtiva = not fugaAtiva
    b.Text = fugaAtiva and
        "FUGA AUTOMÁTICA: ON" or "FUGA AUTOMÁTICA: OFF"
    b.BackgroundColor3 = fugaAtiva and
        Color3.fromRGB(100, 15, 25) or
        Color3.fromRGB(28, 20, 25)
end)

botao("VOO: ON", 2, function(b)
    vooAtivo = not vooAtivo
    b.Text = vooAtivo and "VOO: ON" or "VOO: OFF"
end)

rotulo("Distância de detecção: " .. distancia, 3)

botao("Distância + 5", 4, function()
    distancia = math.min(distancia + 5, 100)
end)

botao("Distância - 5", 5, function()
    distancia = math.max(distancia - 5, 5)
end)

rotulo("Força para frente: " .. forcaFrente, 6)

botao("Aumentar força frontal", 7, function()
    forcaFrente = math.min(forcaFrente + 10, 150)
end)

botao("Diminuir força frontal", 8, function()
    forcaFrente = math.max(forcaFrente - 10, 20)
end)

rotulo("Força para cima: " .. forcaCima, 9)

botao("Aumentar força vertical", 10, function()
    forcaCima = math.min(forcaCima + 5, 100)
end)

botao("Diminuir força vertical", 11, function()
    forcaCima = math.max(forcaCima - 5, 0)
end)

botao("IMPULSO DE TESTE", 12, function()
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        root.AssemblyLinearVelocity =
            root.CFrame.LookVector * forcaFrente
            + Vector3.new(0, forcaCima, 0)
    end
end)

fechar.Activated:Connect(function()
    painel.Visible = false
end)

abrir.Activated:Connect(function()
    painel.Visible = not painel.Visible
end)

-- Detecta NPCs identificados com um BoolValue chamado "Policial"
-- e aplica um impulso quando a fuga automática está ativa.
RunService.Heartbeat:Connect(function()
    if not fugaAtiva or not vooAtivo then return end
    if os.clock() - ultimoImpulso < 1.5 then return end

    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not root or not humanoid or humanoid.Health <= 0 then return end

    for _, npc in ipairs(workspace:GetChildren()) do
        if npc:IsA("Model") and npc:FindFirstChild("Policial") then
            local npcRoot = npc:FindFirstChild("HumanoidRootPart")
            local npcHumanoid = npc:FindFirstChildOfClass("Humanoid")

            if npcRoot and npcHumanoid and npcHumanoid.Health > 0
                and (npcRoot.Position - root.Position).Magnitude <= distancia then

                ultimoImpulso = os.clock()
                root.AssemblyLinearVelocity =
                    root.CFrame.LookVector * forcaFrente
                    + Vector3.new(0, forcaCima, 0)
                break
            end
        end
    end
end)

player.CharacterAdded:Connect(function()
    ultimoImpulso = 0
end)
