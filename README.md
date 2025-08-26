-- LocalScript em StarterGui
-- Interface estilizada com abas + ESP

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local enabled = true
local showAllies = false

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "ESP_UI"

-- Criar Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 320, 0, 220)
MainFrame.Position = UDim2.new(0, 50, 0, 100)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)

-- Bordas arredondadas
local UICorner = Instance.new("UICorner", MainFrame)
UICorner.CornerRadius = UDim.new(0, 12)

-- Borda externa leve
local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Thickness = 2
Stroke.Color = Color3.fromRGB(60, 60, 60)

-- Criar abas
local ESPButton = Instance.new("TextButton", MainFrame)
ESPButton.Size = UDim2.new(0, 100, 0, 30)
ESPButton.Position = UDim2.new(0, 0, 0, 0)
ESPButton.Text = "ESP"
ESPButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)

local ServerButton = Instance.new("TextButton", MainFrame)
ServerButton.Size = UDim2.new(0, 100, 0, 30)
ServerButton.Position = UDim2.new(0, 100, 0, 0)
ServerButton.Text = "Servidores"
ServerButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)

-- Estilo botões
for _, btn in ipairs({ESPButton, ServerButton}) do
    local bc = Instance.new("UICorner", btn)
    bc.CornerRadius = UDim.new(0, 8)
    local st = Instance.new("UIStroke", btn)
    st.Color = Color3.fromRGB(80, 80, 80)
end

-- Criar Frames das abas
local ESPFrame = Instance.new("Frame", MainFrame)
ESPFrame.Size = UDim2.new(1, 0, 1, -30)
ESPFrame.Position = UDim2.new(0, 0, 0, 30)
ESPFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Instance.new("UICorner", ESPFrame).CornerRadius = UDim.new(0, 8)

local ServerFrame = Instance.new("Frame", MainFrame)
ServerFrame.Size = ESPFrame.Size
ServerFrame.Position = ESPFrame.Position
ServerFrame.BackgroundColor3 = ESPFrame.BackgroundColor3
ServerFrame.Visible = false
Instance.new("UICorner", ServerFrame).CornerRadius = UDim.new(0, 8)

-- Botões do ESP
local ToggleESP = Instance.new("TextButton", ESPFrame)
ToggleESP.Size = UDim2.new(0, 220, 0, 40)
ToggleESP.Position = UDim2.new(0, 50, 0, 30)
ToggleESP.Text = "ESP: ON"
ToggleESP.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
Instance.new("UICorner", ToggleESP).CornerRadius = UDim.new(0, 8)

local ToggleAllies = Instance.new("TextButton", ESPFrame)
ToggleAllies.Size = UDim2.new(0, 220, 0, 40)
ToggleAllies.Position = UDim2.new(0, 50, 0, 90)
ToggleAllies.Text = "Mostrar Aliados: OFF"
ToggleAllies.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
Instance.new("UICorner", ToggleAllies).CornerRadius = UDim.new(0, 8)

-- Lista de servidores fake
for i = 1, 5 do
    local ServerBtn = Instance.new("TextButton", ServerFrame)
    ServerBtn.Size = UDim2.new(0, 260, 0, 35)
    ServerBtn.Position = UDim2.new(0, 30, 0, 20 + (i - 1) * 40)
    ServerBtn.Text = "Servidor " .. i .. " - Ping: " .. math.random(20, 100)
    ServerBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    Instance.new("UICorner", ServerBtn).CornerRadius = UDim.new(0, 8)

    ServerBtn.MouseButton1Click:Connect(function()
        print("Você selecionou Servidor " .. i)
        -- animação de clique
        TweenService:Create(ServerBtn, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(120, 120, 120)}):Play()
        task.wait(0.2)
        TweenService:Create(ServerBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(80, 80, 80)}):Play()
    end)
end

-- Função ESP
local function addESP(player)
    if player.Character then
        if not player.Character:FindFirstChild("Highlight") then
            local highlight = Instance.new("Highlight")
            highlight.Name = "Highlight"
            highlight.Adornee = player.Character
            highlight.Parent = player.Character

            if player.Team == LocalPlayer.Team then
                highlight.FillColor = Color3.fromRGB(0, 255, 0) -- verde
            else
                highlight.FillColor = Color3.fromRGB(255, 0, 0) -- vermelho
            end
        end
    end
end

-- Monitorar players
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        task.wait(1)
        addESP(player)
    end)
end)

for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer and player.Character then
        addESP(player)
    end
end

-- Botões ESP
ToggleESP.MouseButton1Click:Connect(function()
    enabled = not enabled
    ToggleESP.Text = "ESP: " .. (enabled and "ON" or "OFF")

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local highlight = player.Character:FindFirstChild("Highlight")
            if highlight then
                highlight.Enabled = enabled
            end
        end
    end
end)

ToggleAllies.MouseButton1Click:Connect(function()
    showAllies = not showAllies
    ToggleAllies.Text = "Mostrar Aliados: " .. (showAllies and "ON" or "OFF")
end)

-- Troca de abas com animação
ESPButton.MouseButton1Click:Connect(function()
    ESPFrame.Visible = true
    ServerFrame.Visible = false
end)

ServerButton.MouseButton1Click:Connect(function()
    ESPFrame.Visible = false
    ServerFrame.Visible = true
end)
