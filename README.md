-- Script Auto Stand - Versão Simples
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Variáveis
local menuAberto = false
local autoStandLigado = false

-- BOTÃO PARA ABRIR/FECHAR MENU
local botaoMenu = Instance.new("TextButton")
botaoMenu.Size = UDim2.new(0, 50, 0, 50)
botaoMenu.Position = UDim2.new(0, 10, 0, 10)
botaoMenu.Text = "MENU"
botaoMenu.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
botaoMenu.TextColor3 = Color3.new(1, 1, 1)
botaoMenu.Font = Enum.Font.SourceSansBold
botaoMenu.TextScaled = true
botaoMenu.Parent = screenGui

-- MENU PRINCIPAL
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 250, 0, 100)
menu.Position = UDim2.new(0, 70, 0, 10)
menu.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
menu.BorderSizePixel = 2
menu.BorderColor3 = Color3.new(0, 0, 0)
menu.Visible = false
menu.Parent = screenGui

-- TÍTULO DO MENU
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 30)
titulo.Text = "AUTO STAND"
titulo.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
titulo.TextColor3 = Color3.new(1, 1, 1)
titulo.Font = Enum.Font.SourceSansBold
titulo.TextScaled = true
titulo.Parent = menu

-- BOTÃO AUTO STAND (TOGGLE)
local botaoAutoStand = Instance.new("TextButton")
botaoAutoStand.Size = UDim2.new(0, 150, 0, 40)
botaoAutoStand.Position = UDim2.new(0, 10, 0, 40)
botaoAutoStand.Text = "AUTO STAND"
botaoAutoStand.BackgroundColor3 = Color3.new(0.5, 0, 0)
botaoAutoStand.TextColor3 = Color3.new(1, 1, 1)
botaoAutoStand.Font = Enum.Font.SourceSansBold
botaoAutoStand.TextScaled = true
botaoAutoStand.Parent = menu

-- BOTÃO ON/OFF (LIGAR/DESLIGAR)
local botaoToggle = Instance.new("TextButton")
botaoToggle.Size = UDim2.new(0, 60, 0, 40)
botaoToggle.Position = UDim2.new(0, 170, 0, 40)
botaoToggle.Text = "OFF"
botaoToggle.BackgroundColor3 = Color3.new(0.5, 0, 0)
botaoToggle.TextColor3 = Color3.new(1, 1, 1)
botaoToggle.Font = Enum.Font.SourceSansBold
botaoToggle.TextScaled = true
botaoToggle.Parent = menu

-- FUNÇÃO PARA ENCONTRAR STANDS DISPONÍVEIS
function encontrarStandsDisponiveis()
    local stands = {}
    
    for _, objeto in pairs(workspace:GetDescendants()) do
        if objeto:IsA("BasePart") and objeto.Name == "Stand" then
            local clickDetector = objeto:FindFirstChildOfClass("ClickDetector")
            
            if clickDetector then
                -- Verificar se o stand está ocupado
                local ocupado = false
                local parent = objeto.Parent
                
                if parent then
                    for _, child in pairs(parent:GetDescendants()) do
                        if child:IsA("TextLabel") or child:IsA("BillboardGui") then
                            local texto = child.Text or ""
                            if texto:find("JARRA DE PONTA") or texto:find("Artista da Dica") then
                                ocupado = true
                                break
                            end
                        end
                    end
                end
                
                if not ocupado then
                    table.insert(stands, {parte = objeto, detector = clickDetector})
                end
            end
        end
    end
    
    return stands
end

-- FUNÇÃO PARA TELEPORTAR
function teleportar(stand)
    local character = player.Character
    if not character then return false end
    
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    
    root.CFrame = stand.CFrame + Vector3.new(0, 3, 0)
    return true
end

-- FUNÇÃO PARA CLICAR NO STAND
function clicarStand(detector)
    pcall(function()
        fireclickdetector(detector)
    end)
end

-- FUNÇÃO PRINCIPAL DO AUTO STAND
function executarAutoStand()
    if not autoStandLigado then return end
    
    -- Encontrar stands disponíveis
    local stands = encontrarStandsDisponiveis()
    
    if #stands > 0 then
        -- Escolher stand aleatório
        local escolhido = stands[math.random(1, #stands)]
        
        -- Teleportar
        teleportar(escolhido.parte)
        wait(0.3)
        
        -- Clicar no stand
        clicarStand(escolhido.detector)
        wait(0.1)
        clicarStand(escolhido.detector)
        
        -- Desligar Auto Stand depois de reivindicar
        autoStandLigado = false
        botaoToggle.Text = "OFF"
        botaoToggle.BackgroundColor3 = Color3.new(0.5, 0, 0)
        botaoAutoStand.BackgroundColor3 = Color3.new(0.5, 0, 0)
    end
end

-- FUNÇÃO PARA LIGAR/DESLIGAR
function toggleAutoStand()
    autoStandLigado = not autoStandLigado
    
    if autoStandLigado then
        botaoToggle.Text = "ON"
        botaoToggle.BackgroundColor3 = Color3.new(0, 0.5, 0)
        botaoAutoStand.BackgroundColor3 = Color3.new(0, 0.5, 0)
        
        -- Executar Auto Stand
        coroutine.wrap(executarAutoStand)()
    else
        botaoToggle.Text = "OFF"
        botaoToggle.BackgroundColor3 = Color3.new(0.5, 0, 0)
        botaoAutoStand.BackgroundColor3 = Color3.new(0.5, 0, 0)
    end
end

-- EVENTOS
botaoMenu.MouseButton1Click:Connect(function()
    menuAberto = not menuAberto
    menu.Visible = menuAberto
    botaoMenu.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0.2, 0.2, 0.2)
    botaoMenu.Text = menuAberto and "FECHAR" or "MENU"
end)

botaoAutoStand.MouseButton1Click:Connect(toggleAutoStand)
botaoToggle.MouseButton1Click:Connect(toggleAutoStand)

-- Tecla E para abrir/fechar menu
mouse.KeyDown:Connect(function(key)
    if key:lower() == "e" then
        menuAberto = not menuAberto
        menu.Visible = menuAberto
        botaoMenu.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0.2, 0.2, 0.2)
        botaoMenu.Text = menuAberto and "FECHAR" or "MENU"
    end
end)
