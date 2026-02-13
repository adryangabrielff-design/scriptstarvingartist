--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local TEMPO_LOOP = 1 -- segundos

--// SERVICES
local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

-------------------------------------------------
-- HRP
-------------------------------------------------
local function getHRP()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:WaitForChild("HumanoidRootPart")
end

-------------------------------------------------
-- PEGAR PART DO MODEL
-------------------------------------------------
local function getPart(model)
    if model.PrimaryPart then
        return model.PrimaryPart
    end
    
    for _, v in pairs(model:GetDescendants()) do
        if v:IsA("BasePart") then
            return v
        end
    end
end

-------------------------------------------------
-- SPAM TECLA E
-------------------------------------------------
local function spamE(vezes)
    for i = 1, vezes do
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
        task.wait(0.02)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
        task.wait(0.02)
    end
end

-------------------------------------------------
-- SPAM CLIQUE MOUSE
-------------------------------------------------
local function spamMouse(vezes)
    for i = 1, vezes do
        VirtualInputManager:SendMouseButtonEvent(0,0,0,true,game,0)
        task.wait(0.02)
        VirtualInputManager:SendMouseButtonEvent(0,0,0,false,game,0)
        task.wait(0.02)
    end
end

-------------------------------------------------
-- CLICAR BOTÃO CLAIM GUI
-------------------------------------------------
local function clicarBotao()
    for _, gui in pairs(player.PlayerGui:GetDescendants()) do
        
        if gui:IsA("TextButton") or gui:IsA("ImageButton") then
            
            local nome = string.upper(gui.Name or "")
            local texto = string.upper(gui.Text or "")
            
            if string.find(nome,"CLAIM")
            or string.find(nome,"REIVIND")
            or string.find(texto,"CLAIM")
            or string.find(texto,"REIVIND") then
                
                pcall(function()
                    gui:Activate()
                end)
                
                pcall(function()
                    gui.MouseButton1Click:Fire()
                end)
            end
        end
    end
end

-------------------------------------------------
-- ATIVAR CLICK DETECTOR
-------------------------------------------------
local function ativarClickDetector(model)
    for _, v in pairs(model:GetDescendants()) do
        if v:IsA("ClickDetector") then
            pcall(function()
                fireclickdetector(v)
            end)
        end
    end
end

-------------------------------------------------
-- ACHAR STAND MAIS PERTO
-------------------------------------------------
local function acharStand(hrp)

    local maisPerto = nil
    local distMin = math.huge

    for _, v in pairs(workspace:GetDescendants()) do
        
        if v:IsA("TextLabel") or v:IsA("TextButton") then
            
            local txt = string.upper(v.Text or "")
            
            if string.find(txt, TEXTO_ALVO) then
                
                local model = v:FindFirstAncestorOfClass("Model")
                
                if model then
                    local part = getPart(model)
                    
                    if part then
                        local dist = (hrp.Position - part.Position).Magnitude
                        
                        if dist < distMin then
                            distMin = dist
                            maisPerto = model
                        end
                    end
                end
            end
        end
    end

    return maisPerto
end

-------------------------------------------------
-- TELEPORTE DEMÔNIO
-------------------------------------------------
local function teleportarDemonio(part, hrp)
    for i = 1, 25 do
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        hrp.AssemblyLinearVelocity = Vector3.zero
        task.wait(0.01)
    end
end

-------------------------------------------------
-- LOOP COM TEMPO LIMITE
-------------------------------------------------
task.spawn(function()

    local inicio = tick()

    while tick() - inicio <= TEMPO_LOOP do
        
        local hrp = getHRP()
        local standModel = acharStand(hrp)

        if standModel then
            
            local part = getPart(standModel)
            
            if part then
                
                teleportarDemonio(part, hrp)
                ativarClickDetector(standModel)
                
                spamE(20)
                spamMouse(20)
                clicarBotao()
                
                spamE(10)
                spamMouse(10)
                
            end
        end

        task.wait(0.2)

    end

    print("🔥 Loop demônio finalizado 🔥")

end)

-- SERVICES
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- BLUR
-------------------------------------------------
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

