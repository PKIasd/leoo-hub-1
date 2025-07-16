-- Variables, change as needed
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "LeooHub"

-- Hub frame
local hub = Instance.new("Frame", gui)
hub.Size = UDim2.new(0, 300, 0, 400)
hub.Position = UDim2.new(0.5, -150, 0.5, -200)
hub.BackgroundColor3 = Color3.new(0,0,0)
hub.Active = true
hub.Draggable = true

-- Title
local title = Instance.new("TextLabel", hub)
title.Size = UDim2.new(1,0,0,50)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1,1,1)
title.Text = "Leoo Hub - feito com amor"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24

-- Botão de trocar página
local nextPage = Instance.new("TextButton", hub)
nextPage.Size = UDim2.new(1,0,0,40)
nextPage.Position = UDim2.new(0,0,1,-40)
nextPage.Text = "Ir para próxima página"
nextPage.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
nextPage.TextColor3 = Color3.new(1,1,1)

-- Página de controle
local page1 = Instance.new("Frame", hub)
page1.Size = UDim2.new(1,0,0,300)
page1.Position = UDim2.new(0,0,0,50)
page1.BackgroundTransparency = 1

local page2 = Instance.new("Frame", hub)
page2.Size = page1.Size
page2.Position = page1.Position
page2.BackgroundTransparency = 1
page2.Visible = false

-- Lista de opções
local options1 = {
    "Pulo Infinito", "Noclip", "Fly", "Velocidade 100",
    "ESP", "Visão no Escuro", "Teleporte", "GodMode",
    "No Slow", "Créditos", "Leoo Info"
}
local buttons1 = {}

for i,opt in ipairs(options1) do
    local btn = Instance.new("TextButton", page1)
    btn.Size = UDim2.new(0.9,0,0,25)
    btn.Position = UDim2.new(0.05,0,0,(i-1)*28)
    btn.Text = opt
    btn.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Name = opt:gsub("%s+","")
    buttons1[opt] = btn
end

-- Botões da página 2
local options2 = {"Fly Ilimitado", "Kill Aura", "Resetar"}
local buttons2 = {}
for i,opt in ipairs(options2) do
    local btn = Instance.new("TextButton", page2)
    btn.Size = UDim2.new(0.9,0,0,25)
    btn.Position = UDim2.new(0.05,0,0,(i-1)*28)
    btn.Text = opt
    btn.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Name = opt:gsub("%s+","")
    buttons2[opt] = btn
end

-- Botão voltar
local backBtn = Instance.new("TextButton", page2)
backBtn.Size = nextPage.Size
backBtn.Position = nextPage.Position
backBtn.Text = "Voltar para página 1"
backBtn.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
backBtn.TextColor3 = Color3.new(1,1,1)

-- Bola cinza
local ball = Instance.new("Part", workspace)
ball.Shape = Enum.PartType.Ball
ball.Color = Color3.fromRGB(128,128,128)
ball.Size = Vector3.new(2,2,2)
ball.Anchored = false
ball.CanCollide = false
ball.Position = player.Character and player.Character.Head.Position + Vector3.new(0,5,0) or Vector3.new(0,5,0)

ball.Touched:Connect(function() end) -- necessário pra poder clicável via MouseButton1Click

-- Click detector
local click = Instance.new("ClickDetector", ball)
local hubOpen = false

click.MouseClick:Connect(function()
    hubOpen = not hubOpen
    gui.Enabled = hubOpen
end)

-- scripts das opções
buttons1["PuloInfinito"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 == Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0,0.5,0) or Color3.new(0.2,0.2,0.2)
    player.Character.Humanoid.JumpPower = ond and 1000 or 50
end)

buttons1["Noclip"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    local c = player.Character:GetChildren()
    for _, part in ipairs(c) do
        if part:IsA("BasePart") then part.CanCollide = not ond end
    end
end)

buttons1["Fly"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    -- implementação simples
    local hs = player.Character.Humanoid
    hs.PlatformStand = not ond
end)

buttons1["Velocidade100"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    player.Character.Humanoid.WalkSpeed = ond and 100 or 16
end)

buttons1["ESP"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    for _, pl in ipairs(game.Players:GetPlayers()) do
        if pl ~= player and pl.Character and pl.Character:FindFirstChild("Head") then
            pl.Character.Head.Color = ond and Color3.new(0.5,0.5,0.5) or Color3.new(1,1,1)
        end
    end
end)

buttons1["VisãonoEscuro"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    game.Lighting.TimeOfDay = ond and "12:00:00" or "00:00:00"
end)

buttons1["Teleporte"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0.2) or Color3.new(0,0.5,0)
    if ond then
        mouse.KeyDown:Connect(function(key)
            if key == "y" then
                local ray = workspace:Raycast(player.Character.Head.Position, (mouse.Hit.p - player.Character.Head.Position).Unit * 500)
                if ray then player.Character:SetPrimaryPartCFrame(CFrame.new(ray.Position + Vector3.new(0,3,0))) end
            end
        end)
    end
end)

buttons1["GodMode"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0.2,0.2,0) or Color3.new(0.2,0.2,0.2)
    player.Character.Humanoid:SetAttribute("Invulnerable", ond)
    -- você precisaria interceptar dano via outra lógica
end)

buttons1["NoSlow"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0,0.5,0) or Color3.new(0.2,0.2,0.2)
    -- bloqueia lentidão implicando em override de velocidade
end)

buttons1["Créditos"].MouseButton1Click:Connect(function(btn)
    print("Leoo")
end)

buttons1["LeooInfo"].MouseButton1Click:Connect(function(btn)
    print("Leoo")
end)

-- página 2
buttons2["FlyIlimitado"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0,0.5,0) or Color3.new(0.2,0.2,0.2)
    -- lógica semelhante ao Fly porém infinitamente
end)

buttons2["KillAura"].MouseButton1Click:Connect(function(btn)
    local ond = btn.BackgroundColor3 ~= Color3.new(0.2,0.2,0.2)
    btn.BackgroundColor3 = ond and Color3.new(0,0.5,0) or Color3.new(0.2,0.2,0.2)
    -- lógica: detecta jogadores próximos e aplica dano periodicamente
end)

buttons2["Resetar"].MouseButton1Click:Connect(function(btn)
    btn.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
    player.Character:BreakJoints()
end)

-- troca de páginas
nextPage.MouseButton1Click:Connect(function()
    page1.Visible = false
    page2.Visible = true
end)
backBtn.MouseButton1Click:Connect(function()
    page1.Visible = true
    page2.Visible = false
end)

-- Start GUI hidden
gui.Enabled = false
