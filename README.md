-- AUTO STAND MENU COMPLETO (TELEPORT RANDOM + AUTO CLAIM CLICKDETECTOR)

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
-- GUI BASE
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
toggleButton.TextSize = 28
toggleButton.BackgroundColor3 = Color3.fromRGB(40,40,40)
toggleButton.TextColor3 = Color3.new(1,1,1)
toggleButton.Parent = screenGui

-------------------------------------------------
-- MENU FRAME
-------------------------------------------------
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0,260,0,160)
menuFrame.Position = UDim2.new(0,90,0,20)
menuFrame.BackgroundColor3 = Color3.fromRGB(25,25,25)
menuFrame.Visible = false
menuFrame.Parent = screenGui
Instance.new("UICorner", menuFrame)

-------------------------------------------------
-- TITULO
-------------------------------------------------
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,35)
title.BackgroundColor3 = Color3.fromRGB(45,45,45)
title.Text = "AUTO STAND MENU"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.Parent = menuFrame

-------------------------------------------------
-- BOTÃO FECHAR
-------------------------------------------------
local close = Instance.new("TextButton")
close.Size = UDim2.new(0,30,0,25)
close.Position = UDim2.new(1,-35,0,5)
close.Text = "X"
close.BackgroundColor3 = Color3.fromRGB(170,0,0)
close.TextColor3 = Color3.new(1,1,1)
close.Parent = menuFrame
Instance.new("UICorner", close)

-------------------------------------------------
-- AUTO BOTÃO
-------------------------------------------------
local autoToggle = Instance.new("TextButton")
autoToggle.Size = UDim2.new(0.9,0,0,40)
autoToggle.Position = UDim2.new(0.05,0,0.4,0)
autoToggle.Text = "AUTO STAND OFF"
autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
autoToggle.TextColor3 = Color3.new(1,1,1)
autoToggle.Parent = menuFrame
Instance.new("UICorner", autoToggle)

-------------------------------------------------
-- TELEPORT FORCE REAL
-------------------------------------------------
local function teleportForce(cf)
	local char = player.Character or player.CharacterAdded:Wait()

	for i = 1, 40 do
		if char then
			char:PivotTo(cf + Vector3.new(0,3,0))
		end
		RunService.Heartbeat:Wait()
	end
end

-------------------------------------------------
-- ACHAR STANDS LIVRES
-------------------------------------------------
local function getFreeStands()

	local stands = {}

	for _, v in pairs(workspace:GetDescendants()) do
		
		if v:IsA("BillboardGui") and v.Adornee then
			
			local temUnclaimed = false
			local temYour = false
			
			for _, txt in pairs(v:GetDescendants()) do
				if txt:IsA("TextLabel") or txt:IsA("TextButton") then
					
					local t = string.upper(txt.Text or "")
					
					if t:find("UNCLAIMED") then
						temUnclaimed = true
					end
					
					if t:find("YOUR TEXT HERE") then
						temYour = true
					end
				end
			end
			
			if temUnclaimed or temYour then
				table.insert(stands, v.Adornee)
			end
		end
	end

	return stands
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
		task.wait(1)

		if autoStandAtivo then
			
			local stands = getFreeStands()
			
			if #stands > 0 then
				
				local escolhido = stands[math.random(1,#stands)]
				
				teleportForce(escolhido.CFrame)
				
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
		autoToggle.Text = "AUTO STAND ON"
		autoToggle.BackgroundColor3 = Color3.fromRGB(0,150,0)
	else
		autoToggle.Text = "AUTO STAND OFF"
		autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
	end

end)

-------------------------------------------------
-- MENU BOTÕES
-------------------------------------------------
toggleButton.MouseButton1Click:Connect(function()
	menuAberto = not menuAberto
	menuFrame.Visible = menuAberto
end)

close.MouseButton1Click:Connect(function()
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
