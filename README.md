--[[
# ESP Vermelho com Interface (Roblox Lua LocalScript)
Este script cria um botão na tela que liga/desliga um ESP vermelho (Highlight) em todos os jogadores (exceto você).
Ideal para uso em executores que aceitam scripts em formato .md.lua (markdown comentado) ou para documentação.
Copie e cole em seu executor, preferencialmente em StarterPlayerScripts (ou execute via loadstring, se aceitar markdown).
--]]

-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ESPvermelho"
ScreenGui.Parent = (pcall(function() return game.CoreGui end) and game.CoreGui) or LocalPlayer:WaitForChild("PlayerGui")

local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 220, 0, 50)
Button.Position = UDim2.new(0.5, -110, 0.85, 0)
Button.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
Button.TextColor3 = Color3.new(1, 1, 1)
Button.Font = Enum.Font.SourceSansBold
Button.TextSize = 22
Button.Text = "LIGAR ESP VERMELHO"
Button.Parent = ScreenGui

local ESP_ON = false
local highlights = {}

local function addESP(plr)
    if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
        local old = plr.Character:FindFirstChild("HighlightESP")
        if old then old:Destroy() end

        local highlight = Instance.new("Highlight")
        highlight.Name = "HighlightESP"
        highlight.FillColor = Color3.fromRGB(255,0,0)
        highlight.OutlineColor = Color3.fromRGB(255,0,0)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        highlight.Adornee = plr.Character
        highlight.Parent = plr.Character
        highlights[plr] = highlight
    end
end

local function removeESP()
    for plr, highlight in pairs(highlights) do
        if highlight and highlight.Parent then
            highlight:Destroy()
        end
    end
    highlights = {}
end

local function updateESP()
    if ESP_ON then
        for _,plr in pairs(Players:GetPlayers()) do
            addESP(plr)
        end
    else
        removeESP()
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        wait(1)
        if ESP_ON then addESP(plr) end
    end)
end)

for _, plr in ipairs(Players:GetPlayers()) do
    plr.CharacterAdded:Connect(function()
        wait(1)
        if ESP_ON then addESP(plr) end
    end)
end

Button.MouseButton1Click:Connect(function()
    ESP_ON = not ESP_ON
    if ESP_ON then
        Button.Text = "DESLIGAR ESP VERMELHO"
        Button.BackgroundColor3 = Color3.fromRGB(50,200,50)
    else
        Button.Text = "LIGAR ESP VERMELHO"
        Button.BackgroundColor3 = Color3.fromRGB(200,50,50)
    end
    updateESP()
end)
