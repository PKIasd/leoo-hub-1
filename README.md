-- Serviços necessários
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Função para criação simplificada
local function create(class, props)
	local obj = Instance.new(class)
	for i, v in pairs(props) do
		obj[i] = v
	end
	return obj
end

-- Gui Principal
local screenGui = create("ScreenGui", {
	Name = "LeooHubGui",
	ResetOnSpawn = false,
	Parent = player:WaitForChild("PlayerGui")
})

-- Bola cinza flutuante
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

-- HUB
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
create("TextLabel", {
	Parent = hubFrame,
	Size = UDim2.new(1, 0, 0, 30),
	BackgroundTransparency = 1,
	Text = "leoo hub - feito com amor",
	TextColor3 = Color3.new(1, 1, 1),
	Font = Enum.Font.SourceSansBold,
	TextSize = 20
})

-- Estados das opções (toggle)
local optionStates = {
	["pulos infinito"] = false,
	["noclip"] = false,
	["fly"] = false,
	["velocidade 100"] = false,
	["esp"] = false,
	["visao no escuro"] = false,
	["teleporte"] = false,
	["godmode"] = false,
	["no slow"] = false,
	["creditos"] = false
}

-- Armazenar conexões para desconectar depois
local activeConnections = {}

-- Lógica das opções
local function toggleOption(name)
	local state = optionStates[name]
	optionStates[name] = not state

	if name == "pulos infinito" then
		if not state then
			activeConnections["jump"] = RunService.RenderStepped:Connect(function()
				player.Character.Humanoid.JumpPower = 150
			end)
		else
			if activeConnections["jump"] then activeConnections["jump"]:Disconnect() end
			player.Character.Humanoid.JumpPower = 50
		end

	elseif name == "noclip" then
		if not state then
			activeConnections["noclip"] = RunService.Stepped:Connect(function()
				for _, part in pairs(player.Character:GetDescendants()) do
					if part:IsA("BasePart") then part.CanCollide = false end
				end
			end)
		else
			if activeConnections["noclip"] then activeConnections["noclip"]:Disconnect() end
		end

	elseif name == "fly" then
		if not state then
			local root = player.Character:FindFirstChild("HumanoidRootPart")
			if root then
				local bp = Instance.new("BodyPosition", root)
				bp.Name = "FlyPosition"
				bp.MaxForce = Vector3.new(400000, 400000, 400000)
				activeConnections["fly"] = RunService.RenderStepped:Connect(function()
					bp.Position = root.Position + Vector3.new(0, 10, 0)
				end)
			end
		else
			if activeConnections["fly"] then activeConnections["fly"]:Disconnect() end
			local root = player.Character:FindFirstChild("HumanoidRootPart")
			if root then
				local bp = root:FindFirstChild("FlyPosition")
				if bp then bp:Destroy() end
			end
		end

	elseif name == "velocidade 100" then
		player.Character.Humanoid.WalkSpeed = state and 16 or 100

	elseif name == "esp" then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= player and p.Character then
				for _, part in pairs(p.Character:GetChildren()) do
					if part:IsA("BasePart") then
						part.BrickColor = state and BrickColor.Random() or BrickColor.Gray()
					end
				end
			end
		end

	elseif name == "visao no escuro" then
		Lighting.ClockTime = state and 0 or 14
		Lighting.Brightness = state and 1 or 2

	elseif name == "teleporte" then
		if not state then
			activeConnections["teleport"] = mouse.KeyDown:Connect(function(key)
				if key == "y" and mouse.Hit then
					player.Character:SetPrimaryPartCFrame(mouse.Hit + Vector3.new(0, 5, 0))
				end
			end)
		else
			if activeConnections["teleport"] then activeConnections["teleport"]:Disconnect() end
		end

	elseif name == "godmode" then
		if not state then
			activeConnections["god"] = player.Character.Humanoid:GetPropertyChangedSignal("Health"):Connect(function()
				player.Character.Humanoid.Health = 100
			end)
		else
			if activeConnections["god"] then activeConnections["god"]:Disconnect() end
		end

	elseif name == "no slow" then
		if not state then
			activeConnections["noslow"] = player.Character:FindFirstChildOfClass("Humanoid").StateChanged:Connect(function(old, new)
				if new == Enum.HumanoidStateType.Physics then
					player.Character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Running)
				end
			end)
		else
			if activeConnections["noslow"] then activeConnections["noslow"]:Disconnect() end
		end

	elseif name == "creditos" then
		if not state then
			local msg = Instance.new("Message", workspace)
			msg.Text = "Feito por: leoo :)"
			wait(3)
			msg:Destroy()
			optionStates["creditos"] = false
		end
	end
end

-- Lista de botões
local options = {
	"pulos infinito", "noclip", "fly", "velocidade 100", "esp", "visao no escuro",
	"teleporte", "godmode", "no slow", "creditos"
}

-- Criação dos botões no Hub
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
		toggleOption(name)
	end)
end

-- Alternar visibilidade do Hub
toggleButton.MouseButton1Click:Connect(function()
	hubFrame.Visible = not hubFrame.Visible
end)
