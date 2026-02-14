--// CONFIG
local TEXTO_ALVO = "UNCLAIMED"
local TEMPO_LOOP = 1

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
-- AUTO LOOP
-------------------------------------------------
task.spawn(function()
	while true do
		local hrp=getHRP()
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

Instance.new("UIStroke",frame).Color=Color3.fromRGB(170,0,255)

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

	-------------------------------------------------
	-- NAV BAR
	-------------------------------------------------

	local nav=Instance.new("Frame", main)
	nav.Size=UDim2.new(1,0,0,50)
	nav.Position=UDim2.new(0,0,0,50)
	nav.BackgroundTransparency=1

	local pages=Instance.new("Frame", main)
	pages.Size=UDim2.new(1,0,1,-100)
	pages.Position=UDim2.new(0,0,0,100)
	pages.BackgroundTransparency=1

	local auto=Instance.new("Frame",pages)
	auto.Size=UDim2.new(1,0,1,0)
	auto.BackgroundTransparency=1

	local playerP=auto:Clone()
	playerP.Parent=pages
	playerP.Visible=false

	local settings=auto:Clone()
	settings.Parent=pages
	settings.Visible=false

	local function switch(p)
		auto.Visible=false
		playerP.Visible=false
		settings.Visible=false
		p.Visible=true
	end

	local btns={}

	local function navBtn(text,x,page)
		local hold=Instance.new("Frame",nav)
		hold.Size=UDim2.new(0,150,0,45)
		hold.Position=UDim2.new(0,x,0,5)
		hold.BackgroundTransparency=1

		local b=Instance.new("TextButton",hold)
		b.Size=UDim2.new(1,0,1,0)
		b.BackgroundTransparency=1
		b.Text=text
		b.TextScaled=true
		b.Font=Enum.Font.GothamBold
		b.TextColor3=Color3.fromRGB(170,170,170)

		local line=Instance.new("Frame",hold)
		line.Size=UDim2.new(1,0,0,3)
		line.Position=UDim2.new(0,0,1,-3)
		line.BackgroundColor3=Color3.fromRGB(170,0,255)
		line.Visible=false

		table.insert(btns,{b=b,l=line,p=page})

		b.MouseButton1Click:Connect(function()
			for _,v in pairs(btns) do
				v.b.TextColor3=Color3.fromRGB(170,170,170)
				v.l.Visible=false
			end
			b.TextColor3=Color3.new(1,1,1)
			line.Visible=true
			switch(page)
		end)
	end

	navBtn("AUTO",20,auto)
	navBtn("PLAYER",190,playerP)
	navBtn("SETTINGS",360,settings)

	btns[1].b.TextColor3=Color3.new(1,1,1)
	btns[1].l.Visible=true
	switch(auto)

	-------------------------------------------------
	-- OPEN BUTTON EVENT
	-------------------------------------------------

	open.MouseButton1Click:Connect(function()
		main.Visible = not main.Visible
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
