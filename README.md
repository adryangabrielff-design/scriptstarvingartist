-- Script Auto Stand - VERSÃO SUPREMA
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Variáveis
local menuAberto = false
local processando = false

-- BOTÃO MENU
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 60, 0, 60)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "MENU"
toggleButton.BackgroundColor3 = Color3.new(0, 0, 0)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextScaled = true
toggleButton.Parent = screenGui

-- FRAME DO MENU
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 220, 0, 100)
menuFrame.Position = UDim2.new(0, 80, 0, 10)
menuFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
menuFrame.BorderSizePixel = 2
menuFrame.BorderColor3 = Color3.new(1, 0, 0)
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- BOTÃO AUTO STAND
local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0, 200, 0, 60)
autoStandButton.Position = UDim2.new(0, 10, 0, 20)
autoStandButton.Text = "AUTO STAND"
autoStandButton.BackgroundColor3 = Color3.new(1, 0, 0)
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.Font = Enum.Font.SourceSansBold
autoStandButton.TextScaled = true
autoStandButton.Parent = menuFrame

-- FUNÇÃO DE TELEPORTE ABSOLUTO
function teleportarAbsoluto(stand)
    local character = player.Character
    if not character then
        character = player.CharacterAdded:Wait()
        wait(0.5)
    end
    
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then
        repeat wait() root = character:FindFirstChild("HumanoidRootPart") until root
    end
    
    -- TELEPORTE RADICAL - 10x seguidas
    for i = 1, 10 do
        root.CFrame = stand.CFrame + Vector3.new(0, 5, 0)
        root.Velocity = Vector3.new(0, 0, 0)
        root.RotVelocity = Vector3.new(0, 0, 0)
        wait()
    end
end

-- FUNÇÃO DE CLIQUE ABSOLUTO
function clicarAbsoluto(stand, detector)
    -- MÉTODO 1: FireClickDetector MÚLTIPLO
    for i = 1, 15 do
        pcall(function()
            fireclickdetector(detector)
        end)
        wait(0.01)
    end
    
    -- MÉTODO 2: MouseClick
    pcall(function()
        detector.MouseClick:Fire()
    end)
    
    -- MÉTODO 3: Clique na tela
    pcall(function()
        local standPos = stand.Position
        local cam = workspace.CurrentCamera
        local screenPos = cam:WorldToViewportPoint(standPos)
        
        -- Simular clique do mouse REAL
        mouse1press()
        wait(0.05)
        mouse1release()
        
        -- Clicar na posição do stand
        if screenPos.Z > 0 then
            local virtual = game:GetService("VirtualInputManager")
            virtual:SendMouseButtonEvent(screenPos.X, screenPos.Y, 0, true, nil, 0)
            wait(0.05)
            virtual:SendMouseButtonEvent(screenPos.X, screenPos.Y, 0, false, nil, 0)
        end
    end)
    
    -- MÉTODO 4: MAIS CLICK DETECTOR
    for i = 1, 20 do
        pcall(function()
            fireclickdetector(detector)
        end)
        wait()
    end
end

-- FUNÇÃO PARA ENCONTRAR STAND
function encontrarStand()
    local workspace = game:GetService("Workspace")
    local stands = {}
    
    -- Varredura COMPLETA
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == "Stand" then
            local detector = obj:FindFirstChildOfClass("ClickDetector")
            if detector then
                -- Verificar se está ocupado
                local ocupado = false
                local parent = obj.Parent
                
                if parent then
                    for _, child in pairs(parent:GetDescendants()) do
                        if child:IsA("TextLabel") or child:IsA("BillboardGui") then
                            local texto = child.Text or ""
                            if texto:find("JARRA") or texto:find("Artista") then
                                ocupado = true
                                break
                            end
                        end
                    end
                end
                
                if not ocupado then
                    table.insert(stands, {obj, detector})
                end
            end
        end
    end
    
    return stands
end

-- FUNÇÃO PRINCIPAL
function autoStand()
    if processando then return end
    processando = true
    
    autoStandButton.Text = "PROCURANDO..."
    autoStandButton.BackgroundColor3 = Color3.new(1, 0.5, 0)
    
    wait(0.3)
    
    local stands = encontrarStand()
    
    if #stands > 0 then
        -- ESCOLHER ALEATÓRIO
        local index = math.random(1, #stands)
        local stand = stands[index][1]
        local detector = stands[index][2]
        
        autoStandButton.Text = "TELEPORTANDO..."
        wait(0.2)
        
        -- TELEPORTAR
        teleportarAbsoluto(stand)
        
        wait(0.3)
        
        autoStandButton.Text = "CLICANDO..."
        
        -- CLICAR
        clicarAbsoluto(stand, detector)
        
        wait(0.2)
        
        -- MAIS CLIQUES PARA GARANTIR
        for i = 1, 10 do
            pcall(function()
                fireclickdetector(detector)
            end)
            wait(0.02)
        end
        
        autoStandButton.Text = "FEITO!"
        autoStandButton.BackgroundColor3 = Color3.new(0, 1, 0)
        
        wait(1.5)
    else
        autoStandButton.Text = "SEM STANDS"
        autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
        wait(1.5)
    end
    
    autoStandButton.Text = "AUTO STAND"
    autoStandButton.BackgroundColor3 = Color3.new(1, 0, 0)
    processando = false
end

-- EVENTOS
toggleButton.MouseButton1Click:Connect(function()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    toggleButton.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0, 0, 0)
    toggleButton.Text = menuAberto and "FECHAR" or "MENU"
end)

autoStandButton.MouseButton1Click:Connect(autoStand)

-- Tecla E
mouse.KeyDown:Connect(function(key)
    if key:lower() == "e" then
        menuAberto = not menuAberto
        menuFrame.Visible = menuAberto
        toggleButton.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0, 0, 0)
        toggleButton.Text = menuAberto and "FECHAR" or "MENU"
    end
end)
