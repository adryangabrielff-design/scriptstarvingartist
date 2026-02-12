local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Criar ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "StandMenu"
gui.Parent = playerGui

-- Botão abrir/fechar menu
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0,120,0,40)
toggleButton.Position = UDim2.new(0,10,0,10)
toggleButton.Text = "Menu"
toggleButton.Parent = gui

-- Frame do menu
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0,200,0,150)
menuFrame.Position = UDim2.new(0,10,0,60)
menuFrame.BackgroundColor3 = Color3.fromRGB(40,40,40)
menuFrame.Visible = true
menuFrame.Parent = gui

-- Botão Auto Stand
local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0,180,0,40)
autoStandButton.Position = UDim2.new(0,10,0,10)
autoStandButton.Text = "Auto Stand: OFF"
autoStandButton.Parent = menuFrame

local autoStandOn = false

autoStandButton.MouseButton1Click:Connect(function()
	autoStandOn = not autoStandOn
	
	if autoStandOn then
		autoStandButton.Text = "Auto Stand: ON"
	else
		autoStandButton.Text = "Auto Stand: OFF"
	end
end)

-- Toggle menu abrir/fechar
toggleButton.MouseButton1Click:Connect(function()
	menuFrame.Visible = not menuFrame.Visible
end)

-- Função Auto Stand
task.spawn(function()
	while true do
		if autoStandOn then
			
			local stands = {}

			for _, obj in pairs(workspace:GetDescendants()) do
				if obj.Name == "Stand" and obj:IsA("BasePart") then
					table.insert(stands, obj)
				end
			end

			if #stands > 0 then
				local randomStand = stands[math.random(1,#stands)]
				
				local character = player.Character
				if character and character:FindFirstChild("HumanoidRootPart") then
					character.HumanoidRootPart.CFrame = randomStand.CFrame + Vector3.new(0,3,0)
				end
			end
		end
		
		task.wait(3)
	end
end)
