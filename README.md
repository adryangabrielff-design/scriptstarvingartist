local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Criar GUI
local gui = Instance.new("ScreenGui")
gui.Name = "SimpleMenu"
gui.ResetOnSpawn = false
gui.Parent = playerGui

-- Botão abrir / fechar menu
local openCloseButton = Instance.new("TextButton")
openCloseButton.Size = UDim2.new(0,120,0,40)
openCloseButton.Position = UDim2.new(0,10,0,10)
openCloseButton.Text = "Abrir Menu"
openCloseButton.Parent = gui

-- Frame do menu
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0,220,0,160)
menuFrame.Position = UDim2.new(0,10,0,60)
menuFrame.BackgroundColor3 = Color3.fromRGB(35,35,35)
menuFrame.Visible = true
menuFrame.Parent = gui

-- Estado menu
local menuOpen = true

openCloseButton.MouseButton1Click:Connect(function()
	menuOpen = not menuOpen
	menuFrame.Visible = menuOpen
	
	if menuOpen then
		openCloseButton.Text = "Fechar Menu"
	else
		openCloseButton.Text = "Abrir Menu"
	end
end)

-------------------------------------------------
-- BOTÃO AUTO STAND (estrutura pronta)
-------------------------------------------------

local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0,200,0,45)
autoStandButton.Position = UDim2.new(0,10,0,10)
autoStandButton.Text = "Auto Stand: OFF"
autoStandButton.Parent = menuFrame

local autoStandEnabled = false

autoStandButton.MouseButton1Click:Connect(function()
	autoStandEnabled = not autoStandEnabled
	
	if autoStandEnabled then
		autoStandButton.Text = "Auto Stand: ON"
	else
		autoStandButton.Text = "Auto Stand: OFF"
	end
end)

-------------------------------------------------
-- LOOP BASE DA FUNÇÃO
-------------------------------------------------

task.spawn(function()
	while true do
		
		if autoStandEnabled then
			-- Coloque aqui sua lógica oficial do jogo
			-- Exemplo:
			-- chamar RemoteEvent
			-- chamar função do servidor
			-- usar sistema interno do seu place
			
			print("Auto Stand ativo")
		end
		
		task.wait(2)
	end
end)
