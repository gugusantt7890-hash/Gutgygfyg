
-- COPÃO DE 10⚡️ PRO
-- LocalScript em StarterPlayerScripts
-- Para testar no seu próprio jogo.

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local function criarMenu()
    if playerGui:FindFirstChild("CopaoDe10") then
        return
    end

    local gui = Instance.new("ScreenGui")
    gui.Name = "CopaoDe10"
    gui.ResetOnSpawn = false
    gui.DisplayOrder = 999
    gui.Parent = playerGui

    local botao = Instance.new("TextButton")
    botao.Size = UDim2.fromOffset(210, 52)
    botao.Position = UDim2.new(0, 15, 0.65, 0)
    botao.BackgroundColor3 = Color3.fromRGB(170, 25, 35)
    botao.TextColor3 = Color3.new(1, 1, 1)
    botao.TextScaled = true
    botao.Font = Enum.Font.GothamBold
    botao.Text = "COPÃO DE 10⚡️: OFF"
    botao.Parent = gui

    Instance.new("UICorner", botao).CornerRadius =
        UDim.new(0, 10)

    local ativo = false

    botao.Activated:Connect(function()
        ativo = not ativo
        botao.Text = ativo
            and "COPÃO DE 10⚡️: ON"
            or "COPÃO DE 10⚡️: OFF"
    end)
end

criarMenu()

while task.wait(1) do
    if not playerGui:FindFirstChild("CopaoDe10") then
        criarMenu()
    end
end
