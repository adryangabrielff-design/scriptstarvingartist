-- AUTO STAND MENU COMPLETO (TELEPORTE + AUTO CLAIM)

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
-- AUTO FRAME
-------------------------------------------------
local container = Instance.new("Frame")
container.Size = UDim2.new(1,0,1,-35)
container.Position = UDim2.new(0,0,0,35)
container.BackgroundTransparency = 1
container.Parent = menuFrame

local autoFrame = Instance.new("Frame")
autoFrame.Size = UDim2.new(1,-20,0,40)
autoFrame.Position = UDim2.new(0,10,0,10)
autoFrame.BackgroundColor3 = Color3.fromRGB(35,35,35)
autoFrame.Parent = container
Instance.new("UICorner", autoFrame)

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
-- TELEPORTE FORÇADO
-------------------------------------------------
local function teleportForce(cf)

	local char = player.Character or player.CharacterAdded:Wait()

	for i = 1, 30 do
		if char then
			char:PivotTo(cf + Vector3.new(0,3,0))
		end
		RunService.Heartbeat:Wait()
	end
end

-------------------------------------------------
-- AUTO CLAIM
-------------------------------------------------
local function autoClaim()

	local char = player.Character
	if not char then return end

	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	-- tenta proximity prompt
	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("ProximityPrompt") then
			if (v.Parent.Position - hrp.Position).Magnitude < 15 then
				fireproximityprompt(v)
			end
		end
	end

	-- tenta touch claim
	for _, part in pairs(workspace:GetDescendants()) do
		if part:IsA("BasePart") then
			if (part.Position - hrp.Position).Magnitude < 10 then
				firetouchinterest(hrp, part, 0)
				firetouchinterest(hrp, part, 1)
			end
		end
	end
end

-------------------------------------------------
-- AUTO STAND
-------------------------------------------------
local function autoStand()

	if not autoStandAtivo then return end

	local destinos = {}

	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("TextLabel") or v:IsA("TextButton") then
			
			if v.Text then
				local txt = string.upper(v.Text)

				if string.find(txt, "UNCLAIMED") or string.find(txt, "YOUR TEXT HERE") then
					
					local gui = v:FindFirstAncestorOfClass("BillboardGui")
					if gui and gui.Adornee then
						table.insert(destinos, gui.Adornee.CFrame)
					end
				end
			end
		end
	end

	if #destinos > 0 then
		local destino = destinos[math.random(1, #destinos)]
		teleportForce(destino)

		task.wait(0.5)
		autoClaim()
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
		autoStand()
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

-------------------------------------------------
-- TECLA F
-------------------------------------------------
UserInputService.InputBegan:Connect(function(input, gp)
	if not gp and input.KeyCode == Enum.KeyCode.F then
		menuAberto = not menuAberto
		menuFrame.Visible = menuAberto
	end
end)
