--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"

--// SERVICES
local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

--// PEGAR PERSONAGEM
local function getHRP()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:WaitForChild("HumanoidRootPart")
end

--// ACHAR STAND MAIS PERTO
local function acharStand(hrp)
    local alvo = nil
    local distMin = math.huge

    for _, obj in pairs(workspace:GetDescendants()) do
        
        local texto = nil
        
        if obj:IsA("TextLabel") or obj:IsA("TextButton") then
            texto = obj.Text
        end
        
        if texto then
            texto = string.upper(texto)
            
            if string.find(texto, TEXTO_ALVO) then
                local model = obj:FindFirstAncestorOfClass("Model")
                
                if model then
                    local part = model:FindFirstChildWhichIsA("BasePart")
                    
                    if part then
                        local dist = (hrp.Position - part.Position).Magnitude
                        
                        if dist < distMin then
                            distMin = dist
                            alvo = model
                        end
                    end
                end
            end
        end
    end

    return alvo
end

--// TELEPORTAR FORÇADO
local function teleportar(model, hrp)
    local part = model:FindFirstChildWhichIsA("BasePart")
    
    if part then
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        hrp.Velocity = Vector3.new(0,0,0)
    end
end

--// SPAM PROXIMITY
task.spawn(function()
    while true do
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("ProximityPrompt") then
                pcall(function()
                    fireproximityprompt(v)
                end)
            end
        end
        task.wait(0.05)
    end
end)

--// SPAM CLICK DETECTOR
task.spawn(function()
    while true do
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("ClickDetector") then
                pcall(function()
                    fireclickdetector(v)
                end)
            end
        end
        task.wait(0.05)
    end
end)

--// SPAM TECLA E
task.spawn(function()
    while true do
        pcall(function()
            VirtualInputManager:SendKeyEvent(true,"E",false,game)
            VirtualInputManager:SendKeyEvent(false,"E",false,game)
        end)
        task.wait(0.05)
    end
end)

--// SPAM CLIQUE MOUSE
task.spawn(function()
    while true do
        pcall(function()
            VirtualInputManager:SendMouseButtonEvent(0,0,0,true,game,0)
            VirtualInputManager:SendMouseButtonEvent(0,0,0,false,game,0)
        end)
        task.wait(0.05)
    end
end)

--// SPAM REMOTES
task.spawn(function()
    while true do
        for _, v in pairs(game:GetDescendants()) do
            if v:IsA("RemoteEvent") then
                pcall(function()
                    v:FireServer()
                end)
            end
        end
        task.wait(0.2)
    end
end)

--// LOOP TELEPORTE ULTRA RÁPIDO
task.spawn(function()
    while true do
        local hrp = getHRP()
        local stand = acharStand(hrp)

        if stand then
            teleportar(stand, hrp)
        end

        task.wait(0.1)
    end
end)
