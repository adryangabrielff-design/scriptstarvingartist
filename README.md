-- AUTO STAND MENU COMPLETO (UNCLAIMED + YOUR TEXT HERE)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

-------------------------------------------------
-- VARIÁVEIS
-------------------------------------------------
local menuAberto = false
local autoStandAtivo = false

-------------------------------------------------
-- GUI
-------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandMenu"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- BOTÃO MENU
-------------------------------------------------
local toggleButton = Instance.new("ImageButton")
toggleButton.Size = UDim2.new(0,50,0,50)
toggleButton.Position = UDim2.new(0,20,0,20)
toggleButton.BackgroundColor3 = Color3.fromRGB(35,35,35)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = screenGui

local toggleIcon = Instance.new("TextLabel")
toggleIcon.Size = UDim2.new(1,0,1,0)
toggleIcon.BackgroundTransparency = 1
toggleIcon.Text = "☰"
toggleIcon.TextColor3 = Color3.new(1,1,1)
toggleIcon.TextSize = 30
toggleIcon.Font = Enum.Font.GothamBold
toggleIcon.Parent = toggleButton

-------------------------------------------------
-- FRAME MENU
-------------------------------------------------
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0,260,0,170)
menuFrame.Position = UDim2.new(0,90,0,20)
menuFrame.BackgroundColor3 = Color3.fromRGB(25,25,25)
menuFrame.Visible = false
menuFrame.Parent = screenGui
Instance.new("UICorner", menuFrame)

-------------------------------------------------
-- TÍTULO
-------------------------------------------------
local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1,0,0,35)
menuTitle.BackgroundColor3 = Color3.fromRGB(45,45,45)
menuTitle.Text = "AUTO STAND MENU"
menuTitle.TextColor3 = Color3.new(1,1,1)
menuTitle.TextSize = 16
menuTitle.Font = Enum.Font.GothamBold
menuTitle.Parent = menuFrame

-------------------------------------------------
-- BOTÕES
-------------------------------------------------
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0,30,0,25)
minimizeButton.Position = UDim2.new(1,-70,0,5)
minimizeButton.Text = "-"
minimizeButton.BackgroundColor3 = Color3.fromRGB(70,70,70)
minimizeButton.TextColor3 = Color3.new(1,1,1)
minimizeButton.Parent = menuFrame
Instance.new("UICorner", minimizeButton)

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0,30,0,25)
closeButton.Position = UDim2.new(1,-35,0,5)
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(170,0,0)
closeButton.TextColor3 = Color3.new(1,1,1)
closeButton.Parent = menuFrame
Instance.new("UICorner", closeButton)

-------------------------------------------------
-- CONTAINER
-------------------------------------------------
local container = Instance.new("Frame")
container.Size = UDim2.new(1,0,1,-35)
container.Position = UDim2.new(0,0,0,35)
container.BackgroundTransparency = 1
container.Parent = menuFrame

-------------------------------------------------
-- AUTO FRAME
-------------------------------------------------
local autoFrame = Instance.new("Frame")
autoFrame.Size = UDim2.new(1,-20,0,40)
autoFrame.Position = UDim2.new(0,10,0,10)
autoFrame.BackgroundColor3 = Color3.fromRGB(35,35,35)
autoFrame.Parent = container
Instance.new("UICorner", autoFrame)

local autoText = Instance.new("TextLabel")
autoText.Size = UDim2.new(0.5,-5,1,0)
autoText.Position = UDim2.new(0,10,0,0)
autoText.BackgroundTransparency = 1
autoText.Text = "AUTO STAND"
autoText.TextColor3 = Color3.new(1,1,1)
autoText.Parent = autoFrame

local autoToggle = Instance.new("TextButton")
autoToggle.Size = UDim2.new(0.5,-10,0,30)
autoToggle.Position = UDim2.new(0.5,5,0.5,-15)
autoToggle.Text = "OFF"
autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
autoToggle.TextColor3 = Color3.new(1,1,1)
autoToggle.Parent = autoFrame
Instance.new("UICorner", autoToggle)

