-- Script principal para GUI de Auto Stand
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui

-- Variáveis de controle
local menuAberto = false
local autoStandAtivo = false
local standsProcessados = {}

-- Criar botão principal para abrir/fechar menu
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "MENU"
toggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Parent = screenGui

-- Criar frame do menu (inicialmente invisível)
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 200, 0, 300)
menuFrame.Position = UDim2.new(0, 70, 0, 10)
menuFrame.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
menuFrame.Visible = false
menuFrame.Parent = screenGui

-- Título do menu
local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, 0, 0, 30)
menuTitle.Text = "AUTO STAND MENU"
menuTitle.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
menuTitle.TextColor3 = Color3.new(1, 1, 1)
menuTitle.Parent = menuFrame

-- Botão Auto Stand (toggle)
local autoStandButton = Instance.new("TextButton")
autoStandButton.Size = UDim2.new(0, 180, 0, 40)
autoStandButton.Position = UDim2.new(0, 10, 0, 50)
autoStandButton.Text = "AUTO STAND: OFF"
autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
autoStandButton.TextColor3 = Color3.new(1, 1, 1)
autoStandButton.Parent = menuFrame

-- Função para encontrar stands disponíveis
local function encontrarStandsDisponiveis()
    local standsDisponiveis = {}
    
    -- Procurar por botões "Stand" no mapa
    for _, objeto in pairs(workspace:GetDescendants()) do
        if objeto:IsA("BasePart") and objeto.Name == "Stand" then
            -- Verificar se o stand não tem player e não foi processado
            local temPlayer = false
            local textoJarra = objeto.Parent and objeto.Parent:FindFirstChild("JARRA DE PONTA")
            local textoArtista = objeto.Parent and objeto.Parent:FindFirstChild("Artista da Dica")
            
            if textoJarra or textoArtista then
                temPlayer = true
            end
            
            if not temPlayer and not standsProcessados[objeto] then
                table.insert(standsDisponiveis, objeto)
            end
        end
    end
    
    return standsDisponiveis
end

-- Função para reivindicar stand
local function reivindicarStand(stand)
    if stand and stand:IsA("BasePart") then
        -- Teleportar para o stand
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            character.HumanoidRootPart.CFrame = stand.CFrame + Vector3.new(0, 5, 0)
            
            -- Simular clique no botão
            wait(0.5)
            
            -- Encontrar e clicar no botão ClickDetector
            local clickDetector = stand:FindFirstChildOfClass("ClickDetector")
            if clickDetector then
                fireclickdetector(clickDetector)
                standsProcessados[stand] = true
                print("Stand reivindicado com sucesso!")
                return true
            end
        end
    end
    return false
end

-- Função principal do Auto Stand
local function executarAutoStand()
    if not autoStandAtivo then return end
    
    print("Procurando stands disponíveis...")
    local stands = encontrarStandsDisponiveis()
    
    if #stands > 0 then
        -- Escolher stand aleatório
        local standEscolhido = stands[math.random(1, #stands)]
        print("Stand encontrado! Reivindicando...")
        
        if reivindicarStand(standEscolhido) then
            -- Desativar Auto Stand após sucesso
            autoStandAtivo = false
            autoStandButton.Text = "AUTO STAND: OFF"
            autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
            print("Auto Stand desativado após reivindicar com sucesso")
        end
    else
        print("Nenhum stand disponível encontrado")
        wait(2) -- Aguardar antes de procurar novamente
        if autoStandAtivo then
            executarAutoStand()
        end
    end
end

-- Toggle do menu
toggleButton.MouseButton1Click:Connect(function()
    menuAberto = not menuAberto
    menuFrame.Visible = menuAberto
end)

-- Toggle do Auto Stand
autoStandButton.MouseButton1Click:Connect(function()
    autoStandAtivo = not autoStandAtivo
    
    if autoStandAtivo then
        autoStandButton.Text = "AUTO STAND: ON"
        autoStandButton.BackgroundColor3 = Color3.new(0, 0.5, 0)
        standsProcessados = {} -- Reset dos stands processados
        print("Auto Stand ativado")
        
        -- Iniciar a função de auto stand
        coroutine.wrap(function()
            executarAutoStand()
        end)()
    else
        autoStandButton.Text = "AUTO STAND: OFF"
        autoStandButton.BackgroundColor3 = Color3.new(0.5, 0, 0)
        print("Auto Stand desativado")
    end
end)

-- Opcional: Fechar menu com ESC
mouse.KeyDown:Connect(function(key)
    if key == "e" then -- Pressione E para toggle do menu
        menuAberto = not menuAberto
        menuFrame.Visible = menuAberto
    end
end)

print("Script de Auto Stand carregado com sucesso!")
