-- Criação de serviços úteis
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Função para criar objetos de forma simplificada
local function create(class, props)
	local obj = Instance.new(class)
	for i, v in pairs(props) do
		obj[i] = v
	end
	return obj
end

-- Criando a bola cinza flutuante
local screenGui = create("ScreenGui", {
	Name = "LeooHubGui",
	ResetOnSpawn = false,
	Parent = player:WaitForChild("PlayerGui")
})

-- Bola cinza (botão)
local toggleButton = create("TextButton", {
	Name = "ToggleButton",
	Parent = screenGui,
	Size = UDim2.new(0, 50, 0, 50),
	Position = UDim2.new(0, 100, 0, 100),
	BackgroundColor3 = Color3.fromRGB(120, 120, 120),
	Text = "",
	Draggable = true,
	Active = true
})

-- O HUB principal
local hubFrame = create("Frame", {
	Name = "HubFrame",
	Parent = screenGui,
	Size = UDim2.new(0, 300, 0, 400),
	Position = UDim2.new(0, 160, 0, 100),
	BackgroundColor3 = Color3.new(0, 0, 0),
	Visible = false,
	Draggable = true,
	Active = true
})

-- Título
local title = create("TextLabel", {
	Parent = hubFrame,
	Size = UDim2.new(1, 0, 0, 30),
	BackgroundTransparency = 1,
	Text = "leoo hub - feito com amor",
	TextColor3 = Color3.new(1, 1, 1),
	Font = Enum.Font.SourceSansBold,
	TextSize = 20
})

-- Lista de funções do hub
local options = {
	"pulos infinito", "noclip", "fly", "velocidade 100", "esp", "visao no escuro",
	"teleporte", "godmode", "no slow", "creditos"
}

-- Funções de cada botão (simples para testes locais)
local function activateOption(name)
	if name == "pulos infinito" then
		player.Character.Humanoid.JumpPower = 100
		player.Character.Humanoid.UseJumpPower = true
	elseif name == "noclip" then
		RunService.Stepped:Connect(function()
			if noclipActive then
				for _, part in pairs(player.Character:GetDescendants()) do
					if part:IsA("BasePart") then
						part.CanCollide = false
					end
				end
			end
		end)
		noclipActive = not noclipActive
	elseif name == "fly" then
		-- Simples fly (melhor com módulos avançados)
		local bp = Instance.new("BodyPosition", player.Character.HumanoidRootPart)
		bp.Position = player.Character.HumanoidRootPart.Position + Vector3.new(0, 10, 0)
		bp.MaxForce = Vector3.new(400000, 400000, 400000)
		wait(3)
		bp:Destroy()
	elseif name == "velocidade 100" then
		player.Character.Humanoid.WalkSpeed = 100
	elseif name == "esp" then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= player and p.Character then
				p.Character:FindFirstChildOfClass("Humanoid").DisplayDistanceType = Enum.HumanoidDisplayDistanceType.Viewer
				for _, part in pairs(p.Character:GetChildren()) do
					if part:IsA("BasePart") then
						part.BrickColor = BrickColor.Gray()
					end
				end
			end
		end
	elseif name == "visao no escuro" then
		Lighting.Brightness = 2
		Lighting.ClockTime = 14
	elseif name == "teleporte" then
		mouse.KeyDown:Connect(function(key)
			if key == "y" then
				if mouse.Hit then
					player.Character:SetPrimaryPartCFrame(mouse.Hit + Vector3.new(0, 5, 0))
				end
			end
		end)
	elseif name == "godmode" then
		player.Character.Humanoid:GetPropertyChangedSignal("Health"):Connect(function()
			player.Character.Humanoid.Health = 100
		end)
	elseif name == "no slow" then
		local hum = player.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum.StateChanged:Connect(function(old, new)
				if new == Enum.HumanoidStateType.Physics then
					hum:ChangeState(Enum.HumanoidStateType.Running)
				end
			end)
		end
	elseif name == "creditos" then
		local msg = Instance.new("Message", workspace)
		msg.Text = "Feito por: leoo :)"
		wait(3)
		msg:Destroy()
	end
end

-- Criar os botões no hub
for i, name in ipairs(options) do
	local btn = create("TextButton", {
		Parent = hubFrame,
		Size = UDim2.new(1, -20, 0, 30),
		Position = UDim2.new(0, 10, 0, 30 + (i - 1) * 35),
		BackgroundColor3 = Color3.fromRGB(30, 30, 30),
		Text = name,
		TextColor3 = Color3.new(1, 1, 1),
		Font = Enum.Font.SourceSans,
		TextSize = 18
	})

	btn.MouseButton1Click:Connect(function()
		activateOption(name)
	end)
end

-- Abrir/fechar o hub clicando na bola
toggleButton.MouseButton1Click:Connect(function()
	hubFrame.Visible = not hubFrame.Visible
end)