-------------------------------------------------
-- LANGUAGE VARIABLES
-------------------------------------------------
local LANG = "PT"

local TEXT = {
	PT = {
		TITLE = "MENU",
		CLOSE = "X",
		MIN = "-",
		CONFIRM = "Tem certeza que quer eliminar o menu?",
		YES = "SIM",
		NO = "NÃO"
	},

	EN = {
		TITLE = "MENU",
		CLOSE = "X",
		MIN = "-",
		CONFIRM = "Are you sure you want to delete the menu?",
		YES = "YES",
		NO = "NO"
	}
}

-------------------------------------------------
-- LANGUAGE SELECT GUI
-------------------------------------------------
local langGui = Instance.new("ScreenGui")
langGui.Parent = PlayerGui
langGui.ResetOnSpawn = false

-- BACKGROUND
local bg = Instance.new("Frame")
bg.Size = UDim2.new(1,0,1,0)
bg.BackgroundColor3 = Color3.fromRGB(0,0,0)
bg.Parent = langGui

-- TITLE USA/BR
local title = Instance.new("TextLabel")
title.Size = UDim2.new(0,300,0,60)
title.Position = UDim2.new(0.5,-150,0,40)
title.BackgroundTransparency = 1
title.Text = "USA / BR"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = bg

-- USA BUTTON
local usaBtn = Instance.new("ImageButton")
usaBtn.Size = UDim2.new(0,200,0,120)
usaBtn.Position = UDim2.new(0.25,-100,0.5,-60)
usaBtn.Image = "rbxassetid://16047725674"
usaBtn.Parent = bg

local usaTxt = Instance.new("TextLabel")
usaTxt.Size = UDim2.new(1,0,0,30)
usaTxt.Position = UDim2.new(0,0,1,5)
usaTxt.BackgroundTransparency = 1
usaTxt.Text = "ENGLISH"
usaTxt.TextColor3 = Color3.fromRGB(255,255,255)
usaTxt.TextScaled = true
usaTxt.Parent = usaBtn

-- BR BUTTON
local brBtn = Instance.new("ImageButton")
brBtn.Size = UDim2.new(0,200,0,120)
brBtn.Position = UDim2.new(0.75,-100,0.5,-60)
brBtn.Image = "rbxassetid://16047725919"
brBtn.Parent = bg

local brTxt = Instance.new("TextLabel")
brTxt.Size = UDim2.new(1,0,0,30)
brTxt.Position = UDim2.new(0,0,1,5)
brTxt.BackgroundTransparency = 1
brTxt.Text = "PORTUGUÊS"
brTxt.TextColor3 = Color3.fromRGB(255,255,255)
brTxt.TextScaled = true
brTxt.Parent = brBtn

-------------------------------------------------
-- MAIN GUI CREATOR
-------------------------------------------------
local function createMainGUI()

	local gui = Instance.new("ScreenGui")
	gui.Parent = PlayerGui
	gui.ResetOnSpawn = false

	-- OPEN BUTTON ⚠️
	local openButton = Instance.new("TextButton")
	openButton.Size = UDim2.new(0,60,0,60)
	openButton.Position = UDim2.new(0,10,0,20)
	openButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
	openButton.Text = "⚠️"
	openButton.TextScaled = true
	openButton.TextColor3 = Color3.fromRGB(255,255,255)
	openButton.Parent = gui

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(170,0,255)
	stroke.Thickness = 2
	stroke.Parent = openButton

	-- MAIN MENU
	local mainFrame = Instance.new("Frame")
	mainFrame.Size = UDim2.new(0,800,0,500)
	mainFrame.Position = UDim2.new(0.5,-400,0.5,-250)
	mainFrame.BackgroundColor3 = Color3.fromRGB(0,0,0)
	mainFrame.BackgroundTransparency = 0.3
	mainFrame.Visible = false
	mainFrame.Parent = gui

	local stroke2 = Instance.new("UIStroke")
	stroke2.Color = Color3.fromRGB(170,0,255)
	stroke2.Thickness = 2
	stroke2.Parent = mainFrame

	-- TITLE
	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1,0,0,50)
	title.BackgroundTransparency = 1
	title.Text = TEXT[LANG].TITLE
	title.TextColor3 = Color3.fromRGB(255,255,255)
	title.TextScaled = true
	title.Parent = mainFrame

	-- CLOSE X
	local close = Instance.new("TextButton")
	close.Size = UDim2.new(0,50,0,50)
	close.Position = UDim2.new(1,-60,0,5)
	close.Text = TEXT[LANG].CLOSE
	close.Parent = mainFrame

	-- MINIMIZE
	local min = Instance.new("TextButton")
	min.Size = UDim2.new(0,50,0,50)
	min.Position = UDim2.new(1,-120,0,5)
	min.Text = TEXT[LANG].MIN
	min.Parent = mainFrame

	-- CONFIRM GUI
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
	-- BUTTON EVENTS
	-------------------------------------------------

	openButton.MouseButton1Click:Connect(function()
		mainFrame.Visible = true
	end)

	min.MouseButton1Click:Connect(function()
		mainFrame.Visible = false
	end)

	close.MouseButton1Click:Connect(function()
		confirm.Visible = true
		blur.Size = 20
	end)

	no.MouseButton1Click:Connect(function()
		confirm.Visible = false
		blur.Size = 0
	end)

	yes.MouseButton1Click:Connect(function()
		gui:Destroy()
		blur.Size = 0
	end)

