-- Auto Stand GUI Script
-- Coloque este script dentro de um ScreenGui ou em StarterGui

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- Criar a ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandGUI"
screenGui.Parent = player.PlayerGui

-- Variáveis de controle
local menuAberto = true
local autoStandAtivo = false
local processando = false

-- Criar botão toggle do menu (sempre visível)
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.Text = "☰"
toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextSize = 30
toggleButton.Parent = screenGui

-- Criar o menu principal
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 250, 0, 100)
menuFrame.Position = UDim2.new(0, 80, 0, 20)
menuFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
menuFrame.BackgroundTransparency = 0.1
menuFrame.BorderSizePixel = 2
menuFrame.BorderColor3 = Color3.new(0.3, 0.3, 0.3)
menuFrame.Parent = screenGui

-- Criar título do menu
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "AUTO STAND MENU"
title.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
title.TextColor3 = Color3.new(1, 1, 0)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.Parent = menuFrame

-- Criar frame para o botão Auto Stand
local autoStandFrame = Instance.new("Frame")
autoStandFrame.Size = UDim2.new(1, -20, 0, 40)
autoStandFrame.Position = UDim2.new(0, 10, 0, 40)
autoStandFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
autoStandFrame.Parent = menuFrame

-- Criar texto do botão Auto Stand
local autoStandText = Instance.new("TextLabel")
autoStandText.Size = UDim2.new(0.6, 0, 1, 0)
autoStandText.Position = UDim2.new(0, 10, 0, 0)
autoStandText.Text = "AUTO STAND"
autoStandText.BackgroundTransparency = 1
autoStandText.TextColor3 = Color3.new(1, 1, 1)
autoStandText.Font = Enum.Font.SourceSans
autoStandText.TextSize = 16
autoStandText.TextXAlignment = Enum.TextXAlignment.Left
autoStandText.Parent = autoStandFrame

-- Criar botão toggle ON/OFF para Auto Stand
local autoStandButton = Instance.new("TextButton")
autoStandButton.Name = "AutoStandToggle"
autoStandButton.Size = UDim2.new(0, 50, 0, 30)
autoStandButton.Position = UDim2.new(1, -60, 0.5, -15)
autoStandButton.Text = "OFF"
autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.Font = Enum.Font.SourceSansBold
autoStandButton.TextSize = 14
autoStandButton.Parent = autoStandFrame

-- Função para alternar menu
local function toggleMenu()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
    toggleButton.Text = menuAberto and "☰" or "☰"
end

-- Função principal do Auto Stand
local function autoStand()
    if not autoStandAtivo or processando then return end
    
    processando = true
    print("Procurando stands disponíveis...")
    
    -- Procurar todos os botões chamados "Stand" no mapa
    local stands = workspace:FindFirstChild("Map"):GetDescendants()
    -- Alternativa: local stands = workspace:GetDescendants()
    
    local standsDisponiveis = {}
    
    for _, obj in ipairs(stands) do
        -- Verificar se é um botão chamado "Stand"
        if obj.Name == "Stand" and obj:IsA("BasePart") then
            local ocupado = false
            
            -- Verificar players próximos (20 studs de raio)
            for _, p in ipairs(game.Players:GetPlayers()) do
                if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    local distancia = (p.Character.HumanoidRootPart.Position - obj.Position).Magnitude
                    if distancia < 20 then
                        ocupado = true
                        break
                    end
                end
            end
            
            -- Se não estiver ocupado, adicionar à lista
            if not ocupado then
                table.insert(standsDisponiveis, obj)
            end
        end
    end
    
    -- Verificar se encontrou stands disponíveis
    if #standsDisponiveis > 0 then
        -- Escolher um stand aleatório
        local standEscolhido = standsDisponiveis[math.random(1, #standsDisponiveis)]
        
        print("Stand encontrado! Teleportando...")
        
        -- Teleportar player até o stand
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = CFrame.new(standEscolhido.Position + Vector3.new(0, 3, 0))
            
            -- Aguardar um momento e clicar no botão
            task.wait(0.5)
            
            -- Simular clique no botão
            local clickDetector = standEscolhido:FindFirstChildOfClass("ClickDetector")
            if clickDetector then
                clickDetector:FireClient(player)
                print("Stand reivindicado com sucesso!")
                
                -- Desligar Auto Stand após conseguir um stand
                autoStandAtivo = false
                autoStandButton.Text = "OFF"
                autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
            end
        end
    else
        print("Nenhum stand disponível encontrado!")
    end
    
    processando = false
end

-- Conectar eventos dos botões

-- Botão toggle do menu
toggleButton.MouseButton1Click:Connect(toggleMenu)

-- Botão toggle Auto Stand
autoStandButton.MouseButton1Click:Connect(function()
    autoStandAtivo = not autoStandAtivo
    
    if autoStandAtivo then
        autoStandButton.Text = "ON"
        autoStandButton.BackgroundColor3 = Color3.new(0.2, 0.8, 0.2)
        autoStand() -- Executar imediatamente
    else
        autoStandButton.Text = "OFF"
        autoStandButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
    end
end)

-- Menu começa aberto
menuFrame.Visible = menuAberto

print("Script Auto Stand carregado com sucesso!")
