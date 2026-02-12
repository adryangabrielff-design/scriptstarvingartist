--[[
    Script de Auto Stand - Menu Simples
    Criado para facilitar expansões futuras
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Variáveis globais do menu
local menu = {
    aberto = true,
    autoStandAtivo = false,
    procurandoStand = false
}

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MenuPrincipal"
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false

-- Função para criar botão toggle
local function criarBotaoToggle(nome, posicao, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 150, 0, 35)
    frame.Position = posicao
    frame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    frame.BorderSizePixel = 0
    frame.Parent = menu.frameMenu
    
    local borda = Instance.new("Frame")
    borda.Size = UDim2.new(1, 0, 0, 2)
    borda.Position = UDim2.new(0, 0, 1, -2)
    borda.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    borda.BorderSizePixel = 0
    borda.Parent = frame
    
    local texto = Instance.new("TextLabel")
    texto.Size = UDim2.new(1, -40, 1, 0)
    texto.Position = UDim2.new(0, 10, 0, 0)
    texto.BackgroundTransparency = 1
    texto.Text = nome
    texto.TextColor3 = Color3.fromRGB(255, 255, 255)
    texto.TextXAlignment = Enum.TextXAlignment.Left
    texto.Font = Enum.Font.GothamBold
    texto.TextSize = 14
    texto.Parent = frame
    
    local toggle = Instance.new("TextButton")
    toggle.Size = UDim2.new(0, 30, 0, 30)
    toggle.Position = UDim2.new(1, -35, 0, 2.5)
    toggle.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    toggle.Text = "OFF"
    toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggle.TextSize = 10
    toggle.Font = Enum.Font.GothamBold
    toggle.BorderSizePixel = 0
    toggle.AutoButtonColor = false
    toggle.Parent = frame
    
    local ativo = false
    
    toggle.MouseButton1Click:Connect(function()
        ativo = not ativo
        
        if ativo then
            toggle.BackgroundColor3 = Color3.fromRGB(50, 255, 50)
            toggle.Text = "ON"
            borda.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        else
            toggle.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
            toggle.Text = "OFF"
            borda.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        end
        
        if callback then
            callback(ativo)
        end
    end)
    
    return toggle
end

-- Função do Auto Stand
local function autoStandFunction(estado)
    menu.autoStandAtivo = estado
    
    if estado and not menu.procurandoStand then
        menu.procurandoStand = true
        print("🔍 Procurando stands disponíveis...")
        
        -- Função para encontrar e interagir com Stand
        local function encontrarStand()
            if not menu.autoStandAtivo then return end
            
            local standsDisponiveis = {}
            
            -- Procurar botões "Stand" no mapa
            for _, objeto in ipairs(workspace:GetDescendants()) do
                if objeto:IsA("TextButton") or objeto:IsA("Part") or objeto:IsA("BasePart") then
                    if objeto.Name:find("Stand") then
                        -- Verificar se tem texto de player ocupando
                        local textoOcupado = objeto:FindFirstChild("TextLabel") 
                            or objeto.Parent:FindFirstChild("TextLabel")
                            or objeto:FindFirstChild("BillboardGui")
                        
                        local ocupado = false
                        
                        if textoOcupado then
                            local texto = textoOcupado.Text or ""
                            if texto:find("JARRA DE PONTA") or texto:find("Artista da Dica") then
                                ocupado = true
                            end
                        end
                        
                        -- Verificar também por BillboardGui
                        for _, child in ipairs(objeto:GetDescendants()) do
                            if child:IsA("BillboardGui") and child:FindFirstChild("TextLabel") then
                                local texto = child.TextLabel.Text or ""
                                if texto:find("JARRA DE PONTA") or texto:find("Artista da Dica") then
                                    ocupado = true
                                end
                            end
                        end
                        
                        if not ocupado then
                            table.insert(standsDisponiveis, objeto)
                        end
                    end
                end
            end
            
            if #standsDisponiveis > 0 and menu.autoStandAtivo then
                -- Escolher stand aleatório
                local standEscolhido = standsDisponiveis[math.random(1, #standsDisponiveis)]
                
                print("🎯 Stand encontrado! Teleportando...")
                
                -- Teleportar
                if standEscolhido:IsA("BasePart") then
                    player.Character:SetPrimaryPartCFrame(standEscolhido.CFrame * CFrame.new(0, 3, 0))
                    wait(0.5)
                end
                
                -- Clicar no botão
                if standEscolhido:IsA("TextButton") then
                    fireclickdetector(standEscolhido)
                else
                    -- Tentar encontrar ClickDetector
                    local clickDetector = standEscolhido:FindFirstChild("ClickDetector")
                    if clickDetector then
                        fireclickdetector(clickDetector)
                    end
                end
                
                print("✅ Stand reivindicado com sucesso!")
                menu.autoStandAtivo = false
                menu.procurandoStand = false
                
                -- Atualizar botão para OFF
                for _, child in ipairs(menu.frameMenu:GetChildren()) do
                    if child:IsA("Frame") and child:FindFirstChild("TextLabel") then
                        local textoLabel = child.TextLabel
                        if textoLabel.Text == "AUTO STAND" then
                            local botao = child:FindFirstChild("TextButton")
                            if botao then
                                botao.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
                                botao.Text = "OFF"
                                child.Frame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
                            end
                        end
                    end
                end
            else
                if menu.autoStandAtivo then
                    print("❌ Nenhum stand disponível no momento. Tentando novamente...")
                    wait(2)
                    encontrarStand()
                else
                    menu.procurandoStand = false
                end
            end
        end
        
        -- Iniciar busca
        coroutine.wrap(encontrarStand)()
    else
        menu.procurandoStand = false
        print("⏹️ Auto Stand desativado")
    end
end

-- Criar botão de toggle do menu
local botaoToggle = Instance.new("TextButton")
botaoToggle.Name = "ToggleMenu"
botaoToggle.Size = UDim2.new(0, 50, 0, 50)
botaoToggle.Position = UDim2.new(0, 20, 0, 20)
botaoToggle.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
botaoToggle.Text = "≡"
botaoToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
botaoToggle.TextSize = 30
botaoToggle.Font = Enum.Font.GothamBold
botaoToggle.BorderSizePixel = 0
botaoToggle.Parent = screenGui

-- Criar frame principal do menu
local frameMenu = Instance.new("Frame")
frameMenu.Name = "Menu"
frameMenu.Size = UDim2.new(0, 200, 0, 300)
frameMenu.Position = UDim2.new(0, 80, 0, 20)
frameMenu.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frameMenu.BackgroundTransparency = 0.1
frameMenu.BorderSizePixel = 0
frameMenu.Parent = screenGui
menu.frameMenu = frameMenu

-- Criar borda do menu
local bordaMenu = Instance.new("Frame")
bordaMenu.Size = UDim2.new(1, 0, 0, 2)
bordaMenu.Position = UDim2.new(0, 0, 0, 0)
bordaMenu.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
bordaMenu.BorderSizePixel = 0
bordaMenu.Parent = frameMenu

-- Criar título do menu
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 40)
titulo.Position = UDim2.new(0, 0, 0, 0)
titulo.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
titulo.Text = "MENU PRINCIPAL"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.Font = Enum.Font.GothamBold
titulo.TextSize = 16
titulo.BorderSizePixel = 0
titulo.Parent = frameMenu

-- Área de conteúdo
local conteudo = Instance.new("Frame")
conteudo.Name = "Conteudo"
conteudo.Size = UDim2.new(1, -20, 1, -60)
conteudo.Position = UDim2.new(0, 10, 0, 50)
conteudo.BackgroundTransparency = 1
conteudo.Parent = frameMenu

-- Criar botões
local yPos = 0

-- Botão AUTO STAND
criarBotaoToggle("AUTO STAND", UDim2.new(0, 0, 0, yPos), autoStandFunction)

-- Função para toggle do menu
botaoToggle.MouseButton1Click:Connect(function()
    menu.aberto = not menu.aberto
    frameMenu.Visible = menu.aberto
    botaoToggle.Text = menu.aberto and "✕" or "≡"
end)

print("✅ Script carregado com sucesso!")
print("📌 Clique no botão '≡' para abrir/fechar o menu")
