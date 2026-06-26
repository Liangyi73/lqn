--[[
    ESP Script (lamehaxx v0.01) with ForceField feature ported from Index UI
    Original ESP logic preserved, UI replaced with Linoria Library
    Added Player ForceField BasePart toggle and color picker in VISUALS right side
    Render team moved to first position in ESP group
--]]

-- Load Linoria Library
local repo = 'https://raw.githubusercontent.com/mstudio45/LinoriaLib/main/'
local Library = loadstring(game:HttpGet(repo .. 'Library.lua'))()
local ThemeManager = loadstring(game:HttpGet(repo .. 'addons/ThemeManager.lua'))()
local SaveManager = loadstring(game:HttpGet(repo .. 'addons/SaveManager.lua'))()
local Options = Library.Options
local Toggles = Library.Toggles

Library.ShowToggleFrameInKeybinds = true
Library.ShowCustomCursor = true
Library.NotifySide = "Left"

local Window = Library:CreateWindow({
    Title = 'lamehaxx v0.01',
    Center = true,
    AutoShow = true,
    Resizable = true,
    ShowCustomCursor = true,
    NotifySide = "Left",
    TabPadding = 8,
    MenuFadeTime = 0.2
})

-- Tabs
local Tabs = {
    VISUALS = Window:AddTab('VISUALS'),
    ['UI Settings'] = Window:AddTab('UI Settings'),
}

-- Global cheat state (kept same as original)
local cheats = {
    b_b = false;
    b_f = false;
    b_f_t = 1;
    b_sd = false;
    b_sn = false;
    b_sh = false;
    b_ht = "Bar";
    b_rt = false;
    b_tc = false;
    teamColor = Color3.new(1, 1, 1); -- default white
    enemyColor = Color3.new(1, 0, 0); -- default red
}

-- Player ForceField settings
local playerForceField = false
local playerForceFieldColor = Color3.new(1, 1, 1)

-- ESP folder (as original)
local cheatsf = Instance.new("Folder", game.CoreGui) cheatsf.Name = "cheats"
local espf = Instance.new("Folder", cheatsf) espf.Name = "esp"

