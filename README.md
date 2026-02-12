--[[
    Script: Auto Stand Menu
    LocalScript para GUI de Auto Stand
    Criado para Roblox
]]

local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Variáveis de controle
local autoStandAtivo = false
local menuVisivel = true
local autoStandCoroutine = nil

-- Criar ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoStandMenu"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false

-- ========== CRIAR ELEMENTOS DA INTERFACE ==========

-- Frame principal do menu
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 200, 0, 250)
menuFrame.Position = UDim2.new(0, 50, 0, 100)
menuFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
menuFrame.BackgroundTransparency = 0.1
menuFrame.BorderSizePixel = 0
menuFrame.Active = true
menuFrame.Draggable = true
menuFrame.Parent = screenGui

-- Estilo do menu (arredondado)
local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 10)
uiCorner.Parent = menuFrame

-- Título do menu
local tituloLabel = Instance.new("TextLabel")
tituloLabel.Name = "Titulo"
tituloLabel.Size = UDim2.new(1, 0, 0, 40)
tituloLabel.Position = UDim2.new(0, 0, 0, 0)
tituloLabel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
tituloLabel.BackgroundTransparency = 0.2
tituloLabel.BorderSizePixel = 0
tituloLabel.Text = "Auto Stand Menu"
tituloLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
tituloLabel.TextSize = 18
tituloLabel.Font = Enum.Font.SourceSansBold
tituloLabel.Parent = menuFrame

-- Botão toggle menu (abrir/fechar)
local toggleMenuButton = Instance.new("TextButton")
toggleMenuButton.Name = "ToggleMenuButton"
toggleMenuButton.Size = UDim2.new(0, 40, 0, 40)
toggleMenuButton.Position = UDim2.new(0, 10, 0, 10)
toggleMenuButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
toggleMenuButton.BorderSizePixel = 0
toggleMenuButton.Text = "✕"
toggleMenuButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleMenuButton.TextSize = 24
toggleMenuButton.Font = Enum.Font.SourceSansBold
toggleMenuButton.Parent = screenGui

-- Arredondar botão toggle
local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(1, 0)
toggleCorner.Parent = toggleMenuButton

-- ========== CONTEÚDO DO MENU ==========

local conteudoFrame = Instance.new("Frame")
conteudoFrame.Name = "Conteudo"
conteudoFrame.Size = UDim2.new(1, -20, 1, -50)
conteudoFrame.Position = UDim2.new(0, 10, 0, 45)
conteudoFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
conteudoFrame.BackgroundTransparency = 0.1
conteudoFrame.BorderSizePixel = 0
conteudoFrame.Parent = menuFrame

-- Arredondar conteúdo
local conteudoCorner = Instance.new("UICorner")
conteudoCorner.CornerRadius = UDim.new(0, 8)
conteudoCorner.Parent = conteudoFrame

-- Layout para organizar os botões
local uiListLayout = Instance.new("UIListLayout")
uiListLayout.Padding = UDim.new(0, 5)
uiListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
uiListLayout.VerticalAlignment = Enum.VerticalAlignment.Top
uiListLayout.Parent = conteudoFrame

-- Criar botão Auto Stand
local autoStandButton = criarBotaoToggle("Auto Stand", Color3.fromRGB(0, 160, 255))
autoStandButton.Parent = conteudoFrame

-- ========== FUNÇÕES ==========

-- Função para criar botões toggle
function criarBotaoToggle(texto, cor)
    local botao = Instance.new("TextButton")
    botao.Name = texto .. "Button"
    botao.Size = UDim2.new(1, -20, 0, 40)
    botao.BackgroundColor3 = cor
    botao.BorderSizePixel = 0
    botao.Text = texto .. " [OFF]"
    botao.TextColor3 = Color3.fromRGB(255, 255, 255)
    botao.TextSize = 16
    botao.Font = Enum.Font.SourceSans
    
    local botaoCorner = Instance.new("UICorner")
    botaoCorner.CornerRadius = UDim.new(0, 6)
    botaoCorner.Parent = botao
    
    -- Variável de estado dentro do botão
    local estado = false
    
    -- Conectar evento de clique
    botao.MouseButton1Click:Connect(function()
        estado = not estado
        
        if estado then
            botao.Text = texto .. " [ON]"
            botao.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
            
            -- Ativar função específica baseada no texto
            if texto == "Auto Stand" then
                ativarAutoStand()
            end
            
        else
            botao.Text = texto .. " [OFF]"
            botao.BackgroundColor3 = cor
            
            -- Desativar função específica baseada no texto
            if texto == "Auto Stand" then
                desativarAutoStand()
            end
        end
    end)
    
    return botao
