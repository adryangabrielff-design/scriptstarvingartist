-- AUTO STAND MENU COMPLETO (SEGURO)
-- Coloque em LocalScript dentro de StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-------------------------------------------------
-- VARIÁVEIS
-------------------------------------------------
local menuAberto = false
local autoStandAtivo = false

-------------------------------------------------
-- GUI PRINCIPAL
-------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandMenu"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- BOTÃO TOGGLE MENU (☰)
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

Instance.new("UICorner", menuFrame).CornerRadius = UDim.new(0,8)

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
-- BOTÃO MINIMIZAR (-)
-------------------------------------------------
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0,30,0,25)
minimizeButton.Position = UDim2.new(1,-70,0,5)
minimizeButton.Text = "-"
minimizeButton.BackgroundColor3 = Color3.fromRGB(70,70,70)
minimizeButton.TextColor3 = Color3.new(1,1,1)
minimizeButton.Font = Enum.Font.GothamBold
minimizeButton.Parent = menuFrame
Instance.new("UICorner", minimizeButton)

-------------------------------------------------
-- BOTÃO FECHAR (X)
-------------------------------------------------
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0,30,0,25)
closeButton.Position = UDim2.new(1,-35,0,5)
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(170,0,0)
closeButton.TextColor3 = Color3.new(1,1,1)
closeButton.Font = Enum.Font.GothamBold
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
-- AUTO STAND FRAME
-------------------------------------------------
local autoFrame = Instance.new("Frame")
autoFrame.Size = UDim2.new(1,-20,0,40)
autoFrame.Position = UDim2.new(0,10,0,10)
autoFrame.BackgroundColor3 = Color3.fromRGB(35,35,35)
autoFrame.Parent = container
Instance.new("UICorner", autoFrame)

-------------------------------------------------
-- TEXTO
-------------------------------------------------
local autoText = Instance.new("TextLabel")
autoText.Size = UDim2.new(0.5,-5,1,0)
autoText.Position = UDim2.new(0,10,0,0)
autoText.BackgroundTransparency = 1
autoText.Text = "AUTO STAND"
autoText.TextColor3 = Color3.new(1,1,1)
autoText.Font = Enum.Font.Gotham
autoText.Parent = autoFrame

-------------------------------------------------
-- BOTÃO TOGGLE AUTO
-------------------------------------------------
local autoToggle = Instance.new("TextButton")
autoToggle.Size = UDim2.new(0.5,-10,0,30)
autoToggle.Position = UDim2.new(0.5,5,0.5,-15)
autoToggle.Text = "OFF"
autoToggle.BackgroundColor3 = Color3.fromRGB(60,60,60)
autoToggle.TextColor3 = Color3.new(1,1,1)
autoToggle.Font = Enum.Font.Gotham
autoToggle.Parent = autoFrame
Instance.new("UICorner", autoToggle)

-------------------------------------------------
-- INDICADOR
-------------------------------------------------
local status = Instance.new("Frame")
status.Size = UDim2.new(0,10,0,10)
status.Position = UDim2.new(1,-20,0.5,-5)
status.BackgroundColor3 = Color3.fromRGB(255,0,0)
status.BackgroundTransparency = 0.3
status.Parent = autoFrame
Instance.new("UICorner", status).CornerRadius = UDim.new(1,0)

-------------------------------------------------
-- FUNÇÃO AUTO STAND (PLACEHOLDER)
-------------------------------------------------
local function autoStand()
	if not autoStandAtivo then return end
	print("Auto Stand Ativo (placeholder)")
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
-- MENU TOGGLE
-------------------------------------------------
toggleButton.MouseButton1Click:Connect(function()
	menuAberto = not menuAberto
	menuFrame.Visible = menuAberto
end)

-------------------------------------------------
-- MINIMIZAR
-------------------------------------------------
minimizeButton.MouseButton1Click:Connect(function()
	menuFrame.Visible = false
	menuAberto = false
end)

-------------------------------------------------
-- FECHAR TUDO
-------------------------------------------------
closeButton.MouseButton1Click:Connect(function()
	screenGui:Destroy()
end)

-------------------------------------------------
-- TECLA F ABRIR MENU
-------------------------------------------------
UserInputService.InputBegan:Connect(function(input, gp)
	if not gp and input.KeyCode == Enum.KeyCode.F then
		menuAberto = not menuAberto
		menuFrame.Visible = menuAberto
	end
end)
