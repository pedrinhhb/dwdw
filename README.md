local player = game.Players.LocalPlayer

-- Evita GUI duplicada
if player.PlayerGui:FindFirstChild("SeedSpawnerGUI") then
	player.PlayerGui.SeedSpawnerGUI:Destroy()
end

local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "SeedSpawnerGUI"
gui.ResetOnSpawn = false

-- Título
local title = Instance.new("TextLabel", gui)
title.Size = UDim2.new(0, 300, 0, 40)
title.Position = UDim2.new(0.5, -150, 0.2, -60)
title.Text = "🌱 Spawn de Sementes"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundColor3 = Color3.fromRGB(34, 139, 34)
title.BorderSizePixel = 0

-- Dropdown Frame
local dropdownFrame = Instance.new("Frame", gui)
dropdownFrame.Size = UDim2.new(0, 200, 0, 30)
dropdownFrame.Position = UDim2.new(0.5, -100, 0.2, 0)
dropdownFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
dropdownFrame.Name = "DropdownFrame"
dropdownFrame.ClipsDescendants = true

local selectedText = Instance.new("TextButton", dropdownFrame)
selectedText.Size = UDim2.new(1, 0, 1, 0)
selectedText.Text = "Escolher Semente"
selectedText.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
selectedText.TextColor3 = Color3.new(1, 1, 1)
selectedText.Font = Enum.Font.Gotham
selectedText.TextSize = 16

-- Lista de sementes
local sementes = {
	"Tomato", "Carrot", "Cabbage", "Potato", "Onion", "Broccoli", "Corn",
	"Sunflower", "Daisy", "Rose", "Tulip", "Lily",
	"Apple", "Watermelon", "Pumpkin", "Banana", "Strawberry", "Grape",
	"Fire Flower", "Ice Flower", "Crystal Carrot", "Golden Tomato",
	"Glitched Seed", "Ghost Onion", "Radioactive Potato", "Rainbow Flower",
	"Candy Blossom", "Moon Blossom"
}

-- ScrollingFrame com as opções
local scrollingFrame = Instance.new("ScrollingFrame", dropdownFrame)
scrollingFrame.Size = UDim2.new(1, 0, 0, 120)
scrollingFrame.Position = UDim2.new(0, 0, 1, 0)
scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, #sementes * 30)
scrollingFrame.ScrollBarThickness = 6
scrollingFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
scrollingFrame.Visible = false
scrollingFrame.BorderSizePixel = 0
scrollingFrame.Name = "SementeLista"
scrollingFrame.ClipsDescendants = true

-- Alternar visibilidade
local aberta = false
selectedText.MouseButton1Click:Connect(function()
	aberta = not aberta
	scrollingFrame.Visible = aberta
end)

-- Criar opções
for i, nome in ipairs(sementes) do
	local option = Instance.new("TextButton", scrollingFrame)
	option.Size = UDim2.new(1, 0, 0, 30)
	option.Position = UDim2.new(0, 0, 0, (i - 1) * 30)
	option.Text = nome
	option.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	option.TextColor3 = Color3.new(1, 1, 1)
	option.Font = Enum.Font.Gotham
	option.TextSize = 16

	option.MouseButton1Click:Connect(function()
		selectedText.Text = nome
		scrollingFrame.Visible = false
		aberta = false
	end)
end

-- Botão de Spawn
local spawnButton = Instance.new("TextButton", gui)
spawnButton.Size = UDim2.new(0, 200, 0, 40)
spawnButton.Position = UDim2.new(0.5, -100, 0.2, 80)
spawnButton.Text = "🌱 Spawnar Semente"
spawnButton.BackgroundColor3 = Color3.fromRGB(34, 139, 34)
spawnButton.TextColor3 = Color3.new(1, 1, 1)
spawnButton.Font = Enum.Font.GothamBold
spawnButton.TextSize = 18

-- Função para spawnar semente real
local function spawnarSementeReal(nome)
	local seedFolder = game.ReplicatedStorage:FindFirstChild("Seeds")
	if not seedFolder then
		warn("Pasta Seeds não encontrada em ReplicatedStorage.")
		return
	end

	local original = seedFolder:FindFirstChild(nome)
	if not original then
		warn("Semente '" .. nome .. "' não encontrada na pasta Seeds.")
		return
	end

	local clone = original:Clone()
	local char = player.Character or player.CharacterAdded:Wait()
	local pos = char:WaitForChild("HumanoidRootPart").Position + Vector3.new(0, 2, -5)

	if clone:IsA("Model") and clone.PrimaryPart then
		clone:SetPrimaryPartCFrame(CFrame.new(pos))
	elseif clone:IsA("BasePart") then
		clone.Position = pos
	end

	clone.Parent = workspace
end

-- Clique no botão
spawnButton.MouseButton1Click:Connect(function()
	local tipo = selectedText.Text
	if table.find(sementes, tipo) then
		spawnarSementeReal(tipo)
	else
		warn("Você precisa escolher uma semente válida.")
	end
end)
