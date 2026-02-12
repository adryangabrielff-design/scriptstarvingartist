-- Script principal para GUI de Auto Stand
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Variáveis de controle
local menuAberto = false
local processando = false

-- Criar botão principal para abrir/fechar menu
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "MENU"
toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextScaled = true
toggleButton.Parent = screenGui

-- Criar frame do menu (inicialmente invisível)
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 200, 0, 100)
menuFrame.Position = UDim2.new(0, 70, 0, 10)
menuFrame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
menuFrame.BorderSizePixel = 2
menuFrame.BorderColor3 = Color3.new(0, 0, 0)
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- Título do menu
local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, 0, 0, 30)
menuTitle.Text = "AUTO STAND"
menuTitle.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
menuTitle.TextColor3 = Color3.new(1, 1, 1)
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.TextScaled = true
menuTitle.Parent = menuFrame

-- Botão Auto Stand (ação única)
local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0, 180, 0, 50)
autoStandButton.Position = UDim2.new(0, 10, 0, 40)
autoStandButton.Text = "AUTO STAND"
autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.Font = Enum.Font.SourceSansBold
autoStandButton.TextScaled = true
autoStandButton.Parent = menuFrame

-- Função para teleporte forçado
local function teleportarParaStand(stand)
    local success, error = pcall(function()
        local character = player.Character
        if not character or not character:FindFirstChild("HumanoidRootPart") then
            -- Aguardar character carregar
            repeat
                wait(0.1)
                character = player.Character
            until character and character:FindFirstChild("HumanoidRootPart")
        end
        
        -- Teleporte forçado múltiplas vezes para garantir
        for i = 1, 3 do
            character.HumanoidRootPart.CFrame = stand.CFrame + Vector3.new(0, 3, 0)
            wait(0.1)
        end
        
        return true
    end)
    
    return success
end

-- Função para encontrar stands disponíveis
local function encontrarStandDisponivel()
    print("Procurando stands...")
    
    local workspaceObjects = workspace:GetDescendants()
    
    for _, objeto in pairs(workspaceObjects) do
        -- Procurar por partes chamadas "Stand"
        if objeto:IsA("BasePart") and objeto.Name == "Stand" then
            
            -- Verificar se tem ClickDetector
            local clickDetector = objeto:FindFirstChildOfClass("ClickDetector")
            if not clickDetector then
                continue
            end
            
            -- Verificar se stand está ocupado
            local ocupado = false
            local parent = objeto.Parent
            
            if parent then
                local jarra = parent:FindFirstChild("JARRA DE PONTA")
                local artista = parent:FindFirstChild("Artista da Dica")
                ocupado = jarra ~= nil or artista ~= nil
            end
            
            -- Se não estiver ocupado, retorna o stand
            if not ocupado then
                print("Stand disponível encontrado: " .. objeto:GetFullName())
                return objeto, clickDetector
            end
        end
    end
    
    print("Nenhum stand disponível encontrado")
    return nil, nil
end

-- Função principal do Auto Stand
local function executarAutoStand()
    -- Impedir execução múltipla
    if processando then
        print("Já está processando um stand...")
        return
    end
    
    processando = true
    autoStandButton.Text = "PROCURANDO..."
    autoStandButton.BackgroundColor3 = Color3.new(0.5, 0.5, 0)
    autoStandButton.Active = false
    
    -- Pequena pausa para feedback visual
    wait(0.3)
    
    -- Encontrar stand disponível
    local stand, clickDetector = encontrarStandDisponivel()
    
    if stand and clickDetector then
        -- Teleportar para o stand
        print("Teleportando para o stand...")
        local teleportou = teleportarParaStand(stand)
        
        if teleportou then
            wait(0.3)
            
            -- Clicar no stand
            print("Clicando no stand...")
            pcall(function()
                fireclickdetector(clickDetector)
            end)
            
            wait(0.2)
            
            -- Clicar novamente para garantir
            pcall(function()
                fireclickdetector(clickDetector)
            end)
            
            print("Stand reivindicado com sucesso!")
            autoStandButton.Text = "AUTO STAND"
            autoStandButton.BackgroundColor3 = Color3.new(0.2, 0.8, 0.2) -- Verde por 1 segundo
            wait(1)
            autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
        else
            print("Falha no teleporte!")
            autoStandButton.Text = "AUTO STAND"
            autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
        end
    else
        print("Nenhum stand disponível no momento!")
        autoStandButton.Text = "AUTO STAND"
        autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
    end
    
    autoStandButton.Active = true
    processando = false
end

-- Toggle do menu
toggleButton.MouseButton1Click:Connect(function()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    toggleButton.BackgroundColor3 = menuAberto and Color3.new(0.1, 0.5, 0.1) or Color3.new(0.2, 0.2, 0.2)
end)

-- Clique no botão Auto Stand
autoStandButton.MouseButton1Click:Connect(function()
    executarAutoStand()
end)

-- Tecla de atalho (E para abrir/fechar menu)
mouse.KeyDown:Connect(function(key)
    key = key:lower()
    if key == "e" then
        menuAberto = not menuAberto
        menuFrame.Visible = menuAberto
        toggleButton.BackgroundColor3 = menuAberto and Color3.new(0.1, 0.5, 0.1) or Color3.new(0.2, 0.2, 0.2)
    end
end)
