--[[
Script Roblox Lua Local com interface (botão para ligar/desligar) que destaca todos os jogadores com highlight vermelho (tipo ESP).
Coloque este LocalScript em StarterPlayerScripts para funcionar corretamente.
--]]

local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "HighlightESPGui"
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão principal
local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 220, 0, 50)
button.Position = UDim2.new(0.5, -110, 0.9, 0)
button.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
button.TextColor3 = Color3.new(1, 1, 1)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 22
button.Text = "LIGAR ESP VERMELHO"
button.Parent = gui

local espAtivo = false
local highlights = {}

-- Função para adicionar highlight vermelho em todos os jogadores (menos você)
local function ligarESP()
    for _, plr in ipairs(game.Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            -- Remove highlights antigos se houver
            local old = plr.Character:FindFirstChild("RedHighlightESP")
            if old then old:Destroy() end

            local highlight = Instance.new("Highlight")
            highlight.Name = "RedHighlightESP"
            highlight.FillColor = Color3.fromRGB(255,0,0)
            highlight.OutlineColor = Color3.fromRGB(255,0,0)
            highlight.FillTransparency = 0.5
            highlight.OutlineTransparency = 0
            highlight.Adornee = plr.Character
            highlight.Parent = plr.Character
            highlights[plr] = highlight
        end
    end
end

-- Função para remover highlight de todos
local function desligarESP()
    for plr, highlight in pairs(highlights) do
        if highlight and highlight.Parent then
            highlight:Destroy()
        end
        highlights[plr] = nil
    end
end

-- Atualiza highlights ao entrar/respawn de jogadores
local function atualizarESP()
    if espAtivo then
        ligarESP()
    end
end

-- Eventos para novos jogadores e respawns
game.Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        wait(1)
        atualizarESP()
    end)
end)

for _, plr in ipairs(game.Players:GetPlayers()) do
    plr.CharacterAdded:Connect(function()
        wait(1)
        atualizarESP()
    end)
end

-- Clique do botão: liga/desliga ESP
button.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    if espAtivo then
        button.Text = "DESLIGAR ESP VERMELHO"
        button.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        ligarESP()
    else
        button.Text = "LIGAR ESP VERMELHO"
        button.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        desligarESP()
    end
end)

-- Limpeza ao sair
player.OnRemoving:Connect(function()
    gui:Destroy()
end)
