--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local TEMPO_LOOP = 999999 -- loop infinito

--// SERVICES
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------------------------
-- BLUR
-------------------------------------------------
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

-------------------------------------------------
-- LANGUAGE
-------------------------------------------------
local LANG = "PT"

local TEXT = {
	PT = {
		TITLE = "MENU",
		CLOSE = "X",
		MIN = "-",
		CONFIRM = "Tem certeza que quer eliminar o menu?",
		YES = "SIM",
		NO = "NÃO"
	},

	EN = {
		TITLE = "MENU",
		CLOSE = "X",
		MIN = "-",
		CONFIRM = "Are you sure you want to delete the menu?",
		YES = "YES",
		NO = "NO"
	}
}

-------------------------------------------------
-- HRP
-------------------------------------------------
local function getHRP()
	local char = player.Character or player.CharacterAdded:Wait()
	return char:WaitForChild("HumanoidRootPart")
end

-------------------------------------------------
-- GET PART MODEL
-------------------------------------------------
local function getPart(model)
	if model.PrimaryPart then return model.PrimaryPart end
	for _,v in pairs(model:GetDescendants()) do
		if v:IsA("BasePart") then return v end
	end
end

-------------------------------------------------
-- INPUT SPAM
-------------------------------------------------
local function spamE(q)
	for i=1,q do
		VirtualInputManager:SendKeyEvent(true,Enum.KeyCode.E,false,game)
		task.wait(0.02)
		VirtualInputManager:SendKeyEvent(false,Enum.KeyCode.E,false,game)
	end
end

local function spamMouse(q)
	for i=1,q do
		VirtualInputManager:SendMouseButtonEvent(0,0,0,true,game,0)
		task.wait(0.02)
		VirtualInputManager:SendMouseButtonEvent(0,0,0,false,game,0)
	end
end

-------------------------------------------------
-- CLICK CLAIM BUTTON GUI
-------------------------------------------------
local function clicarBotao()
	for _,gui in pairs(player.PlayerGui:GetDescendants()) do
		if gui:IsA("TextButton") or gui:IsA("ImageButton") then
			local nome = string.upper(gui.Name or "")
			local texto = string.upper(gui.Text or "")
			if string.find(nome,"CLAIM") or string.find(texto,"CLAIM") then
				pcall(function() gui:Activate() end)
				pcall(function() gui.MouseButton1Click:Fire() end)
			end
		end
	end
end

-------------------------------------------------
-- CLICK DETECTOR
-------------------------------------------------
local function ativarClickDetector(model)
	for _,v in pairs(model:GetDescendants()) do
		if v:IsA("ClickDetector") then
			pcall(function() fireclickdetector(v) end)
		end
	end
end

-------------------------------------------------
-- FIND NEAREST STAND
-------------------------------------------------
local function acharStand(hrp)
	local near=nil
	local dist=math.huge

	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("TextLabel") or v:IsA("TextButton") then
			local txt = string.upper(v.Text or "")
			if string.find(txt,TEXTO_ALVO) then
				local model = v:FindFirstAncestorOfClass("Model")
				if model then
					local part = getPart(model)
					if part then
						local d = (hrp.Position-part.Position).Magnitude
						if d<dist then
							dist=d
							near=model
						end
					end
				end
			end
		end
	end

	return near
end

-------------------------------------------------
-- TELEPORT
-------------------------------------------------
local function teleportar(part,hrp)
	for i=1,25 do
		hrp.CFrame = part.CFrame + Vector3.new(0,3,0)
		hrp.AssemblyLinearVelocity = Vector3.zero
		task.wait(0.01)
	end
end

-------------------------------------------------
-- LOOP AUTO
-------------------------------------------------
task.spawn(function()
	while true do
		local hrp=getHRP()
		local stand=acharStand(hrp)
		if stand then
			local part=getPart(stand)
			if part then
				teleportar(part,hrp)
				ativarClickDetector(stand)
				spamE(20)
				spamMouse(20)
				clicarBotao()
			end
		end
		task.wait(0.3)
	end
end)

-------------------------------------------------
-- LANGUAGE GUI
-------------------------------------------------
local langGui = Instance.new("ScreenGui", PlayerGui)
langGui.ResetOnSpawn=false

local frame = Instance.new("Frame", langGui)
frame.Size=UDim2.new(0,400,0,260)
frame.Position=UDim2.new(0.5,-200,0.5,-130)
frame.BackgroundColor3=Color3.fromRGB(0,0,0)
frame.BackgroundTransparency=0.25

local stroke=Instance.new("UIStroke",frame)
stroke.Color=Color3.fromRGB(170,0,255)
stroke.Thickness=3

