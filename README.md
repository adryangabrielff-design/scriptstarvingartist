--[[
    Script GUI Simples - Auto Stand
    Fácil de expandir e modificar
]]

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local guiService = game:GetService("GuiService")
local tweenService = game:GetService("TweenService")

-- Variáveis de estado
local menuAberto = false
local autoStandAtivo = false

-- Criar ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MenuPrincipal"
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Criar botão toggle do menu (sempre visível)
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleMenu"
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
toggleButton.BackgroundTransparency = 0.2
toggleButton.BorderSizePixel = 0
toggleButton.Text = "≡"
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.TextScaled = true
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.Parent = screenGui

-- Arredondar bordas do toggle
local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 8)
toggleCorner.Parent = toggleButton

-- Criar frame do menu (invisível inicialmente)
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 250, 0, 180)
menuFrame.Position = UDim2.new(0, 80, 0, 20)
menuFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
menuFrame.BackgroundTransparency = 0.1
menuFrame.BorderSizePixel = 0
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- Arredondar bordas do menu
local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0, 12)
menuCorner.Parent = menuFrame

-- Adicionar título ao menu
local menuTitle = Instance.new("TextLabel")
menuTitle.Name = "Title"
menuTitle.Size = UDim2.new(1, 0, 0, 40)
menuTitle.Position = UDim2.new(0, 0, 0, 0)
menuTitle.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
menuTitle.BackgroundTransparency = 0.3
menuTitle.BorderSizePixel = 0
menuTitle.Text = "MENU PRINCIPAL"
menuTitle.TextColor3 = Color3.new(1, 1, 1)
menuTitle.TextScaled = true
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.Parent = menuFrame

-- Arredondar topo do título
local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = menuTitle

-- Criar container para os botões
local buttonContainer = Instance.new("Frame")
buttonContainer.Name = "ButtonContainer"
buttonContainer.Size = UDim2.new(1, -20, 1, -50)
buttonContainer.Position = UDim2.new(0, 10, 0, 45)
buttonContainer.BackgroundTransparency = 1
buttonContainer.Parent = menuFrame

-- Criar botão Auto Stand
local autoStandButton = Instance.new("TextButton")
autoStandButton.Name = "AutoStand"
autoStandButton.Size = UDim2.new(1, 0, 0, 40)
autoStandButton.Position = UDim2.new(0, 0, 0, 0)
autoStandButton.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
autoStandButton.BorderSizePixel = 0
autoStandButton.Text = "AUTO STAND"
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.TextScaled = true
autoStandButton.Font = Enum.Font.SourceSans
autoStandButton.Parent = buttonContainer

local autoStandCorner = Instance.new("UICorner")
autoStandCorner.CornerRadius = UDim.new(0, 8)
autoStandCorner.Parent = autoStandButton

-- Criar botão CLICK
local clickButton = Instance.new("TextButton")
clickButton.Name = "Click"
clickButton.Size = UDim2.new(0.8, 0, 0, 40)
clickButton.Position = UDim2.new(0.1, 0, 0, 50)
clickButton.BackgroundColor3 = Color3.new(0.2, 0.6, 0.2)
clickButton.BorderSizePixel = 0
clickButton.Text = "CLICK"
clickButton.TextColor3 = Color3.new(1, 1, 1)
clickButton.TextScaled = true
clickButton.Font = Enum.Font.SourceSansBold
clickButton.Parent = buttonContainer

local clickCorner = Instance.new("UICorner")
clickCorner.CornerRadius = UDim.new(0, 8)
clickCorner.Parent = clickButton

-- Adicionar status label
local statusLabel = Instance.new("TextLabel")
statusLabel.Name = "Status"
statusLabel.Size = UDim2.new(1, 0, 0, 30)
statusLabel.Position = UDim2.new(0, 0, 0, 100)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Desativado"
statusLabel.TextColor3 = Color3.new(1, 0.3, 0.3)
statusLabel.TextScaled = true
statusLabel.Font = Enum.Font.SourceSans
statusLabel.Parent = buttonContainer