end

-------------------------------------------------
-- LANGUAGE BUTTON EVENTS
-------------------------------------------------

usaBtn.MouseButton1Click:Connect(function()
	LANG = "EN"
	langGui:Destroy()
	createMainGUI()
end)

brBtn.MouseButton1Click:Connect(function()
	LANG = "PT"
	langGui:Destroy()
	createMainGUI()
end)

-------------------------------------------------
-- NAV BAR TOP
-------------------------------------------------

local navBar = Instance.new("Frame")
navBar.Size = UDim2.new(1,0,0,50)
navBar.Position = UDim2.new(0,0,0,50)
navBar.BackgroundTransparency = 1
navBar.Parent = mainFrame

-------------------------------------------------
-- PAGE CONTAINER
-------------------------------------------------

local pageContainer = Instance.new("Frame")
pageContainer.Size = UDim2.new(1,0,1,-100)
pageContainer.Position = UDim2.new(0,0,0,100)
pageContainer.BackgroundTransparency = 1
pageContainer.Parent = mainFrame

-------------------------------------------------
-- CREATE PAGES
-------------------------------------------------

local autoPage = Instance.new("Frame")
autoPage.Size = UDim2.new(1,0,1,0)
autoPage.BackgroundTransparency = 1
autoPage.Visible = true
autoPage.Parent = pageContainer

local playerPage = Instance.new("Frame")
playerPage.Size = UDim2.new(1,0,1,0)
playerPage.BackgroundTransparency = 1
playerPage.Visible = false
playerPage.Parent = pageContainer

local settingsPage = Instance.new("Frame")
settingsPage.Size = UDim2.new(1,0,1,0)
settingsPage.BackgroundTransparency = 1
settingsPage.Visible = false
settingsPage.Parent = pageContainer

-------------------------------------------------
-- PAGE SWITCH FUNCTION
-------------------------------------------------

local function switchPage(page)
	autoPage.Visible = false
	playerPage.Visible = false
	settingsPage.Visible = false
	
	page.Visible = true
end

-------------------------------------------------
-- NAV BUTTON CREATOR
-------------------------------------------------

local function createNavButton(text, posX, page)

	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0,150,0,40)
	btn.Position = UDim2.new(0,posX,0,5)
	btn.BackgroundColor3 = Color3.fromRGB(0,0,0)
	btn.TextColor3 = Color3.fromRGB(255,255,255)
	btn.TextScaled = true
	btn.Text = text
	btn.Parent = navBar

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(170,0,255)
	stroke.Thickness = 2
	stroke.Parent = btn

	btn.MouseButton1Click:Connect(function()
		switchPage(page)
	end)

end

-------------------------------------------------
-- CREATE NAV BUTTONS
-------------------------------------------------

createNavButton("AUTO", 20, autoPage)
createNavButton("PLAYER", 190, playerPage)
createNavButton("SETTINGS", 360, settingsPage)