-- ESP drawing function (unchanged)
local localplayer = game:GetService("Players").LocalPlayer
local function addEsp(player)
    local bbg = Instance.new("BillboardGui", espf)
    bbg.Name = player.Name
    bbg.AlwaysOnTop = true
    bbg.Size = UDim2.new(4,0,5.4,0)
    bbg.ClipsDescendants = false
    
    local outlines = Instance.new("Frame", bbg)
    outlines.Size = UDim2.new(1,0,1,0)
    outlines.BorderSizePixel = 0
    outlines.BackgroundTransparency = 1
    local left = Instance.new("Frame", outlines)
    left.BorderSizePixel = 0
    left.Size = UDim2.new(0,1,1,0)
    local right = left:Clone()
    right.Parent = outlines
    right.Size = UDim2.new(0,-1,1,0)
    right.Position = UDim2.new(1,0,0,0)
    local up = left:Clone()
    up.Parent = outlines
    up.Size = UDim2.new(1,0,0,1)
    local down = left:Clone()
    down.Parent = outlines
    down.Size = UDim2.new(1,0,0,-1)
    down.Position = UDim2.new(0,0,1,0)
    
    local info = Instance.new("BillboardGui", bbg)
    info.Name = "info"
    info.Size = UDim2.new(3,0,0,54)
    info.StudsOffset = Vector3.new(3.6,-3,0)
    info.AlwaysOnTop = true
    info.ClipsDescendants = false
    local namelabel = Instance.new("TextLabel", info)
    namelabel.Name = "namelabel"
    namelabel.BackgroundTransparency = 1
    namelabel.TextStrokeTransparency = 0
    namelabel.TextXAlignment = Enum.TextXAlignment.Left
    namelabel.Size = UDim2.new(0,100,0,18)
    namelabel.Position = UDim2.new(0,0,0,0)
    namelabel.Text = player.Name
    local distancel = Instance.new("TextLabel", info)
    distancel.Name = "distancelabel"
    distancel.BackgroundTransparency = 1
    distancel.TextStrokeTransparency = 0
    distancel.TextXAlignment = Enum.TextXAlignment.Left
    distancel.Size = UDim2.new(0,100,0,18)
    distancel.Position = UDim2.new(0,0,0,18)
    local healthl = Instance.new("TextLabel", info)
    healthl.Name = "healthlabel"
    healthl.BackgroundTransparency = 1
    healthl.TextStrokeTransparency = 0
    healthl.TextXAlignment = Enum.TextXAlignment.Left
    healthl.Size = UDim2.new(0,100,0,18)
    healthl.Position = UDim2.new(0,0,0,36)
    
    local uill = Instance.new("UIListLayout", info)
    
    local forhealth = Instance.new("BillboardGui", bbg)
    forhealth.Name = "forhealth"
    forhealth.Size = UDim2.new(5,0,6,0)
    forhealth.AlwaysOnTop = true
    forhealth.ClipsDescendants = false
    
    local healthbar = Instance.new("Frame", forhealth)
    healthbar.Name = "healthbar"
    healthbar.BackgroundColor3 = Color3.fromRGB(40,40,40)
    healthbar.BorderColor3 = Color3.fromRGB(0,0,0)
    healthbar.Size = UDim2.new(0.04,0,0.9,0)
    healthbar.Position = UDim2.new(0,0,0.05,0)
    local bar = Instance.new("Frame", healthbar)
    bar.Name = "bar"
    bar.BorderSizePixel = 0
    bar.BackgroundColor3 = Color3.fromRGB(94,255,69)
    bar.AnchorPoint = Vector2.new(0,1)
    bar.Position = UDim2.new(0,0,1,0)
    bar.Size = UDim2.new(1,0,1,0)
    
    local co = coroutine.create(function()
        while wait(0.1) do
            if (player.Character and player.Character:FindFirstChild"HumanoidRootPart") then
                bbg.Adornee = player.Character.HumanoidRootPart
                info.Adornee = player.Character.HumanoidRootPart
                forhealth.Adornee = player.Character.HumanoidRootPart
                
                if (player.Team ~= localplayer.Team) then
                    bbg.Enabled = true
                    info.Enabled = true
                    forhealth.Enabled = true
                end
                if player.Character:FindFirstChild("ForceField") then
                    outlines.BackgroundTransparency = 0.4
                    left.BackgroundTransparency = 0.4
                    right.BackgroundTransparency = 0.4
                    up.BackgroundTransparency = 0.4
                    down.BackgroundTransparency = 0.4
                    healthl.TextTransparency = 0.4
                    healthl.TextStrokeTransparency = 0.8
                    distancel.TextTransparency = 0.4
                    distancel.TextStrokeTransparency = 0.8
                    namelabel.TextTransparency = 0.4
                    namelabel.TextStrokeTransparency = 0.8
                    bar.BackgroundTransparency = 0.4
                    healthbar.BackgroundTransparency = 0.8
                else
                    outlines.BackgroundTransparency = 0
                    left.BackgroundTransparency = 0
                    right.BackgroundTransparency = 0
                    up.BackgroundTransparency = 0
                    down.BackgroundTransparency = 0
                    healthl.TextTransparency = 0
                    healthl.TextStrokeTransparency = 0
                    distancel.TextTransparency = 0
                    distancel.TextStrokeTransparency = 0
                    namelabel.TextTransparency = 0
                    namelabel.TextStrokeTransparency = 0
                    bar.BackgroundTransparency = 0
                    healthbar.BackgroundTransparency = 0
                end
                if cheats.b_b == true then
                    outlines.Visible = true
                else
                    outlines.Visible = false
                end
                if cheats.b_f == true then
                    if player.Character:FindFirstChild("ForceField") then
                        outlines.BackgroundTransparency = 0.9
                    else
                        outlines.BackgroundTransparency = cheats.b_f_t
                    end
                else
                    outlines.BackgroundTransparency = 1
                end
                if cheats.b_sh == true then
                    if (player.Character:FindFirstChild"Humanoid") then
                        healthl.Text = "Health: "..math.floor(player.Character:FindFirstChild"Humanoid".Health)
                        healthbar.bar.Size = UDim2.new(1,0,player.Character:FindFirstChild"Humanoid".Health/player.Character:FindFirstChild"Humanoid".MaxHealth,0)
                    end
                    if cheats.b_ht == "Bar" then
                        healthbar.Visible = true
                        healthl.Visible = false
                    elseif cheats.b_ht == "Text" then
                        healthbar.Visible = false
                        healthl.Visible = true
                    elseif cheats.b_ht == "Both" then
                        healthl.Visible = true
                        healthbar.Visible = true
                    end
                else
                    healthl.Visible = false
                    healthbar.Visible = false
                end
                if cheats.b_sn then
                    namelabel.Visible = true
                else
                    namelabel.Visible = false
                end
                if cheats.b_sd == true then
                    distancel.Visible = true
                    if (localplayer.Character and localplayer.Character:FindFirstChild"HumanoidRootPart") then
                        distancel.Text = "Distance: "..math.floor(0.5+(localplayer.Character:FindFirstChild"HumanoidRootPart".Position - player.Character:FindFirstChild"HumanoidRootPart".Position).magnitude)
                    end
                else
                    distancel.Visible = false
                end
                if cheats.b_rt == true then
                    if (player.Team == localplayer.Team) then
                        bbg.Enabled = true
                        info.Enabled = true
                        forhealth.Enabled = true
                    end
                else
                    if (player.Team == localplayer.Team) then
                        bbg.Enabled = false
                        info.Enabled = false
                        forhealth.Enabled = false
                    end
                end
                if cheats.b_tc == true then
                    outlines.BackgroundColor3 = player.TeamColor.Color
                    left.BackgroundColor3 = player.TeamColor.Color
                    right.BackgroundColor3 = player.TeamColor.Color
                    up.BackgroundColor3 = player.TeamColor.Color
                    down.BackgroundColor3 = player.TeamColor.Color
                    healthl.TextColor3 = player.TeamColor.Color
                    distancel.TextColor3 = player.TeamColor.Color
                    namelabel.TextColor3 = player.TeamColor.Color
                else
                    local colorToUse = cheats.teamColor
                    if player.Team ~= localplayer.Team then
                        colorToUse = cheats.enemyColor
                    end
                    outlines.BackgroundColor3 = colorToUse
                    left.BackgroundColor3 = colorToUse
                    right.BackgroundColor3 = colorToUse
                    up.BackgroundColor3 = colorToUse
                    down.BackgroundColor3 = colorToUse
                    healthl.TextColor3 = colorToUse
                    distancel.TextColor3 = colorToUse
                    namelabel.TextColor3 = colorToUse
                end
            end
            if not (game:GetService"Players":FindFirstChild(player.Name)) then
                print(player.Name.." has left. Clearing esp.")
                espf:FindFirstChild(player.Name):Destroy()
                coroutine.yield()
            end
        end
    end)
    coroutine.resume(co)
