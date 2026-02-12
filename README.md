-- Script para GUI de Auto Stand
-- Coloque este script em um LocalScript dentro de StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- Variáveis do menu
local menuAberto = false
local autoStandAtivo = false

-- Criar a ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandMenu"
screenGui.Parent = player.PlayerGui

-- Criar botão de toggle do menu
local toggleButton = Instance.new("ImageButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = screenGui

-- Ícone do botão (simples)
local toggleIcon = Instance.new("TextLabel")
toggleIcon.Name = "Icon"
toggleIcon.Size = UDim2.new(1, 0, 1, 0)
toggleIcon.BackgroundTransparency = 1
toggleIcon.Text = "☰"
toggleIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleIcon.TextSize = 30
toggleIcon.Font = Enum.Font.GothamBold
toggleIcon.Parent = toggleButton

-- Criar menu principal
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 250, 0, 150)
menuFrame.Position = UDim2.new(0, 90, 0, 20)
menuFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
menuFrame.BorderSizePixel = 0
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- Arredondar cantos do menu
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = menuFrame

-- Título do menu
local menuTitle = Instance.new("TextLabel")
menuTitle.Name = "Title"
menuTitle.Size = UDim2.new(1, 0, 0, 35)
menuTitle.Position = UDim2.new(0, 0, 0, 0)
menuTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
menuTitle.BorderSizePixel = 0
menuTitle.Text = "AUTO STAND MENU"
menuTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
menuTitle.TextSize = 16
menuTitle.Font = Enum.Font.GothamBold
menuTitle.Parent = menuFrame

-- Container para os botões
local container = Instance.new("Frame")
container.Name = "Container"
container.Size = UDim2.new(1, 0, 1, -35)
container.Position = UDim2.new(0, 0, 0, 35)
container.BackgroundTransparency = 1
container.Parent = menuFrame

-- Frame da opção Auto Stand
local autoStandFrame = Instance.new("Frame")
autoStandFrame.Name = "AutoStandFrame"
autoStandFrame.Size = UDim2.new(1, -20, 0, 40)
autoStandFrame.Position = UDim2.new(0, 10, 0, 10)
autoStandFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
autoStandFrame.BorderSizePixel = 0
autoStandFrame.Parent = container

-- Arredondar cantos do frame
local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 6)
frameCorner.Parent = autoStandFrame

-- Texto "AUTO STAND"
local autoStandText = Instance.new("TextLabel")
autoStandText.Name = "Text"
autoStandText.Size = UDim2.new(0.5, -5, 1, 0)
autoStandText.Position = UDim2.new(0, 10, 0, 0)
autoStandText.BackgroundTransparency = 1
autoStandText.Text = "AUTO STAND"
autoStandText.TextColor3 = Color3.fromRGB(255, 255, 255)
autoStandText.TextSize = 14
autoStandText.TextXAlignment = Enum.TextXAlignment.Left
autoStandText.Font = Enum.Font.Gotham
autoStandText.Parent = autoStandFrame

-- Botão de toggle ON/OFF
local toggleAutoStand = Instance.new("TextButton")
toggleAutoStand.Name = "ToggleButton"
toggleAutoStand.Size = UDim2.new(0.5, -10, 0, 30)
toggleAutoStand.Position = UDim2.new(0.5, 5, 0.5, -15)
toggleAutoStand.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
toggleAutoStand.BorderSizePixel = 0
toggleAutoStand.Text = "OFF"
toggleAutoStand.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleAutoStand.TextSize = 14
toggleAutoStand.Font = Enum.Font.Gotham
toggleAutoStand.Parent = autoStandFrame

-- Arredondar botão
local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 4)
buttonCorner.Parent = toggleAutoStand

-- Status do botão
local statusIndicator = Instance.new("Frame")
statusIndicator.Name = "StatusIndicator"
statusIndicator.Size = UDim2.new(0, 10, 0, 10)
statusIndicator.Position = UDim2.new(1, -20, 0.5, -5)
statusIndicator.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
statusIndicator.BackgroundTransparency = 0.3
statusIndicator.BorderSizePixel = 0
statusIndicator.Parent = autoStandFrame

-- Arredondar indicador
local indicatorCorner = Instance.new("UICorner")
indicatorCorner.CornerRadius = UDim.new(1, 0)
indicatorCorner.Parent = statusIndicator

-- FUNÇÃO PRINCIPAL: AUTO STAND
local function autoStand()
    if not autoStandAtivo then return end
    
    print("🔍 Procurando botões Stand...")
    
    -- Procurar botões "Stand" no mapa
    local standButtons = {}
    
    -- Procurar em Workspace
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("TextButton") or obj:IsA("ImageButton") or obj:IsA("ClickDetector") then
            if obj.Name:lower():find("stand") then
                table.insert(standButtons, obj)
                print("✅ Botão encontrado: " .. obj:GetFullName())
            end
        end
    end
    
    -- Se encontrou botões
    if #standButtons > 0 and autoStandAtivo then
        -- Escolher um botão aleatório
        local randomIndex = math.random(1, #standButtons)
        local selectedButton = standButtons[randomIndex]
        
        print("🎲 Botão selecionado: " .. selectedButton:GetFullName())
        
        -- Teleportar o jogador até o botão
        if selectedButton:IsA("BasePart") or selectedButton.Parent:IsA("BasePart") then
            local part = selectedButton:IsA("BasePart") and selectedButton or selectedButton.Parent
            local character = player.Character or player.CharacterAdded:Wait()
            local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
            
            if humanoidRootPart then
                humanoidRootPart.CFrame = part.CFrame * CFrame.new(0, 3, 0)
                wait(0.5)
            end
        end
        
        -- Acionar o botão
        if selectedButton:IsA("ClickDetector") then
            fireclickdetector(selectedButton)
            print("🖱️ ClickDetector acionado!")
        elseif selectedButton:IsA("TextButton") or selectedButton:IsA("ImageButton") then
            -- Simular clique
            selectedButton:Click()
            print("🖱️ Botão clicado!")
        end
        
        print("✨ Auto Stand executado com sucesso!")
        
        -- Desativar auto stand após executar
        autoStandAtivo = false
        toggleAutoStand.Text = "OFF"
        toggleAutoStand.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        statusIndicator.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        statusIndicator.BackgroundTransparency = 0.3
    else
        print("❌ Nenhum botão Stand encontrado ou processo interrompido.")
    end
end

-- Função para alternar Auto Stand
local function alternarAutoStand()
    autoStandAtivo = not autoStandAtivo
    
    if autoStandAtivo then
        toggleAutoStand.Text = "ON"
        toggleAutoStand.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
        statusIndicator.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        statusIndicator.BackgroundTransparency = 0
        
        -- Executar Auto Stand
        autoStand()
    else
        toggleAutoStand.Text = "OFF"
        toggleAutoStand.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        statusIndicator.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        statusIndicator.BackgroundTransparency = 0.3
        print("⏹️ Auto Stand desativado")
    end
end

-- Conectar o botão de toggle do Auto Stand
toggleAutoStand.MouseButton1Click:Connect(alternarAutoStand)

-- Função para alternar menu
local function alternarMenu()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    
    if menuAberto then
        toggleButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        toggleIcon.TextColor3 = Color3.fromRGB(0, 200, 255)
    else
        toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
        toggleIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
end

-- Conectar o botão de toggle do menu
toggleButton.MouseButton1Click:Connect(alternarMenu)

-- Tecla para abrir/fechar menu (tecla F)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.F then
        alternarMenu()
    end
end)

print("✅ Script Auto Stand carregado! Pressione F para abrir/fechar o menu.")
