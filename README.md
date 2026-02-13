--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"

--// SERVICES
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local VirtualInputManager = game:GetService("VirtualInputManager")

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
        task.wait(0.05)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
        task.wait(0.05)
    end
end

-------------------------------------------------
-- CLICAR BOTÃO REIVINDICAR (SE EXISTIR)
-------------------------------------------------
local function clicarBotaoReivindicar()

    for _, gui in pairs(player.PlayerGui:GetDescendants()) do
        
        if gui:IsA("TextButton") or gui:IsA("ImageButton") then
            
            local nome = string.upper(gui.Name or "")
            local texto = string.upper(gui.Text or "")
            
            if string.find(nome, "REIVIND") 
            or string.find(texto, "REIVIND")
            or string.find(texto, "CLAIM") then
                
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
                            maisPerto = part
                        end
                    end
                end
            end
        end
    end

    return maisPerto
end

-------------------------------------------------
-- TELEPORTE INSANO
-------------------------------------------------
local function teleportarInsano(part, hrp)

    for i = 1, 10 do
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        hrp.AssemblyLinearVelocity = Vector3.zero
        task.wait(0.03)
    end

end

-------------------------------------------------
-- LOOP INSANO
-------------------------------------------------
task.spawn(function()

    while true do
        
        local hrp = getHRP()
        local part = acharStand(hrp)

        if part then
            
            -- TELEPORTE
            teleportarInsano(part, hrp)
            
            -- ESPERA
            task.wait(0.2)
            
            -- SPAM TECLA E
            spamE(10)
            
            -- TENTA CLICAR BOTÃO
            clicarBotaoReivindicar()
            
            -- MAIS SPAM E
            spamE(5)
            
        end

        task.wait(0.3)

    end

end)
