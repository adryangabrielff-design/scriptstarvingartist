--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local TEMPO_LOOP = 2 -- segundos

--// SERVICES
local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

-------------------------------------------------
-- HRP
-------------------------------------------------
local function getHRP()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:WaitForChild("HumanoidRootPart")
end

-------------------------------------------------
-- PEGAR PART DO MODEL
-------------------------------------------------
local function getPart(model)
    if model.PrimaryPart then
        return model.PrimaryPart
    end
    
    for _, v in pairs(model:GetDescendants()) do
        if v:IsA("BasePart") then
            return v
        end
    end
end

-------------------------------------------------
-- SPAM TECLA E
-------------------------------------------------
local function spamE(vezes)
    for i = 1, vezes do
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
        task.wait(0.02)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
        task.wait(0.02)
    end
end

-------------------------------------------------
-- SPAM CLIQUE MOUSE
-------------------------------------------------
local function spamMouse(vezes)
    for i = 1, vezes do
        VirtualInputManager:SendMouseButtonEvent(0,0,0,true,game,0)
        task.wait(0.02)
        VirtualInputManager:SendMouseButtonEvent(0,0,0,false,game,0)
        task.wait(0.02)
    end
end

-------------------------------------------------
-- CLICAR BOTÃO CLAIM GUI
-------------------------------------------------
local function clicarBotao()
    for _, gui in pairs(player.PlayerGui:GetDescendants()) do
        
        if gui:IsA("TextButton") or gui:IsA("ImageButton") then
            
            local nome = string.upper(gui.Name or "")
            local texto = string.upper(gui.Text or "")
            
            if string.find(nome,"CLAIM")
            or string.find(nome,"REIVIND")
            or string.find(texto,"CLAIM")
            or string.find(texto,"REIVIND") then
                
                pcall(function()
                    gui:Activate()
                end)
                
                pcall(function()
                    gui.MouseButton1Click:Fire()
                end)
            end
        end
    end
end

-------------------------------------------------
-- ATIVAR CLICK DETECTOR
-------------------------------------------------
local function ativarClickDetector(model)
    for _, v in pairs(model:GetDescendants()) do
        if v:IsA("ClickDetector") then
            pcall(function()
                fireclickdetector(v)
            end)
        end
    end
end

-------------------------------------------------
-- ACHAR STAND MAIS PERTO
-------------------------------------------------
local function acharStand(hrp)

    local maisPerto = nil
    local distMin = math.huge

    for _, v in pairs(workspace:GetDescendants()) do
        
        if v:IsA("TextLabel") or v:IsA("TextButton") then
            
            local txt = string.upper(v.Text or "")
            
            if string.find(txt, TEXTO_ALVO) then
                
                local model = v:FindFirstAncestorOfClass("Model")
                
                if model then
                    local part = getPart(model)
                    
                    if part then
                        local dist = (hrp.Position - part.Position).Magnitude
                        
                        if dist < distMin then
                            distMin = dist
                            maisPerto = model
                        end
                    end
                end
            end
        end
    end

    return maisPerto
end

-------------------------------------------------
-- TELEPORTE DEMÔNIO
-------------------------------------------------
local function teleportarDemonio(part, hrp)
    for i = 1, 25 do
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        hrp.AssemblyLinearVelocity = Vector3.zero
        task.wait(0.01)
    end
end

-------------------------------------------------
-- LOOP COM TEMPO LIMITE
-------------------------------------------------
task.spawn(function()

    local inicio = tick()

    while tick() - inicio <= TEMPO_LOOP do
        
        local hrp = getHRP()
        local standModel = acharStand(hrp)

        if standModel then
            
            local part = getPart(standModel)
            
            if part then
                
                teleportarDemonio(part, hrp)
                ativarClickDetector(standModel)
                
                spamE(20)
                spamMouse(20)
                clicarBotao()
                
                spamE(10)
                spamMouse(10)
                
            end
        end

        task.wait(0.2)

    end

    print("🔥 Loop demônio finalizado 🔥")

end)