end

-- VISUALS Tab UI setup
local visualsTab = Tabs.VISUALS
local groupESP = visualsTab:AddLeftGroupbox('ESP')

-- Render team moved to first position
groupESP:AddToggle('b_rt', {
    Text = 'Render team',
    Default = false,
    Callback = function(v) cheats.b_rt = v end
})

groupESP:AddToggle('b_b', {
    Text = 'Bounding box',
    Default = false,
    Callback = function(v) cheats.b_b = v end
})
groupESP:AddToggle('b_f', {
    Text = 'Fill alpha',
    Default = false,
    Callback = function(v) cheats.b_f = v end
})
groupESP:AddSlider('b_f_t', {
    Text = 'Fill transparency',
    Default = 1,
    Min = 0,
    Max = 1,
    Rounding = 2,
    Suffix = '',
    Callback = function(v) cheats.b_f_t = v end
})
groupESP:AddDivider()
groupESP:AddToggle('b_sd', {
    Text = 'Show distance',
    Default = false,
    Callback = function(v) cheats.b_sd = v end
})
groupESP:AddToggle('b_sn', {
    Text = 'Show name',
    Default = false,
    Callback = function(v) cheats.b_sn = v end
})
groupESP:AddToggle('b_sh', {
    Text = 'Show health',
    Default = false,
    Callback = function(v) cheats.b_sh = v end
})
groupESP:AddDropdown('b_ht', {
    Text = 'Health type',
    Values = {'Text', 'Bar', 'Both'},
    Default = 'Bar',
    Callback = function(v) cheats.b_ht = v end
})
-- Removed the divider that was before b_rt, now it's first

