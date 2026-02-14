--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local TEMPO_LOOP = 1

-------------------------------------------------
-- SERVICES
-------------------------------------------------
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- TEXT CONFIG
-------------------------------------------------
local LANG = "PT"

local TEXT = {
	PT = {
		CLOSE = "X",
		MIN = "-",
		CONFIRM = "Deseja fechar o menu?",
		YES = "Sim",
		NO = "Não"
	}
}

-------------------------------------------------
-- GUI BASE
-------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "DemonMenu"
gui.Parent = PlayerGui

-------------------------------------------------
-- MAIN FRAME
-------------------------------------------------
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0,600,0,400)
mainFrame.Position = UDim2.new(0.5,-300,0.5,-200)
mainFrame.BackgroundColor3 = Color3.fromRGB(20,20,20)
mainFrame.Parent = gui

-------------------------------------------------
-- NAV BAR
-------------------------------------------------
local navBar = Instance.new("Frame")
navBar.Size = UDim2.new(1,0,0,60)
navBar.BackgroundTransparency = 1
navBar.Parent = mainFrame

local navContainer = Instance.new("Frame")
navContainer.Size = UDim2.new(0.6,0,1,0)
navContainer.Position = UDim2.new(0.2,0,0,0)
navContainer.BackgroundTransparency = 1
navContainer.Parent = navBar

local layout = Instance.new("UIListLayout")
layout.FillDirection = Enum.FillDirection.Horizontal
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.VerticalAlignment = Enum.VerticalAlignment.Center
layout.Padding = UDim.new(0,40)
layout.Parent = navContainer

-------------------------------------------------
-- NAV BUTTON FUNCTION
-------------------------------------------------
local buttons = {}
local selectedButton = nil

local function createNavButton(text)
	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(0,120,1,0)
	holder.BackgroundTransparency = 1
	holder.Parent = navContainer
	
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1,0,0.7,0)
	btn.Position = UDim2.new(0,0,0.15,0)
	btn.BackgroundTransparency = 1
	btn.Text = text
	btn.TextColor3 = Color3.fromRGB(170,170,170)
	btn.TextScaled = true
	btn.Parent = holder
	
	local underline = Instance.new("Frame")
	underline.Size = UDim2.new(1,0,0,3)
	underline.Position = UDim2.new(0,0,1,-3)
	underline.BackgroundColor3 = Color3.fromRGB(255,255,255)
	underline.Visible = false
	underline.Parent = holder
	
	btn.MouseButton1Click:Connect(function()
		
		if selectedButton then
			selectedButton.TextColor3 = Color3.fromRGB(170,170,170)
			selectedButton.Parent.Underline.Visible = false
		end
		
		btn.TextColor3 = Color3.fromRGB(255,255,255)
		underline.Visible = true
		
		selectedButton = btn
	end)
	
	holder.Name = "Holder"
	underline.Name = "Underline"
	
	table.insert(buttons, btn)
end

createNavButton("AUTO")
createNavButton("PLAYER")
createNavButton("SETTINGS")

-------------------------------------------------
-- CLOSE / MIN BUTTONS
-------------------------------------------------
local close = Instance.new("TextButton")
close.Size = UDim2.new(0,50,0,50)
close.Position = UDim2.new(1,-60,0,5)
close.Text = TEXT[LANG].CLOSE
close.BackgroundColor3 = Color3.fromRGB(40,0,0)
close.TextColor3 = Color3.fromRGB(255,255,255)
close.Parent = mainFrame

local min = Instance.new("TextButton")
min.Size = UDim2.new(0,50,0,50)
min.Position = UDim2.new(1,-120,0,5)
min.Text = TEXT[LANG].MIN
min.BackgroundColor3 = Color3.fromRGB(30,30,30)
min.TextColor3 = Color3.fromRGB(255,255,255)
min.Parent = mainFrame

-------------------------------------------------
-- CONFIRM GUI
-------------------------------------------------
local confirm = Instance.new("Frame")
confirm.Size = UDim2.new(0,400,0,200)
confirm.Position = UDim2.new(0.5,-200,0.5,-100)
confirm.BackgroundColor3 = Color3.fromRGB(0,0,0)
confirm.Visible = false
confirm.Parent = gui

local txt = Instance.new("TextLabel")
txt.Size = UDim2.new(1,0,0.5,0)
txt.BackgroundTransparency = 1
txt.Text = TEXT[LANG].CONFIRM
txt.TextScaled = true
txt.TextColor3 = Color3.fromRGB(255,255,255)
txt.Parent = confirm

local yes = Instance.new("TextButton")
yes.Size = UDim2.new(0.4,0,0.3,0)
yes.Position = UDim2.new(0.1,0,0.6,0)
yes.Text = "✅ "..TEXT[LANG].YES
yes.Parent = confirm

local no = Instance.new("TextButton")
no.Size = UDim2.new(0.4,0,0.3,0)
no.Position = UDim2.new(0.5,0,0.6,0)
no.Text = "❌ "..TEXT[LANG].NO
no.Parent = confirm

-------------------------------------------------
-- BUTTON ACTIONS
-------------------------------------------------
close.MouseButton1Click:Connect(function()
	confirm.Visible = true
end)

yes.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

no.MouseButton1Click:Connect(function()
	confirm.Visible = false
end)

-------------------------------------------------
-- MINIMIZE
-------------------------------------------------
local minimized = false

min.MouseButton1Click:Connect(function()
	minimized = not minimized
	
	if minimized then
		for _,v in pairs(mainFrame:GetChildren()) do
			if v ~= navBar and v ~= close and v ~= min then
				v.Visible = false
			end
		end
		
		mainFrame.Size = UDim2.new(0,600,0,60)
	else
		for _,v in pairs(mainFrame:GetChildren()) do
			if v:IsA("GuiObject") then
				v.Visible = true
			end
		end
		
		mainFrame.Size = UDim2.new(0,600,0,400)
	end
end)

-------------------------------------------------
-- LANGUAGE EVENTS
-------------------------------------------------
usa.MouseButton1Click:Connect(function()
	LANG="EN"
	langGui:Destroy()
	createMainGUI()
end)

br.MouseButton1Click:Connect(function()
	LANG="PT"
	langGui:Destroy()
	createMainGUI()
end)
