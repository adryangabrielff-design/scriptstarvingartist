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

--// SERVICES
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer

-------------------------------------------------
-- IDIOMA
-------------------------------------------------
local LANG = "PT" -- padrão

-------------------------------------------------
-- BLUR
-------------------------------------------------
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

-------------------------------------------------
-- GUI BASE
-------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "PurpleMenuSystem"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- TEXTOS
-------------------------------------------------
local TEXT = {
	PT = {
		LANGUAGE = "Qual a sua linguagem?",
		CLOSE_CONFIRM = "Tem certeza que deseja eliminar o menu?"
	},
	EN = {
		LANGUAGE = "What is your language?",
		CLOSE_CONFIRM = "Are you sure you want to delete the menu?"
	}
}

-------------------------------------------------
-- SELECT LANGUAGE GUI
-------------------------------------------------
local langFrame = Instance.new("Frame")
langFrame.Size = UDim2.new(0,500,0,300)
langFrame.Position = UDim2.new(0.5,-250,0.5,-150)
langFrame.BackgroundColor3 = Color3.fromRGB(0,0,0)
langFrame.Parent = gui

Instance.new("UICorner", langFrame)

local strokeLang = Instance.new("UIStroke")
strokeLang.Color = Color3.fromRGB(170,0,255)
strokeLang.Thickness = 3
strokeLang.Parent = langFrame

local langText = Instance.new("TextLabel")
langText.Size = UDim2.new(1,0,0,60)
langText.BackgroundTransparency = 1
langText.TextScaled = true
langText.TextColor3 = Color3.new(1,1,1)
langText.Text = "Choose Language"
langText.Parent = langFrame

-------------------------------------------------
-- BOTÃO EUA
-------------------------------------------------
local usaButton = Instance.new("TextButton")
usaButton.Size = UDim2.new(0,200,0,120)
usaButton.Position = UDim2.new(0.1,0,0.4,0)
usaButton.Text = "🇺🇸\nWhat is your language"
usaButton.TextScaled = true
usaButton.BackgroundColor3 = Color3.fromRGB(30,30,30)
usaButton.Parent = langFrame
Instance.new("UICorner", usaButton)

-------------------------------------------------
-- BOTÃO BRASIL
-------------------------------------------------
local brButton = Instance.new("TextButton")
brButton.Size = UDim2.new(0,200,0,120)
brButton.Position = UDim2.new(0.55,0,0.4,0)
brButton.Text = "🇧🇷\nQual a sua linguagem"
brButton.TextScaled = true
brButton.BackgroundColor3 = Color3.fromRGB(30,30,30)
brButton.Parent = langFrame
Instance.new("UICorner", brButton)

-------------------------------------------------
-- MENU BOTÃO ⚠️
-------------------------------------------------
local openButton = Instance.new("TextButton")
openButton.Size = UDim2.new(0,60,0,60)
openButton.Position = UDim2.new(0,10,0,20)
openButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
openButton.Text = "⚠️"
openButton.TextSize = 28
openButton.Visible = false
openButton.Parent = gui

local strokeBtn = Instance.new("UIStroke")
strokeBtn.Color = Color3.fromRGB(170,0,255)
strokeBtn.Parent = openButton

Instance.new("UICorner", openButton)

-------------------------------------------------
-- MENU PRINCIPAL
-------------------------------------------------
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0.8,0,0.8,0)
menu.Position = UDim2.new(0.1,0,0.1,0)
menu.BackgroundColor3 = Color3.fromRGB(0,0,0)
menu.BackgroundTransparency = 0.35
menu.Visible = false
menu.Parent = gui

local strokeMenu = Instance.new("UIStroke")
strokeMenu.Color = Color3.fromRGB(170,0,255)
strokeMenu.Thickness = 4
strokeMenu.Parent = menu

Instance.new("UICorner", menu)

-------------------------------------------------
-- BOTÃO -
-------------------------------------------------
local minimize = Instance.new("TextButton")
minimize.Size = UDim2.new(0,60,0,45)
minimize.Position = UDim2.new(1,-140,0,20)
minimize.Text = "-"
minimize.TextScaled = true
minimize.BackgroundColor3 = Color3.fromRGB(40,40,40)
minimize.Parent = menu
Instance.new("UICorner", minimize)

-------------------------------------------------
-- BOTÃO X
-------------------------------------------------
local close = Instance.new("TextButton")
close.Size = UDim2.new(0,60,0,45)
close.Position = UDim2.new(1,-70,0,20)
close.Text = "X"
close.TextScaled = true
close.BackgroundColor3 = Color3.fromRGB(120,0,0)
close.Parent = menu
Instance.new("UICorner", close)

-------------------------------------------------
-- CONFIRM GUI
-------------------------------------------------
local confirmGui = Instance.new("Frame")
confirmGui.Size = UDim2.new(0,400,0,200)
confirmGui.Position = UDim2.new(0.5,-200,0.5,-100)
confirmGui.BackgroundColor3 = Color3.fromRGB(0,0,0)
confirmGui.BackgroundTransparency = 0.2
confirmGui.Visible = false
confirmGui.Parent = gui

Instance.new("UICorner", confirmGui)

local confirmText = Instance.new("TextLabel")
confirmText.Size = UDim2.new(1,-20,0,80)
confirmText.Position = UDim2.new(0,10,0,20)
confirmText.BackgroundTransparency = 1
confirmText.TextScaled = true
confirmText.TextColor3 = Color3.new(1,1,1)
confirmText.Parent = confirmGui

-------------------------------------------------
-- YES / NO
-------------------------------------------------
local yesButton = Instance.new("TextButton")
yesButton.Size = UDim2.new(0,120,0,50)
yesButton.Position = UDim2.new(0.2,0,1,-70)
yesButton.Text = "✅"
yesButton.TextScaled = true
yesButton.BackgroundColor3 = Color3.fromRGB(0,120,0)
yesButton.Parent = confirmGui
Instance.new("UICorner", yesButton)

local noButton = Instance.new("TextButton")
noButton.Size = UDim2.new(0,120,0,50)
noButton.Position = UDim2.new(0.6,0,1,-70)
noButton.Text = "❌"
noButton.TextScaled = true
noButton.BackgroundColor3 = Color3.fromRGB(120,0,0)
noButton.Parent = confirmGui
Instance.new("UICorner", noButton)

-------------------------------------------------
-- FUNÇÃO APLICAR IDIOMA
-------------------------------------------------
local function applyLanguage()
	confirmText.Text = TEXT[LANG].CLOSE_CONFIRM
end

-------------------------------------------------
-- ESCOLHA IDIOMA
-------------------------------------------------
usaButton.MouseButton1Click:Connect(function()
	LANG = "EN"
	langFrame:Destroy()
	openButton.Visible = true
	applyLanguage()
end)

brButton.MouseButton1Click:Connect(function()
	LANG = "PT"
	langFrame:Destroy()
	openButton.Visible = true
	applyLanguage()
end)

-------------------------------------------------
-- MENU FUNÇÕES
-------------------------------------------------
openButton.MouseButton1Click:Connect(function()
	menu.Visible = true
end)

minimize.MouseButton1Click:Connect(function()
	menu.Visible = false
end)

close.MouseButton1Click:Connect(function()
	confirmGui.Visible = true
	blur.Size = 24
end)

yesButton.MouseButton1Click:Connect(function()
	gui:Destroy()
	blur:Destroy()
end)

noButton.MouseButton1Click:Connect(function()
	confirmGui.Visible = false
	blur.Size = 0
end)
