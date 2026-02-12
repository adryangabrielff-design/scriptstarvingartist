-- Script Auto Stand - VERSÃO ESTANTE & REIVINDICAR
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Variáveis
local menuAberto = false
local processando = false
local VirtualUser = game:GetService("VirtualUser")
local VirtualInput = game:GetService("VirtualInputManager")

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

-- FUNÇÃO PARA ENCONTRAR ESTANTES
function encontrarEstantes()
    local estantes = {}
    local workspace = game:GetService("Workspace")
    
    -- Varredura COMPLETA em TODO O MAPA
    for _, obj in pairs(workspace:GetDescendants()) do
        -- Procura por objetos com "ESTANTE" no nome
        if obj.Name:find("ESTANTE") or obj.Name:find("Estante") then
            -- Procura por botão de reivindicar dentro ou perto da estante
            local botaoReivindicar = nil
            
            -- Procura no próprio objeto
            for _, child in pairs(obj:GetDescendants()) do
                if child:IsA("TextButton") or child:IsA("ImageButton") then
                    if child.Text:find("Reivindicar") or child.Name:find("Reivindicar") then
                        botaoReivindicar = child
                        break
                    end
                end
            end
            
            -- Procura no pai do objeto
            if not botaoReivindicar and obj.Parent then
                for _, child in pairs(obj.Parent:GetDescendants()) do
                    if child:IsA("TextButton") or child:IsA("ImageButton") then
                        if child.Text:find("Reivindicar") or child.Name:find("Reivindicar") then
                            botaoReivindicar = child
                            break
                        end
                    end
                end
            end
            
            -- Se encontrou o botão, adiciona à lista
            if botaoReivindicar then
                table.insert(estantes, {
                    estante = obj,
                    botao = botaoReivindicar
                })
            end
        end
    end
    
    return estantes
end

-- FUNÇÃO DE TELEPORTE ABSOLUTO
function teleportarParaEstante(estante)
    local character = player.Character
    if not character then
        character = player.CharacterAdded:Wait()
        wait(0.5)
    end
    
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then
        repeat wait() root = character:FindFirstChild("HumanoidRootPart") until root
    end
    
    -- Posiciona na frente da estante
    local posicaoEstante = estante.Position
    local direcaoFrente = estante.CFrame.LookVector
    
    -- TELEPORTE MASSIVO - 20x seguidas
    for i = 1, 20 do
        -- Teleporta para perto da estante
        root.CFrame = CFrame.new(posicaoEstante + direcaoFrente * 3 + Vector3.new(0, 2, 0))
        root.Velocity = Vector3.new(0, 0, 0)
        root.RotVelocity = Vector3.new(0, 0, 0)
        wait()
    end
end

-- FUNÇÃO PARA CLICAR NO BOTÃO REIVINDICAR
function clicarBotaoReivindicar(botao)
    -- MÉTODO 1: Clique direto no botão GUI
    pcall(function()
        if botao:IsA("TextButton") or botao:IsA("ImageButton") then
            -- Simular clique no botão
            botao.MouseButton1Click:Fire()
            botao.MouseButton1Down:Fire()
            botao.MouseButton1Up:Fire()
            
            -- Ativar o botão
            botao.Activated:Fire()
        end
    end)
    
    wait(0.1)
    
    -- MÉTODO 2: Clicar na posição do botão na tela
    pcall(function()
        local absPos = botao.AbsolutePosition
        local absSize = botao.AbsoluteSize
        local centerX = absPos.X + (absSize.X / 2)
        local centerY = absPos.Y + (absSize.Y / 2)
        
        -- Mover mouse para o botão
        VirtualInput:SendMouseMoveEvent(centerX, centerY)
        wait(0.1)
        
        -- Clicar 10x seguidas
        for i = 1, 10 do
            VirtualInput:SendMouseButtonEvent(centerX, centerY, 0, true, nil, 0)
            wait(0.02)
            VirtualInput:SendMouseButtonEvent(centerX, centerY, 0, false, nil, 0)
            wait(0.02)
        end
    end)
    
    wait(0.1)
    
    -- MÉTODO 3: Clique via VirtualUser
    pcall(function()
        VirtualUser:ClickButton1(Vector2.new(botao.AbsolutePosition.X + 10, botao.AbsolutePosition.Y + 10))
    end)
    
    wait(0.1)
    
    -- MÉTODO 4: Fire os eventos novamente
    for i = 1, 5 do
        pcall(function()
            if botao:IsA("TextButton") then
                fireclickdetector(botao)
            else
                botao.MouseButton1Click:Fire()
                botao.Activated:Fire()
            end
        end)
        wait(0.05)
    end
end

-- FUNÇÃO PRINCIPAL
function autoStand()
    if processando then return end
    processando = true
    
    autoStandButton.Text = "PROCURANDO..."
    autoStandButton.BackgroundColor3 = Color3.new(1, 0.5, 0)
    
    wait(0.3)
    
    local estantes = encontrarEstantes()
    
    if #estantes > 0 then
        -- ESCOLHER UMA ESTANTE ALEATÓRIA
        local index = math.random(1, #estantes)
        local estanteEscolhida = estantes[index].estante
        local botaoEscolhido = estantes[index].botao
        
        autoStandButton.Text = "TELEPORTANDO..."
        wait(0.2)
        
        -- TELEPORTAR PARA ESTANTE
        teleportarParaEstante(estanteEscolhida)
        
        wait(0.5)
        
        autoStandButton.Text = "REIVINDICANDO..."
        autoStandButton.BackgroundColor3 = Color3.new(1, 1, 0)
        
        -- CLICAR NO BOTÃO REIVINDICAR
        clicarBotaoReivindicar(botaoEscolhido)
        
        wait(0.3)
        
        -- MAIS CLIQUES PARA GARANTIR
        for i = 1, 15 do
            pcall(function()
                if botaoEscolhido:IsA("TextButton") then
                    botaoEscolhido.MouseButton1Click:Fire()
                else
                    fireclickdetector(botaoEscolhido)
                end
            end)
            wait(0.02)
        end
        
        autoStandButton.Text = "CONCLUÍDO!"
        autoStandButton.BackgroundColor3 = Color3.new(0, 1, 0)
        
        wait(2)
    else
        autoStandButton.Text = "SEM ESTANTES"
        autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
        wait(2)
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

-- Tecla E para abrir/fechar menu
mouse.KeyDown:Connect(function(key)
    if key:lower() == "e" then
        menuAberto = not menuAberto
        menuFrame.Visible = menuAberto
        toggleButton.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0, 0, 0)
        toggleButton.Text = menuAberto and "FECHAR" or "MENU"
    end
end)