-- Função para alternar menu
local function alternarMenu()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    
    -- Animação simples do botão toggle
    if menuAberto then
        toggleButton.Text = "✕"
        toggleButton.BackgroundColor3 = Color3.new(0.4, 0.2, 0.2)
    else
        toggleButton.Text = "≡"
        toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    end
end

-- Função para encontrar e interagir com botões Stand
local function encontrarEAtivarStand()
    if not autoStandAtivo then return end
    
    local stands = {}
    
    -- Procurar botões chamados "Stand" em todas as partes
    for _, objeto in pairs(workspace:GetDescendants()) do
        if objeto:IsA("Part") or objeto:IsA("MeshPart") or objeto:IsA("BasePart") then
            if objeto.Name:lower():find("stand") then
                table.insert(stands, objeto)
            end
        end
        -- Procurar também em ClickDetectors e ProximityPrompts
        if objeto:IsA("ClickDetector") and objeto.Parent and objeto.Parent.Name:lower():find("stand") then
            table.insert(stands, objeto.Parent)
        end
        if objeto:IsA("ProximityPrompt") and objeto.Parent and objeto.Parent.Name:lower():find("stand") then
            table.insert(stands, objeto.Parent)
        end
    end
    
    if #stands > 0 then
        -- Escolher stand aleatório
        local standEscolhido = stands[math.random(1, #stands)]
        
        -- Teleportar jogador
        if standEscolhido:IsA("BasePart") then
            player.Character:SetPrimaryPartCFrame(standEscolhido.CFrame + Vector3.new(0, 3, 0))
            
            -- Aguardar um pouco
            task.wait(0.5)
            
            -- Tentar ativar o stand
            local clickDetector = standEscolhido:FindFirstChildOfClass("ClickDetector")
            local prompt = standEscolhido:FindFirstChildOfClass("ProximityPrompt")
            
            if clickDetector then
                fireclickdetector(clickDetector)
                statusLabel.Text = "✓ Stand ativado!"
            elseif prompt then
                -- Simular interação com ProximityPrompt
                prompt:InputHoldBegin()
                task.wait(0.5)
                prompt:InputHoldEnd()
                statusLabel.Text = "✓ Stand ativado!"
            else
                statusLabel.Text = "✗ Sem detector"
            end
        end
    else
        statusLabel.Text = "✗ Nenhum stand encontrado"
    end
    
    -- Loop enquanto ativo
    if autoStandAtivo then
        task.wait(3) -- Esperar 3 segundos
        encontrarEAtivarStand()
    end
end

-- Função para alternar Auto Stand
local function alternarAutoStand()
    autoStandAtivo = not autoStandAtivo
    
    if autoStandAtivo then
        autoStandButton.BackgroundColor3 = Color3.new(0.2, 0.6, 0.2)
        statusLabel.Text = "Auto Stand: ATIVO"
        statusLabel.TextColor3 = Color3.new(0.3, 1, 0.3)
        
        -- Iniciar a função
        coroutine.wrap(function()
            encontrarEAtivarStand()
        end)()
    else
        autoStandButton.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
        statusLabel.Text = "Auto Stand: Desativado"
        statusLabel.TextColor3 = Color3.new(1, 0.3, 0.3)
    end
end

-- Conectar eventos dos botões
toggleButton.MouseButton1Click:Connect(alternarMenu)
autoStandButton.MouseButton1Click:Connect(alternarAutoStand)

-- Botão CLICK - executa uma vez
clickButton.MouseButton1Click:Connect(function()
    if autoStandAtivo then
        -- Se auto stand estiver ativo, apenas executa uma busca manual
        coroutine.wrap(function()
            autoStandAtivo = false
            alternarAutoStand() -- Desativa o auto
            task.wait(0.1)
            autoStandAtivo = true
            encontrarEAtivarStand() -- Executa uma vez
            autoStandAtivo = false
            alternarAutoStand() -- Reativa
        end)()
    else
        -- Se não estiver ativo, executa uma vez
        coroutine.wrap(function()
            autoStandAtivo = true
            encontrarEAtivarStand()
            autoStandAtivo = false
        end)()
    end
end)

-- Configuração inicial
statusLabel.Text = "Desativado"
statusLabel.TextColor3 = Color3.new(1, 0.3, 0.3)

print("Script GUI carregado! Menu criado com sucesso.")
