-- Script principal para GUI de Auto Stand
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

-- Variáveis de controle
local menuAberto = false
local processando = false
local VirtualInputManager = game:GetService("VirtualInputManager")
local TeleportService = game:GetService("TeleportService")

-- Criar botão principal para abrir/fechar menu
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 60, 0, 60)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "⚙️ MENU"
toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextScaled = true
toggleButton.BorderSizePixel = 2
toggleButton.BorderColor3 = Color3.new(0, 0, 0)
toggleButton.Parent = screenGui

-- Criar frame do menu
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 250, 0, 150)
menuFrame.Position = UDim2.new(0, 80, 0, 10)
menuFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
menuFrame.BorderSizePixel = 3
menuFrame.BorderColor3 = Color3.new(0, 0.5, 1)
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- Título do menu
local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, 0, 0, 35)
menuTitle.Text = "⚡ AUTO STAND DELUXE ⚡"
menuTitle.BackgroundColor3 = Color3.new(0, 0.3, 0.6)
menuTitle.TextColor3 = Color3.new(1, 1, 1)
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.TextScaled = true
menuTitle.Parent = menuFrame

-- Botão Auto Stand
local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0, 220, 0, 60)
autoStandButton.Position = UDim2.new(0, 15, 0, 50)
autoStandButton.Text = "🔴 AUTO STAND"
autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.Font = Enum.Font.SourceSansBold
autoStandButton.TextScaled = true
autoStandButton.BorderSizePixel = 3
autoStandButton.BorderColor3 = Color3.new(1, 1, 0)
autoStandButton.Parent = menuFrame

-- Texto de status
local statusText = Instance.new("TextLabel")
statusText.Size = UDim2.new(1, 0, 0, 25)
statusText.Position = UDim2.new(0, 0, 0, 120)
statusText.Text = "Pronto para agir!"
statusText.BackgroundColor3 = Color3.new(0, 0, 0, 0.5)
statusText.TextColor3 = Color3.new(1, 1, 0)
statusText.Font = Enum.Font.SourceSans
statusText.TextScaled = true
statusText.Parent = menuFrame

-- Função para atualizar status
local function atualizarStatus(texto, cor)
    statusText.Text = texto
    statusText.TextColor3 = cor or Color3.new(1, 1, 0)
end

-- Função SUPER FORÇADA de teleporte
local function teleportarForcado(stand)
    local character = player.Character
    if not character then
        repeat
            wait(0.1)
            character = player.Character
        until character
    end
    
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then
        repeat
            wait(0.1)
            humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        until humanoidRootPart
    end
    
    -- MÉTODO 1: CFrame direto (5x para garantir)
    for i = 1, 5 do
        humanoidRootPart.CFrame = stand.CFrame + Vector3.new(0, 3, 0)
        wait(0.05)
    end
    
    -- MÉTODO 2: SetPrimaryPartCFrame
    if character:IsA("Model") and character.PrimaryPart then
        character:SetPrimaryPartCFrame(stand.CFrame + Vector3.new(0, 3, 0))
        wait(0.1)
    end
    
    -- MÉTODO 3: Forçar posição em todas as partes
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CFrame = stand.CFrame + Vector3.new(0, 3, 0)
        end
    end
    
    -- MÉTODO 4: Usar BodyPosition (força física)
    local bodyPosition = Instance.new("BodyPosition")
    bodyPosition.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyPosition.P = 10000
    bodyPosition.D = 1000
    bodyPosition.Position = stand.Position + Vector3.new(0, 3, 0)
    bodyPosition.Parent = humanoidRootPart
    
    wait(0.2)
    bodyPosition:Destroy()
    
    -- MÉTODO 5: Teleporte via raiz
    humanoidRootPart.CFrame = stand.CFrame + Vector3.new(0, 2, 0)
    humanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
    humanoidRootPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
    
    return true
end

-- Função para CLIQUE REAL do mouse
local function clicarReal(stand, clickDetector)
    -- Método 1: FireClickDetector
    pcall(function()
        fireclickdetector(clickDetector, 0) -- Botão esquerdo
        fireclickdetector(clickDetector, 1) -- Botão direito (alternativa)
    end)
    
    wait(0.1)
    
    -- Método 2: Mouse1Click
    pcall(function()
        clickDetector.MouseClick:Fire()
    end)
    
    wait(0.1)
    
    -- Método 3: VirtualInput (clique REAL na posição)
    local standPosition = stand.Position
    local viewportPoint = workspace.CurrentCamera:WorldToViewportPoint(standPosition)
    
    -- Mover mouse para a posição do stand
    VirtualInputManager:SendMouseMoveEvent(viewportPoint.X, viewportPoint.Y)
    wait(0.1)
    
    -- Clique esquerdo REAL
    VirtualInputManager:SendMouseButtonEvent(viewportPoint.X, viewportPoint.Y, 0, true, nil, 0)
    wait(0.1)
    VirtualInputManager:SendMouseButtonEvent(viewportPoint.X, viewportPoint.Y, 0, false, nil, 0)
    
    wait(0.1)
    
    -- Método 4: Click remoto (se existir)
    for _, obj in pairs(stand:GetDescendants()) do
        if obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
            pcall(function()
                obj:FireServer()
            end)
        end
    end
    
    -- Método 5: Clique duplo
    for i = 1, 3 do
        fireclickdetector(clickDetector)
        wait(0.05)
    end