local title=Instance.new("TextLabel", frame)
title.Size=UDim2.new(1,0,0,40)
title.BackgroundTransparency=1
title.Text="USA / BR"
title.TextScaled=true
title.TextColor3=Color3.new(1,1,1)

local function flag(name,emoji,pos,texto)
	local b=Instance.new("TextButton", frame)
	b.Name=name
	b.Size=UDim2.new(0,140,0,90)
	b.Position=UDim2.new(0,pos,0,70)
	b.BackgroundTransparency=1
	b.Text=emoji
	b.TextScaled=true

	local t=Instance.new("TextLabel", frame)
	t.Size=UDim2.new(0,140,0,30)
	t.Position=UDim2.new(0,pos,0,170)
	t.BackgroundTransparency=1
	t.Text=texto
	t.TextScaled=true
	t.TextColor3=Color3.new(1,1,1)

	return b
end

local usa=flag("USA","🇺🇸",40,"ENGLISH")
local br=flag("BR","🇧🇷",220,"PORTUGUÊS")

-------------------------------------------------
-- MAIN GUI FUNCTION
-------------------------------------------------
local function createMainGUI()

	local gui = Instance.new("ScreenGui", PlayerGui)

	local open = Instance.new("TextButton", gui)
	open.Size = UDim2.new(0,55,0,55)
	open.Position = UDim2.new(0,10,0,20)
	open.BackgroundColor3 = Color3.fromRGB(0,0,0)
	open.Text = "⚠️"
	open.TextScaled = true
	open.TextColor3 = Color3.new(1,1,1)

	Instance.new("UICorner", open).CornerRadius = UDim.new(0,12)
	Instance.new("UIStroke", open).Color = Color3.fromRGB(170,0,255)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.new(0,660,0,460)
	main.Position = UDim2.new(0.5,-330,0.5,-230)
	main.BackgroundColor3 = Color3.fromRGB(0,0,0)
	main.BackgroundTransparency = 0.3
	main.Visible = false

	Instance.new("UICorner", main).CornerRadius = UDim.new(0,10)
	Instance.new("UIStroke", main).Color = Color3.fromRGB(170,0,255)

	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,50)
	title.BackgroundTransparency = 1
	title.Text = TEXT[LANG].TITLE
	title.TextScaled = true
	title.TextColor3 = Color3.new(1,1,1)

	local close = Instance.new("TextButton", main)
	close.Size = UDim2.new(0,50,0,50)
	close.Position = UDim2.new(1,-60,0,5)
	close.Text = TEXT[LANG].CLOSE
	close.BackgroundTransparency = 1
	close.TextScaled = true

	local min = Instance.new("TextButton", main)
	min.Size = UDim2.new(0,50,0,50)
	min.Position = UDim2.new(1,-120,0,5)
	min.Text = TEXT[LANG].MIN
	min.BackgroundTransparency = 1
	min.TextScaled = true

	local confirm = Instance.new("Frame", gui)
	confirm.Size = UDim2.new(0,400,0,200)
	confirm.Position = UDim2.new(0.5,-200,0.5,-100)
	confirm.BackgroundColor3 = Color3.fromRGB(0,0,0)
	confirm.Visible = false

	local txt = Instance.new("TextLabel", confirm)
	txt.Size = UDim2.new(1,0,0.5,0)
	txt.BackgroundTransparency = 1
	txt.Text = TEXT[LANG].CONFIRM
	txt.TextScaled = true
	txt.TextColor3 = Color3.new(1,1,1)

	local yes = Instance.new("TextButton", confirm)
	yes.Size = UDim2.new(0.4,0,0.3,0)
	yes.Position = UDim2.new(0.1,0,0.6,0)
	yes.Text = "✅ "..TEXT[LANG].YES

	local no = Instance.new("TextButton", confirm)
	no.Size = UDim2.new(0.4,0,0.3,0)
	no.Position = UDim2.new(0.5,0,0.6,0)
	no.Text = "❌ "..TEXT[LANG].NO

	open.MouseButton1Click:Connect(function()
		main.Visible = not main.Visible
	end)

	min.MouseButton1Click:Connect(function()
		main.Visible = false
	end)

	close.MouseButton1Click:Connect(function()
		confirm.Visible = true
		blur.Size = 20
	end)

	no.MouseButton1Click:Connect(function()
		confirm.Visible = false
		blur.Size = 0
	end)

	yes.MouseButton1Click:Connect(function()
		gui:Destroy()
		blur.Size = 0
	end)

end

-------------------------------------------------
-- LANGUAGE EVENTS
-------------------------------------------------
usa.MouseButton1Click:Connect(function()
	LANG="EN"
	langGui:Destroy()
	createMainGUI()
end)

br.MouseButton1Click:Connect(function()
	LANG="PT"
	langGui:Destroy()
	createMainGUI()
end)
