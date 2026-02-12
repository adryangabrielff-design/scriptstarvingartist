-- Script para GUI de Auto Stand
-- Coloque este script em um LocalScript dentro de StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- Variáveis do menu
local menuAberto = false
local autoStandAtivo = false

-- Criar a ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandMenu"
screenGui.Parent = player.PlayerGui

-- Criar botão de toggle do menu
local toggleButton = Instance.new("ImageButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = screenGui

-- Ícone do botão
local toggleIcon = Instance.new("TextLabel")
toggleIcon.Name = "Icon"
toggleIcon.Size = UDim2.new(1, 0, 1, 0)
toggleIcon.BackgroundTransparency = 1
toggleIcon.Text = "☰"
toggleIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleIcon.TextSize = 30
toggleIcon.Font = Enum.Font.GothamBold
toggleIcon.Parent = toggleButton

-- Criar menu principal
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 250, 0, 150)
menuFrame.Position = UDim2.new(0, 90, 0, 20)
menuFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
menuFrame.BorderSizePixel = 0
menuFrame.Visible = false
menuFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = menuFrame

-- Título
local menuTitle = Instance.new("TextLabel")
menuTitle.Name = "Title"
menuTitle.Size = UDim2.new(1, 0, 0, 35)
menuTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
menuTitle.BorderSizePixel = 0
menuTitle.Text = "AUTO STAND MENU"
menuTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
menuTitle.TextSize = 16
menuTitle.Font = Enum.Font.GothamBold
menuTitle.Parent = menuFrame

-- Container
local container = Instance.new("Frame")
container.Size = UDim2.new(1, 0, 1, -35)
container.Position = UDim2.new(0, 0, 0, 35)
container.BackgroundTransparency = 1
container.Parent = menuFrame

-- Frame opção
local autoStandFrame = Instance.new("Frame")
autoStandFrame.Size = UDim2.new(1, -20, 0, 40)
autoStandFrame.Position = UDim2.new(0, 10, 0, 10)
autoStandFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
autoStandFrame.BorderSizePixel = 0
autoStandFrame.Parent = container

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 6)
frameCorner.Parent = autoStandFrame

-- Texto
local autoStandText = Instance.new("TextLabel")
autoStandText.Size = UDim2.new(0.5, -5, 1, 0)
autoStandText.Position = UDim2.new(0, 10, 0, 0)
autoStandText.BackgroundTransparency = 1
autoStandText.Text = "AUTO STAND"
autoStandText.TextColor3 = Color3.fromRGB(255, 255, 255)
autoStandText.TextSize = 14
autoStandText.TextXAlignment = Enum.TextXAlignment.Left
autoStandText.Font = Enum.Font.Gotham
autoStandText.Parent = autoStandFrame

-- Botão toggle
local toggleAutoStand = Instance.new("TextButton")
toggleAutoStand.Size = UDim2.new(0.5, -10, 0, 30)
toggleAutoStand.Position = UDim2.new(0.5, 5, 0.5, -15)
toggleAutoStand.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
toggleAutoStand.BorderSizePixel = 0
toggleAutoStand.Text = "OFF"
toggleAutoStand.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleAutoStand.TextSize = 14
toggleAutoStand.Font = Enum.Font.Gotham
toggleAutoStand.Parent = autoStandFrame

local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 4)
buttonCorner.Parent = toggleAutoStand

-- Indicador
local statusIndicator = Instance.new("Frame")
statusIndicator.Size = UDim2.new(0, 10, 0, 10)
statusIndicator.Position = UDim2.new(1, -20, 0.5, -5)
statusIndicator.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
statusIndicator.BackgroundTransparency = 0.3
statusIndicator.BorderSizePixel = 0
statusIndicator.Parent = autoStandFrame

local indicatorCorner = Instance.new("UICorner")
indicatorCorner.CornerRadius = UDim.new(1, 0)
indicatorCorner.Parent = statusIndicator

-------------------------------------------------
-- FUNÇÃO AUTO STAND (ARRUMADA)
-------------------------------------------------
local function autoStand()
	if not autoStandAtivo then return end
	
	print("🔍 Procurando stands UNCLAIMED...")

	local unclaimedStands = {}

	for _, obj in pairs(workspace:GetDescendants()) do
		if obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("BillboardGui") then
			
			local text = ""
			
			if obj:IsA("BillboardGui") then
				local label = obj:FindFirstChildWhichIsA("TextLabel")
				if label then text = label.Text end
			else
				text = obj.Text or ""
			end
			
			if string.find(string.lower(text), "unclaimed") then
				local standPart = obj:FindFirstAncestorWhichIsA("BasePart")
				if standPart then
					table.insert(unclaimedStands, standPart)
					print("✅ Stand UNCLAIMED encontrado:", standPart.Name)
				end
			end
		end
	end

	if #unclaimedStands == 0 then
		print("❌ Nenhum stand UNCLAIMED encontrado")
		return
	end

	-- Escolher stand aleatório
	local standEscolhido = unclaimedStands[math.random(1, #unclaimedStands)]

	-- Teleportar
	local character = player.Character or player.CharacterAdded:Wait()
	local hrp = character:WaitForChild("HumanoidRootPart")

	hrp.CFrame = standEscolhido.CFrame * CFrame.new(0, 3, 0)
	print("🚀 Teleportado para stand")

	task.wait(1)

	-- Procurar botão Reivindicar
	for _, obj in pairs(standEscolhido.Parent:GetDescendants()) do
		if obj:IsA("TextButton") then
			if string.find(string.lower(obj.Text), "ESTANTE") then
				print("🖱️ Botão Reivindicar encontrado")
				obj:Activate()
				break
			end
		end
	end

	print("✨ Processo concluído")

	autoStandAtivo = false
	toggleAutoStand.Text = "OFF"
	toggleAutoStand.BackgroundColor3 = Color3.fromRGB(60,60,60)
	statusIndicator.BackgroundColor3 = Color3.fromRGB(255,0,0)
	statusIndicator.BackgroundTransparency = 0.3
end

-------------------------------------------------
-- TOGGLE
-------------------------------------------------
local function alternarAutoStand()
	autoStandAtivo = not autoStandAtivo
	
	if autoStandAtivo then
		toggleAutoStand.Text = "ON"
		toggleAutoStand.BackgroundColor3 = Color3.fromRGB(0,150,0)
		statusIndicator.BackgroundColor3 = Color3.fromRGB(0,255,0)
		statusIndicator.BackgroundTransparency = 0
		autoStand()
	else
		toggleAutoStand.Text = "OFF"
	end
end

toggleAutoStand.MouseButton1Click:Connect(alternarAutoStand)

-------------------------------------------------
-- MENU
-------------------------------------------------
local function alternarMenu()
	menuAberto = not menuAberto
	menuFrame.Visible = menuAberto
end

toggleButton.MouseButton1Click:Connect(alternarMenu)

UserInputService.InputBegan:Connect(function(input, gp)
	if not gp and input.KeyCode == Enum.KeyCode.F then
		alternarMenu()
	end
end)