local status = Instance.new("Frame")
status.Size = UDim2.new(0,10,0,10)
status.Position = UDim2.new(1,-20,0.5,-5)
status.BackgroundColor3 = Color3.fromRGB(255,0,0)
status.BackgroundTransparency = 0.3
status.Parent = autoFrame
Instance.new("UICorner", status).CornerRadius = UDim.new(1,0)

-------------------------------------------------
-- TELEPORT FORÇADO
-------------------------------------------------
local function teleportForce(cf)
	local char = player.Character or player.CharacterAdded:Wait()
	for i = 1, 25 do
		if char then
			char:PivotTo(cf + Vector3.new(0,3,0))
		end
		RunService.Heartbeat:Wait()
	end
end

-------------------------------------------------
-- AUTO CLAIM SUPER FORÇADO
-------------------------------------------------
local function pressE()
	VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
	task.wait(0.1)
	VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
end

local function clickClaimGUI()
	local pg = player:FindFirstChild("PlayerGui")
	if not pg then return end

	for _, v in pairs(pg:GetDescendants()) do
		if v:IsA("TextButton") then
			local txt = string.upper(v.Text or "")
			if string.find(txt, "REIVIND") then
				pcall(function()
					v:Activate()
					v.MouseButton1Click:Fire()
				end)
				return true
			end
		end
	end
end

local function clickDetectorClaim()
	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("ClickDetector") then
			pcall(function()
				fireclickdetector(v)
			end)
		end
	end
end

local function forceClaim()
	for i = 1, 6 do
		clickClaimGUI()
		clickDetectorClaim()
		pressE()
		task.wait(0.3)
	end
end

-------------------------------------------------
-- AUTO STAND CORRIGIDO
-------------------------------------------------
local function autoStandLoop()
	while autoStandAtivo do

		local destinos = {}

		for _, v in pairs(workspace:GetDescendants()) do
			if v:IsA("TextLabel") or v:IsA("TextButton") then
				if v.Text then
					local txt = string.upper(v.Text)

					if string.find(txt, "UNCLAIMED") or string.find(txt, "YOUR TEXT HERE") then
						local billboard = v:FindFirstAncestorWhichIsA("BillboardGui")
						if billboard and billboard.Adornee then
							table.insert(destinos, billboard.Adornee)
						end
					end
				end
			end
		end

		if #destinos > 0 then
			local standPart = destinos[math.random(1,#destinos)]
			teleportForce(standPart.CFrame)
			task.wait(0.8)
			forceClaim()
		end

		task.wait(2)
	end
end

-------------------------------------------------
-- TOGGLE AUTO
-------------------------------------------------
autoToggle.MouseButton1Click:Connect(function()

	autoStandAtivo = not autoStandAtivo

	if autoStandAtivo then
		autoToggle.Text = "ON"
		autoToggle.BackgroundColor3 = Color3.fromRGB(0,150,0)
		status.BackgroundColor3 = Color3.fromRGB(0,255,0)
		status.BackgroundTransparency = 0
		task.spawn(autoStandLoop)
	else
		autoToggle.Text = "OFF"
		autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
		status.BackgroundColor3 = Color3.fromRGB(255,0,0)
		status.BackgroundTransparency = 0.3
	end
end)

-------------------------------------------------
-- MENU
-------------------------------------------------
toggleButton.MouseButton1Click:Connect(function()
	menuAberto = not menuAberto
	menuFrame.Visible = menuAberto
end)

minimizeButton.MouseButton1Click:Connect(function()
	menuFrame.Visible = false
	menuAberto = false
end)

closeButton.MouseButton1Click:Connect(function()
	screenGui:Destroy()
end)

-------------------------------------------------
-- TECLA F
-------------------------------------------------
UserInputService.InputBegan:Connect(function(input, gp)
	if not gp and input.KeyCode == Enum.KeyCode.F then
		menuAberto = not menuAberto
		menuFrame.Visible = menuAberto
	end
end)
