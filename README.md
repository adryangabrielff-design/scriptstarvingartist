-- AUTO STAND MENU COMPLETO (MODEL STANDS FIX)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

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
-- MENU FRAME
-------------------------------------------------
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0,260,0,170)
menuFrame.Position = UDim2.new(0,90,0,20)
menuFrame.BackgroundColor3 = Color3.fromRGB(25,25,25)
menuFrame.Visible = false
menuFrame.Parent = screenGui
Instance.new("UICorner", menuFrame)

-------------------------------------------------
-- TITULO
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
-- AUTO TOGGLE
-------------------------------------------------
local autoToggle = Instance.new("TextButton")
autoToggle.Size = UDim2.new(0.8,0,0,35)
autoToggle.Position = UDim2.new(0.1,0,0.5,0)
autoToggle.Text = "AUTO OFF"
autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
autoToggle.TextColor3 = Color3.new(1,1,1)
autoToggle.Parent = menuFrame
Instance.new("UICorner", autoToggle)

-------------------------------------------------
-- TELEPORT FORTE MODEL
-------------------------------------------------
local function teleportModel(model)

	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:WaitForChild("HumanoidRootPart")

	local cf

	if model.PrimaryPart then
		cf = model.PrimaryPart.CFrame
	else
		cf = model:GetPivot()
	end

	for i = 1, 80 do
		root.CFrame = cf + Vector3.new(0,5,0)
		RunService.Heartbeat:Wait()
	end

end

-------------------------------------------------
-- PEGAR MODELS STAND LIVRES
-------------------------------------------------
local function getFreeStandModels()

	local modelos = {}

	for _, gui in pairs(workspace:GetDescendants()) do

		if gui:IsA("BillboardGui") and gui.Adornee then

			local temLivre = false

			for _, txt in pairs(gui:GetDescendants()) do

				if txt:IsA("TextLabel") or txt:IsA("TextButton") then

					local t = string.upper(txt.Text or "")

					if t:find("UNCLAIMED") or t:find("YOUR TEXT HERE") then
						temLivre = true
						break
					end

				end

			end

			if temLivre then
				local model = gui.Adornee:FindFirstAncestorOfClass("Model")
				if model then
					table.insert(modelos, model)
				end
			end

		end

	end

	return modelos
end

-------------------------------------------------
-- AUTO CLAIM CLICKDETECTOR
-------------------------------------------------
local function autoClaim()

	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("ClickDetector") then
			pcall(function()
				fireclickdetector(v)
			end)
		end
	end

end

-------------------------------------------------
-- LOOP AUTO
-------------------------------------------------
task.spawn(function()

	while true do
		task.wait(2)

		if autoStandAtivo then

			local stands = getFreeStandModels()

			if #stands > 0 then

				local escolhido = stands[math.random(1,#stands)]

				teleportModel(escolhido)

				task.wait(1)

				autoClaim()

			end

		end

	end

end)

-------------------------------------------------
-- TOGGLE AUTO
-------------------------------------------------
autoToggle.MouseButton1Click:Connect(function()

	autoStandAtivo = not autoStandAtivo

	if autoStandAtivo then
		autoToggle.Text = "AUTO ON"
		autoToggle.BackgroundColor3 = Color3.fromRGB(0,150,0)
	else
		autoToggle.Text = "AUTO OFF"
		autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
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
