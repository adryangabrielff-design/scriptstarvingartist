--// SERVIÇOS
local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")
local player = Players.LocalPlayer

--// CONFIG
local TELEPORT_DELAY = 2
local CLAIM_DELAY = 1

--// FUNÇÃO: PEGAR STANDS
local function getStands()
    local standsFolder = workspace:FindFirstChild("Stands") or workspace
    local stands = {}

    for _, v in pairs(standsFolder:GetChildren()) do
        if v:IsA("Model") then
            table.insert(stands, v)
        end
    end

    return stands
end

--// FUNÇÃO: TELEPORTAR FORÇADO
local function forceTeleport()
    local character = player.Character or player.CharacterAdded:Wait()
    local hrp = character:WaitForChild("HumanoidRootPart")

    local stands = getStands()
    if #stands == 0 then return end

    local stand = stands[math.random(1, #stands)]

    -- Procura peça para teleportar
    local part = stand:FindFirstChildWhichIsA("BasePart", true)

    if part then
        hrp.CFrame = part.CFrame + Vector3.new(0,5,0)
    end
end

--// FUNÇÃO: CLICK DETECTOR FORÇADO
local function forceClickDetector()
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("ClickDetector") then
            fireclickdetector(v)
            return true
        end
    end
    return false
end

--// FUNÇÃO: CLICAR GUI REIVINDICAR
local function clickClaimGUI()
    local pg = player:FindFirstChild("PlayerGui")
    if not pg then return false end

    for _, gui in pairs(pg:GetDescendants()) do
        if gui:IsA("TextButton") or gui:IsA("ImageButton") then
            local txt = string.lower(gui.Text or "")
            if string.find(txt,"reivind") then
                gui:Activate()
                gui.MouseButton1Click:Fire()
                return true
            end
        end
    end

    return false
end

--// FUNÇÃO: SIMULAR TECLA E
local function pressE()
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
    task.wait(0.1)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
end

--// FUNÇÃO: AUTO CLAIM MAX FORCE
local function autoClaim()
    task.wait(CLAIM_DELAY)

    -- 1 tentativa GUI
    if clickClaimGUI() then return end

    -- 2 tentativa ClickDetector
    if forceClickDetector() then return end

    -- 3 tentativa tecla E
    pressE()
end

--// LOOP PRINCIPAL
while true do
    pcall(function()
        forceTeleport()
        task.wait(1)
        autoClaim()
    end)

    task.wait(TELEPORT_DELAY)
end