end

-- Função para ativar Auto Stand
function ativarAutoStand()
    if autoStandAtivo then return end
    
    autoStandAtivo = true
    print("Auto Stand ATIVADO")
    
    -- Iniciar coroutine para não travar o jogo
    autoStandCoroutine = coroutine.create(function()
        while autoStandAtivo do
            procurarEAtivarStand()
            task.wait(2) -- Espera 2 segundos entre tentativas
        end
    end)
    
    coroutine.resume(autoStandCoroutine)
end

-- Função para desativar Auto Stand
function desativarAutoStand()
    autoStandAtivo = false
    autoStandCoroutine = nil
    print("Auto Stand DESATIVADO")
end

-- Função para procurar e ativar Stand aleatório
function procurarEAtivarStand()
    -- Verificar se o jogador existe
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
        print("Jogador não está carregado")
        return
    end
    
    -- Procurar botões "Stand" no Workspace
    local botoesStand = {}
    
    -- Função recursiva para procurar botões
    local function procurarBotoes(objeto)
        for _, child in pairs(objeto:GetChildren()) do
            if child:IsA("BasePart") and child.Name == "Stand" then
                table.insert(botoesStand, child)
            else
                procurarBotoes(child)
            end
        end
    end
    
    procurarBotoes(workspace)
    
    -- Verificar se encontrou algum botão
    if #botoesStand == 0 then
        print("Nenhum botão Stand encontrado")
        return
    end
    
    -- Escolher botão aleatório
    local botaoEscolhido = botoesStand[math.random(1, #botoesStand)]
    print("Botão selecionado:", botaoEscolhido:GetFullName())
    
    -- Teleportar para o botão
    local humanoidRootPart = player.Character.HumanoidRootPart
    local botaoPosicao = botaoEscolhido.Position
    
    -- Teleportar suavemente
    humanoidRootPart.CFrame = CFrame.new(botaoPosicao + Vector3.new(0, 3, 0))
    task.wait(0.3)
    
    -- Acionar o botão (simular clique/ativação)
    if botaoEscolhido:IsA("BasePart") then
        -- Tentar diferentes métodos de ativação
        fireclickdetector(botaoEscolhido:FindFirstChild("ClickDetector"))
        
        -- Método alternativo: usar Touched
        local touchedEvent = botaoEscolhido.Touched:Connect(function() end)
        touchedEvent:Disconnect()
        
        print("Stand reivindicado com sucesso!")
    end
end

-- ========== FUNÇÕES DO MENU ==========

-- Função para toggle menu (abrir/fechar)
toggleMenuButton.MouseButton1Click:Connect(function()
    menuVisivel = not menuVisivel
    menuFrame.Visible = menuVisivel
    
    if menuVisivel then
        toggleMenuButton.Text = "✕"
        toggleMenuButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
    else
        toggleMenuButton.Text = "☰"
        toggleMenuButton.BackgroundColor3 = Color3.fromRGB(100, 200, 100)
    end
end)

-- Configurar sombra para o menu
local shadow = Instance.new("ImageLabel")
shadow.Name = "Shadow"
shadow.Size = UDim2.new(1, 10, 1, 10)
shadow.Position = UDim2.new(0, -5, 0, -5)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxassetid://6014261993"
shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
shadow.ImageTransparency = 0.7
shadow.ScaleType = Enum.ScaleType.Slice
shadow.SliceCenter = Rect.new(10, 10, 118, 118)
shadow.Parent = menuFrame
shadow.ZIndex = -1

print("Script Auto Stand Menu carregado com sucesso!")

-- Limpeza quando o jogador sair
game.Players.LocalPlayer.OnTeleport:Connect(function()
    if screenGui then
        screenGui:Destroy()
    end
end)