-- Player ForceField section (right side)
local groupPlayer = visualsTab:AddRightGroupbox('Player')
groupPlayer:AddToggle('ForceFieldEnabled', {
    Text = 'ForceField BasePart',
    Default = false,
    Callback = function(v)
        playerForceField = v
    end
})
groupPlayer:AddLabel('Color'):AddColorPicker('ForceFieldColor', {
    Default = playerForceFieldColor,
    Callback = function(c)
        playerForceFieldColor = c
    end
})

-- UI Settings tab (unchanged)
local MenuGroup = Tabs['UI Settings']:AddLeftGroupbox('Menu')
MenuGroup:AddToggle("KeybindMenuOpen", { Default = Library.KeybindFrame.Visible, Text = "Open Keybind Menu", Callback = function(value) Library.KeybindFrame.Visible = value end})
MenuGroup:AddToggle("ShowCustomCursor", {Text = "Custom Cursor", Default = true, Callback = function(Value) Library.ShowCustomCursor = Value end})
MenuGroup:AddDivider()
MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", { Default = "RightShift", NoUI = true, Text = "Menu keybind" })
MenuGroup:AddButton("Unload", function() Library:Unload() end)

Library.ToggleKeybind = Options.MenuKeybind

-- Watermark
Library:SetWatermarkVisibility(true)
Library:SetWatermark('lamehaxx v0.01')

-- Unload cleanup
Library:OnUnload(function()
    print('Unloaded!')
    Library.Unloaded = true
end)

-- Theme & Save managers
ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({ 'MenuKeybind' })
ThemeManager:SetFolder('MyScriptHub')
SaveManager:SetFolder('MyScriptHub/specific-game')
SaveManager:BuildConfigSection(Tabs['UI Settings'])
ThemeManager:ApplyToTab(Tabs['UI Settings'])
SaveManager:LoadAutoloadConfig()

-- ForceField function (applied continuously)
local function applyForceField()
    local character = localplayer.Character
    if not character then return end
    for _, part in ipairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            if playerForceField then
                part.Material = Enum.Material.ForceField
                part.Color = playerForceFieldColor
            end
        end
    end
end

-- Connect force field application to Heartbeat
game:GetService("RunService").Heartbeat:Connect(applyForceField)

-- Main functionality (original)
do
    wait(2)
    -- Initial player addition
    for _,v in pairs(game:GetService("Players"):GetChildren()) do
        if not (v.Name == localplayer.Name) then
            if not (espf:FindFirstChild(v.Name)) then
                addEsp(v)
            end
        end
    end

    -- GUI toggle with KeypadOne
    game:GetService("UserInputService").InputBegan:connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode.KeypadOne then
            if not gameProcessed then
                Window.ScreenGui.Enabled = not Window.ScreenGui.Enabled
            end
        end
    end)

    -- Auto-update for new players every 10 seconds
    while wait(10) do
        for _,v in pairs(game:GetService("Players"):GetChildren()) do
            if not (v.Name == localplayer.Name) then
                if not (espf:FindFirstChild(v.Name)) then
                    addEsp(v)
                end
            end
        end
    end
end
