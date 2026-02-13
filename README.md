--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local DISTANCIA_MAX = 200

--// SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

--// ESPERAR PERSONAGEM
local function getChar()
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    return char, hrp
end

--// PROCURAR STAND MAIS PERTO COM "UNCLAIMED"
local function acharStandMaisPerto(hrp)
    local maisPerto = nil
    local menorDist = math.huge

    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("SurfaceGui") then
            
            local texto = ""

            pcall(function()
                texto = string.upper(obj.Text or "")
            end)

            if string.find(texto, TEXTO_ALVO) then
                local model = obj:FindFirstAncestorOfClass("Model")
                if model then
                    local part = model:FindFirstChildWhichIsA("BasePart")
                    if part then
                        local dist = (hrp.Position - part.Position).Magnitude
                        if dist < menorDist then
                            menorDist = dist
                            maisPerto = model
                        end
                    end
                end
            end
        end
    end

    return maisPerto
end

--// TELEPORTAR
local function teleportarParaStand(model, hrp)
    local part = model:FindFirstChildWhichIsA("BasePart")
    if part then
        hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
        return true
    end
    return false
end

--// FORÇAR CLAIM (TODOS MÉTODOS)
local function forcarClaim()
    
    -- ProximityPrompt
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("ProximityPrompt") then
            pcall(function()
                fireproximityprompt(v)
            end)
        end
    end

    -- ClickDetector
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("ClickDetector") then
            pcall(function()
                fireclickdetector(v)
            end)
        end
    end

    -- Simular tecla E
    pcall(function()
        VirtualInputManager:SendKeyEvent(true,"E",false,game)
        task.wait(0.1)
        VirtualInputManager:SendKeyEvent(false,"E",false,game)
    end)

    -- Simular clique mouse
    pcall(function()
        VirtualInputManager:SendMouseButtonEvent(0,0,0,true,game,0)
        task.wait(0.1)
        VirtualInputManager:SendMouseButtonEvent(0,0,0,false,game,0)
    end)

    -- RemoteEvents
    for _, v in pairs(game:GetDescendants()) do
        if v:IsA("RemoteEvent") then
            pcall(function()
                v:FireServer()
            end)
        end
    end

end

--// LOOP PRINCIPAL
task.spawn(function()
    while task.wait(2) do
        local char, hrp = getChar()

        local stand = acharStandMaisPerto(hrp)

        if stand then
            print("Stand encontrado:", stand.Name)

            if teleportarParaStand(stand, hrp) then
                task.wait(1)

                -- Tentar claim várias vezes
                for i = 1,5 do
                    forcarClaim()
                    task.wait(0.5)
                end
            end
        else
            print("Nenhum UNCLAIMED encontrado")
        end
    end
end)
