--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"

--// SERVICES
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local VirtualInputManager = game:GetService("VirtualInputManager")

--// PEGAR HRP
local function getHRP()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:WaitForChild("HumanoidRootPart")
end

--// PEGAR QUALQUER PART DO MODEL
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

--// APERTAR TECLA E (FORÇADO)
local function pressionarE()
    
    -- Pressiona
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
    task.wait(0.1)
    
    -- Solta
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
    
end

--// ACHAR STAND MAIS PERTO COM UNCLAIMED
local function acharStandMaisPerto(hrp)

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

--// TELEPORTE SUPER FORÇADO
local function teleportar(part, hrp)

    for i = 1, 5 do
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        hrp.AssemblyLinearVelocity = Vector3.new(0,0,0)
        task.wait(0.05)
    end

end

--// LOOP PRINCIPAL
task.spawn(function()

    while true do
        
        local hrp = getHRP()
        local part = acharStandMaisPerto(hrp)

        if part then
            
            -- TELEPORTA
            teleportar(part, hrp)
            
            -- ESPERA UM POUCO PRA CARREGAR
            task.wait(0.4)
            
            -- FORÇA APERTAR E (AUTO CLAIM)
            for i = 1, 3 do
                pressionarE()
                task.wait(0.3)
            end
            
        end

        task.wait(0.5)

    end

end)