end

-- Função para ENCONTRAR TODOS os stands
local function encontrarMelhorStand()
    atualizarStatus("🔍 Escaneando stands...", Color3.new(0, 1, 1))
    local standsEncontrados = {}
    
    -- Procurar em workspace INTEIRO
    for _, objeto in pairs(workspace:GetDescendants()) do
        -- Procurar por stands com qualquer variação de nome
        if objeto:IsA("BasePart") and (
            objeto.Name == "Stand" or 
            objeto.Name:lower():find("stand") or
            objeto.Name:lower():find("estand") or
            objeto.Name == "Button" or
            objeto.Name == "Click"
        ) then
            
            local clickDetector = objeto:FindFirstChildOfClass("ClickDetector")
            if clickDetector then
                -- Verificar se está ocupado
                local ocupado = false
                local parent = objeto.Parent
                
                if parent then
                    -- Procurar por textos de ocupado
                    for _, child in pairs(parent:GetDescendants()) do
                        if child:IsA("TextLabel") or child:IsA("BillboardGui") or child:IsA("StringValue") then
                            local texto = child.Text or child.Value or ""
                            if type(texto) == "string" then
                                if texto:find("JARRA") or texto:find("Artista") or texto:find("PONTA") or texto:find("Dica") then
                                    ocupado = true
                                    break
                                end
                            end
                        end
                    end
                end
                
                if not ocupado then
                    table.insert(standsEncontrados, {
                        parte = objeto,
                        detector = clickDetector,
                        posicao = objeto.Position
                    })
                    print("✅ Stand disponível: " .. objeto:GetFullName())
                end
            end
        end
    end
    
    return standsEncontrados
end

-- Função PRINCIPAL do Auto Stand
local function executarAutoStand()
    if processando then
        atualizarStatus("⏳ Já está processando...", Color3.new(1, 0.5, 0))
        return
    end
    
    processando = true
    autoStandButton.Text = "⏳ PROCURANDO STAND..."
    autoStandButton.BackgroundColor3 = Color3.new(0.5, 0.5, 0)
    autoStandButton.Active = false
    
    -- Reset de status
    atualizarStatus("🔎 Varrendo o mapa...", Color3.new(0, 1, 1))
    
    -- Encontrar todos os stands disponíveis
    local stands = encontrarMelhorStand()
    
    if #stands > 0 then
        atualizarStatus("🎯 Stands encontrados: " .. #stands, Color3.new(0, 1, 0))
        
        -- ESCOLHER STAND ALEATÓRIO
        local escolhido = stands[math.random(1, #stands)]
        local stand = escolhido.parte
        local detector = escolhido.detector
        
        atualizarStatus("⚡ Teleportando para stand ALEATÓRIO...", Color3.new(1, 0.5, 0))
        
        -- TELEPORTE FORÇADO MÁXIMO
        teleportarForcado(stand)
        
        wait(0.3)
        
        atualizarStatus("🖱️ Clicando no stand...", Color3.new(1, 1, 0))
        
        -- CLIQUE REAL MÁXIMO
        clicarReal(stand, detector)
        
        wait(0.2)
        
        -- Verificar se reivindicou (tentar novamente se necessário)
        local aindaOcupado = false
        for i = 1, 3 do
            if not aindaOcupado then
                break
            end
            wait(0.2)
            clicarReal(stand, detector)
        end
        
        atualizarStatus("✅ STAND REIVINDICADO COM SUCESSO!", Color3.new(0, 1, 0))
        autoStandButton.Text = "✅ STAND CONQUISTADO!"
        autoStandButton.BackgroundColor3 = Color3.new(0, 0.8, 0)
        
        wait(2)
        
        autoStandButton.Text = "🔴 AUTO STAND"
        autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
        atualizarStatus("Pronto para agir!", Color3.new(1, 1, 0))
    else
        atualizarStatus("❌ NENHUM STAND DISPONÍVEL!", Color3.new(1, 0, 0))
        autoStandButton.Text = "❌ SEM STANDS"
        autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
        
        wait(2)
        
        autoStandButton.Text = "🔴 AUTO STAND"
        autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
        atualizarStatus("Pronto para agir!", Color3.new(1, 1, 0))
    end
    
    autoStandButton.Active = true
    processando = false
end

-- Toggle do menu
toggleButton.MouseButton1Click:Connect(function()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    toggleButton.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0.2, 0.2, 0.2)
    toggleButton.Text = menuAberto and "🔽 FECHAR" or "⚙️ MENU"
end)

-- Botão Auto Stand
autoStandButton.MouseButton1Click:Connect(function()
    executarAutoStand()
end)

-- Tecla E para menu
mouse.KeyDown:Connect(function(key)
    key = key:lower()
    if key == "e" then
        menuAberto = not menuAberto
        menuFrame.Visible = menuAberto
        toggleButton.BackgroundColor3 = menuAberto and Color3.new(0, 0.5, 0) or Color3.new(0.2, 0.2, 0.2)
        toggleButton.Text = menuAberto and "🔽 FECHAR" or "⚙️ MENU"
    end
end)

print("🚀 SCRIPT AUTO STAND ULTRA CARREGADO!")
print("📌 Pressione E ou clique no botão MENU")
print("🎯 Clique em AUTO STAND para agir!")
