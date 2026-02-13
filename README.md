-- AUTO STAND MENU COMPLETO (FORÇA MÁXIMA)

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
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0,50,0,50)
toggleButton.Position = UDim2.new(0,20,0,20)
toggleButton.Text = "☰"
toggleButton.BackgroundColor3 = Color3.fromRGB(40,40,40)
toggleButton.TextColor3 = Color3.new(1,1,1)
toggleButton.Parent = screenGui

-------------------------------------------------
-- MENU
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
menuTitle.Parent = menuFrame

-------------------------------------------------
-- BOTÃO AUTO
-------------------------------------------------
local autoToggle = Instance.new("TextButton")
autoToggle.Size = UDim2.new(0.8,0,0,40)
autoToggle.Position = UDim2.new(0.1,0,0.5,0)
autoToggle.Text = "AUTO OFF"
autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
autoToggle.TextColor3 = Color3.new(1,1,1)
autoToggle.Parent = menuFrame
Instance.new("UICorner", autoToggle)

-------------------------------------------------
-- TELEPORT FORÇA BRUTA
-------------------------------------------------
local function forceTeleport(cf)

	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:WaitForChild("HumanoidRootPart")

	for i = 1,120 do
		root.CFrame = cf + Vector3.new(0,6,0)
		RunService.Heartbeat:Wait()
	end

end

-------------------------------------------------
-- PEGAR POSIÇÃO QUALQUER DE MODEL
-------------------------------------------------
local function getModelPosition(model)

	if model.PrimaryPart then
		return model.PrimaryPart.CFrame
	end

	local cf,size = model:GetBoundingBox()
	return cf

end

-------------------------------------------------
-- ACHAR STANDS
-------------------------------------------------
local function getStands()

	local lista = {}

	for _, obj in pairs(workspace:GetDescendants()) do

		if obj:IsA("Model") then

			local temTexto = false

			for _, d in pairs(obj:GetDescendants()) do

				if d:IsA("TextLabel") or d:IsA("TextButton") then

					local t = string.upper(d.Text or "")

					if t:find("UNCLAIMED") or t:find("YOUR TEXT HERE") then
						temTexto = true
						break
					end

				end

			end

			if temTexto then
				table.insert(lista,obj)
			end

		end

	end

	return lista
end

-------------------------------------------------
-- CLICK GUI REIVINDICAR
-------------------------------------------------
local function clickReivindicarGUI()

	for _, gui in pairs(player.PlayerGui:GetDescendants()) do

		if gui:IsA("TextButton") then

			local t = string.upper(gui.Text or "")

			if t:find("REIVINDICAR") then

				local pos = gui.AbsolutePosition
				local size = gui.AbsoluteSize

				local x = pos.X + size.X/2
				local y = pos.Y + size.Y/2

				VirtualInputManager:SendMouseButtonEvent(x,y,0,true,game,0)
				VirtualInputManager:SendMouseButtonEvent(x,y,0,false,game,0)

			end

		end

	end

end

-------------------------------------------------
-- CLICK CLICKDETECTOR
-------------------------------------------------
local function spamClickDetector()

	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("ClickDetector") then
			pcall(function()
				fireclickdetector(v)
			end)
		end
	end

end

-------------------------------------------------
-- LOOP AUTO FORÇA MÁXIMA
-------------------------------------------------
task.spawn(function()

	while true do
		task.wait(1)

		if autoStandAtivo then

			local stands = getStands()

			if #stands > 0 then

				local escolhido = stands[math.random(1,#stands)]

				local pos = getModelPosition(escolhido)

				forceTeleport(pos)

				task.wait(0.5)

				for i = 1,10 do
					spamClickDetector()
					clickReivindicarGUI()
					task.wait(0.2)
				end

			end

		end

	end

end)

-------------------------------------------------
-- TOGGLE
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

UserInputService.InputBegan:Connect(function(input,gp)
	if not gp and input.KeyCode == Enum.KeyCode.F then
		menuAberto = not menuAberto
		menuFrame.Visible = menuAberto
	end
end)
