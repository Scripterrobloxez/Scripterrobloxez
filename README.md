-- im doors ðŸ¤“
-- cv is two cool loking letters no real meaning
-- 435345242533452325423454235gfdsdfgdsgfgsdf

--[[
    todo:
    ignore this
]]

warn'hello'
repeat task.wait() until game:IsLoaded()
warn'hola'

local TextChatService = game:GetService('TextChatService')

TextChatService.OnIncomingMessage = function(Message)
    local Properties = Instance.new('TextChatMessageProperties')

    if Message.TextSource.UserId == game.Players.LocalPlayer.UserId then
        task.spawn(function()
            Properties.PrefixText = "<font color='rgb(255, 0, 144)'>[best scripter] </font>" .. Message.PrefixText
        end)
    end

    return Properties
end

local notificationSound = Instance.new'Sound'
notificationSound.SoundId = 'rbxassetid://6897623656'
notificationSound.Volume = 1
notificationSound.Parent = game.Players.LocalPlayer.PlayerGui

local warningSound = Instance.new'Sound' 
warningSound.SoundId = 'rbxassetid://1570162306'
warningSound.Volume = 1
warningSound.Parent = game.Players.LocalPlayer.PlayerGui

local players = game:GetService'Players'
local replicatedStorage = game:GetService'ReplicatedStorage'
local camera = workspace.CurrentCamera
local runService = game:GetService'RunService'
local UserInputService = game:GetService('UserInputService')
local lighting = game.Lighting
local gameData = replicatedStorage.GameData
local remoteFolder = replicatedStorage.RemotesFolder
local latestRoom = gameData.LatestRoom
local floor = gameData.Floor

local player = players.LocalPlayer
local playerGUI = player.PlayerGui
local mainGUI = playerGUI.MainUI
local mainGame = mainGUI.Initiator.Main_Game
local gameIndex = getrawmetatable(game).__index
local hints = playerGUI.PermUI.Hints
local remoteListener = mainGame:FindFirstChild'RemoteListener'

warn(gameIndex(game, 'PlaceId'))

local character
character = player.Character
function eyesSpawned()
    local eyes; do
        eyes = workspace:FindFirstChild('BackdoorLookman') or workspace:FindFirstChild('Eyes')
    end

    return eyes ~= nil
end

local rep = 'https://raw.githubusercontent.com/violin-suzutsuki/LinoriaLib/main/'

local lib =   loadstring(game:HttpGet(rep.. 'Library.lua'))()
local save =  loadstring(game:HttpGet(rep.. 'addons/SaveManager.lua'))()
local theme = loadstring(game:HttpGet(rep.. 'addons/ThemeManager.lua'))()

local Options = getgenv().Options

local window = lib:CreateWindow({
    Title = 'catlova x karlol | ' .. player.Name,
    Center = true,
    AutoShow = true,
    TabPadding = 8,
    MenuFadeTime = 0.2
})

-- settings up stuff

lib.KeybindFrame.Visible = true

local flags = {
    speed = true,
    speedbypass = false,
    noclip = false,
    ncpbypass = false,
    clipPrompts = true,
    auraPrompts = true,
    reachPrompts = true,
    fly = false,
    esp = true,
    espRainbow = false,
    tracers = false,
    fov = true,
    fb = true,
    betterEnvironment = false,
    padLock = false,
    thirdperson = false,
    doorreach = true,
    voiddisabler = true
}

local AntiEntity = {
    Screech =  true,
    Figure = true,
    Seek = true,
    A90 = true,
    Snare = true,
    Eyes = true,
    Obstruction = true
}

local OldScreechModule = remoteListener.Modules.Screech
local OldA90Module = remoteListener.Modules.A90

local values = {
    speedV = 30,
    fieldOfView = 120,
    speedbypassDelay = .2
}

local speedMethod = 'Boost'

local espColors = {
    [1] = Color3.fromRGB(0, 255, 0),
    [2] = Color3.fromRGB(0, 255, 255),
    [3] = Color3.fromRGB(255, 0, 0)
}

function notify(Text, Duration, Type, Duration)
    lib:Notify(Text, Duration or (Type == 'Entity' and 10 or 5))
    if Type == 'Entity' then warningSound:Play() else notificationSound:Play() end
end

function firep(prompt)
    assert(prompt:IsA'ProximityPrompt', 'not a prompt fucking retard')
    
    prompt.Enabled = true
    if prompt.HoldDuration == 0 then
        fireproximityprompt(prompt, 1)
    else
        task.spawn(function()
            prompt:InputHoldBegin()
            prompt.PromptButtonHoldEnded:Wait()
            prompt:InputHoldEnd()
        end)
    end
end

function getPadlockCode(paper)
    if paper and paper:FindFirstChild("UI") then
        local code = {}

        for i, v in paper.UI:GetChildren() do
            if tonumber(v.Name) then
                code[v.ImageRectOffset.X] = {tonumber(v.Name), '_'}
            end
        end
        
        for i, v in hints:GetChildren() do
            if v.Name == 'Icon' then
                if code[v.ImageRectOffset.X] then
                    code[v.ImageRectOffset.X][2] = v.TextLabel.Text
                end
            end
        end
        local final = {}
        
        for i, v in code do
            final[v[1]] = v[2]
        end
        
        local finalcode = ''
        
        for i, v in final do
            finalcode ..= v
        end
        
        warn(finalcode)

        return finalcode
    end

    return '____'
end

function espAttempt(object, typea, text)
    if not flags.esp then return end
    assert(typea == ('item') or typea == ('roomAsset') or typea == ('entity'), 'type was not provided: '..text)

    local room = latestRoom.Value
    local part; do
        local p, e = pcall(function()
            return object.PrimaryPart
        end)
        if p then
            part = object.PrimaryPart
        else
            part = object
        end
    end

    if text == 'Figure' then
        part = object
    end

    local color; do
        if typea == 'item' then
            notify('ITEM | ' .. text .. ' spawned')
            color = espColors[1]
        elseif typea == 'roomAsset' then
            color = espColors[2]
        elseif typea == 'entity' then
            notify('ENTITY | ' .. text .. ' has spawned', 10, 'Entity')
            color = espColors[3]
        else
            color = Color3.fromRGB(124, 124, 124)
        end
    end
    local highlight = Instance.new'Highlight'; do
        highlight.Parent = object
        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        highlight.FillColor = color
        local yess = highlight.OutlineTransparency
        highlight.FillTransparency = 1
        highlight.OutlineTransparency =  1
        game:GetService('TweenService'):Create(highlight, TweenInfo.new(1), {
            FillTransparency = 0.75,
            OutlineTransparency = yess
        }):Play()
        highlight.OutlineColor = color
    end

    local billboard = Instance.new'BillboardGui'; do
        billboard.Adornee = part
        billboard.Parent = part
        billboard.Name = 'zzzzz :yawn:'
        if typea == 'roomAsset' or typea == 'entity' then
            billboard.Size = UDim2.new(8, 0, 2, 0)
        else
            billboard.Size = UDim2.new(3, 0, 0.5, 0)
        end
    end
    

    local label = Instance.new'TextLabel'; do
        label.Size = UDim2.new(1, 0, 1, 0)
        label.FontFace = Font.new('rbxassetid://12187362578')
        label.TextColor3 = color
        label.TextScaled = true
        label.BackgroundTransparency = 1
        label.TextStrokeColor3 = Color3.new()
        label.TextStrokeTransparency = 0
        label.Parent = billboard

        task.spawn(function()
            repeat
                label.Text = string.format('%s\n[ %s ]', (text or part.Name), math.floor((camera.CFrame.Position - part:GetPivot().Position).Magnitude))
                runService.RenderStepped:Wait()
            until label.Parent ~= billboard
        end)
    end

    local tracer = Drawing.new('Line'); do
        tracer.From = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
        tracer.Color = color
        task.spawn(function()
            repeat
                if flags.tracers and part.Parent ~= nil then
                    local vector, onscreen = camera:WorldToViewportPoint(part.Position)
                    tracer.To = Vector2.new(vector.X, vector.Y)
                    tracer.Visible = onscreen
                else
                    tracer.Visible = false
                end
                runService.RenderStepped:Wait()
            until label.Parent ~= billboard
        end)
    end

    billboard.AlwaysOnTop = true

    task.spawn(function()
        repeat
            if flags.esp then
                highlight.Enabled = true
                billboard.Enabled = true
                if flags.espRainbow then
                    tracer.Color = Color3.fromHSV(tick() % 10 / 10, 1, 1)
                    label.TextColor3 = Color3.fromHSV(tick() % 10 / 10, 1, 1)
                    highlight.FillColor = Color3.fromHSV(tick() % 10 / 10, 1, 1)
                    highlight.OutlineColor = Color3.fromHSV(tick() % 10 / 10, 1, 1)
                else
                    tracer.Color = color
                    label.TextColor3 = color
                    highlight.FillColor = color
                    highlight.OutlineColor = Color3.new(1, 1, 1)
                end
            else
                highlight.Enabled = false
                billboard.Enabled = false
            end
            runService.RenderStepped:Wait()
        until billboard.Parent ~= part
    end)

    if typea ~= 'entity' or text == 'Snare' then
        latestRoom:GetPropertyChangedSignal('Value'):Connect(function()
            task.spawn(function()
                game:GetService('TweenService'):Create(highlight, TweenInfo.new(1), {
                    FillTransparency = 1,
                    OutlineTransparency = 1
                }):Play()
                task.wait(1)
                highlight:Destroy()
                billboard:Destroy()
                label:Destroy()
            end)
        end)
    end
end
local general, exploits, visual = window:AddTab'General', window:AddTab'Exploits', window:AddTab'Visuals'
local group = {
    left = general:AddLeftGroupbox'Speed',
    room = general:AddRightGroupbox'Room',
    entity = exploits:AddRightGroupbox'Anti-Entity',
    right = visual:AddRightGroupbox'Camera',
    esp = visual:AddLeftGroupbox'ESP',
    env = visual:AddRightGroupbox'Environment',
    left2 = general:AddRightGroupbox'Noclip',
    right2 = exploits:AddLeftGroupbox'Bypasses',
    prompts = exploits:AddLeftGroupbox'Prompts'
}
-- idk
group.entity:AddToggle('Screech', {
    Text = 'Anti-Screech',
    Default = true,
    Tooltip = 'Screech no more',

    Callback = function(v)
        AntiEntity.Screech = v
    end
})

group.entity:AddToggle('Eyes', {
    Text = 'Anti-Eyes',
    Default = true,
    Tooltip = 'Eyes no more',

    Callback = function(v)
        AntiEntity.Eyes = v
    end
})

group.entity:AddToggle('A90', {
    Text = 'Anti-A90',
    Default = true,
    Tooltip = 'A90 no more',

    Callback = function(v)
        AntiEntity.A90 = v
    end
})

group.entity:AddToggle('Obstruction', {
    Text = 'Anti-Obstruction',
    Default = true,
    Tooltip = 'Obstruction no more',

    Callback = function(v)
        AntiEntity.Obstruction = v
    end
})

group.entity:AddToggle('Seek', {
    Text = 'Anti-Seek',
    Default = true,
    Tooltip = 'Seek no more',

    Callback = function(v)
        AntiEntity.Seek = v
    end
})

group.entity:AddToggle('Figure', {
    Text = 'Anti-Figure',
    Default = true,
    Tooltip = 'Figure no more',

    Callback = function(v)
        AntiEntity.Figure = v
    end
})

group.entity:AddToggle('Snares', {
    Text = 'Anti-Snares',
    Default = true,
    Tooltip = 'Snares no more',

    Callback = function(v)
        AntiEntity.Snare = v
    end
})
local Connection
group.right2:AddToggle('cv432423234342342', {
    Text = 'Position spoof',
    Default = false,
    Tooltip = 'Works as a noclip bypasser, godmode, and invisibility (This will automatically disable your collision.)',
    Callback = function(v)
        if v then
            local oldcframe = game.Players.LocalPlayer.Character:GetPivot()
            character.Collision.Weld.C0 = CFrame.new(0, 12.5, 0) * CFrame.new(0, 0, math.rad(90))
            local OldTick = tick()
            notify('SPOOF | Enabling spoofer (can\'t move rn buddy)')
            task.spawn(function()
                repeat
                    character:PivotTo(oldcframe)
                    task.wait()
                until (tick() - OldTick) >= 1
                notify('SPOOF | Done!')
            end)
            Connection = RunService.RenderStepped:Connect(function()
                character.Collision.CanCollide = false
            end)
        else
            local oldcframe = game.Players.LocalPlayer.Character:GetPivot()
            character.Collision.Weld.C0 = CFrame.new() * CFrame.new(0, 0, math.rad(90))
            local OldTick = tick()
            notify('SPOOF | Disabling spoofer (can\'t move rn buddy)')
            task.spawn(function()
                repeat
                    character:PivotTo(oldcframe)
                    task.wait()
                until (tick() - OldTick) >= 1
                notify('SPOOF | Done!')
            end)

            Connection:Disconnect()
        end
    end
})
local OldVoid = workspace.FallenPartsDestroyHeight
group.right2:AddToggle('cv90650934609', {
    Text = 'Partial void disabler',
    Default = true,
    Tooltip = 'Makes it less likely you will die to the void (Glitch will still activate.)',

    Callback = function(v)
        flags.voiddisabler = v
    end
})
group.room:AddToggle('cv131313m', {
    Text = 'Padlock',
    Default = true,
    Tooltip = 'Automatically does padlock code',
    Callback = function(v)
        flags.padLock = v
    end
})
group.room:AddToggle('cv131313m2233', {
    Text = 'Door reach',
    Default = true,
    Tooltip = 'Automatically opens door for you',
    Callback = function(v)
        flags.doorreach = v
    end
})
group.right:AddLabel('Third person'):AddKeyPicker('cv6mmm', {
    Default = 'V',
    NoUI = false,
    Text = 'Third person',
    Callback = function(v)
        flags.thirdperson = v
        mainGame.Camera.Enabled = not v
    end
})
group.env:AddToggle('environmentFB', {
    Text = 'FullBright',
    Default = true,
    Tooltip = 'Turns the room bright',
    Callback = function(v)
        flags.fb = v
    end
})
group.env:AddToggle('Envr', {
    Text = 'Better Environment',
    Default = false,
    Tooltip = 'Gives better lighting',
    Callback = function(v)
        flags.betterEnvironment = v
        if flags.betterEnvironment then
            task.spawn(function()
                repeat
                    lighting.OutdoorAmbient = Color3.fromRGB()
                    task.wait()
                until not flags.betterEnvironment
            end)
        else
            lighting.Ambient = workspace.CurrentRooms[player:GetAttribute("CurrentRoom")]:GetAttribute("Ambient")
        end
    end
})
-- speeeeedd
group.left:AddToggle('cv1', {
    Text = 'Speed',
    Default = flags.speed,
    Tooltip = 'makes u fast',
    Callback = function(v)
        flags.speed = v
    end
})

group.left:AddSlider('cv2', {
    Text = 'Studs',
    Default = 30,
    Min = 0,
    Max = 50,
    Rounding = 1,
    Compact = true,
    Callback = function(v)
        values.speedV = v
    end
})

group.left:AddDropdown('cv3', {
    Values = {'Boost', 'Walkspeed', 'CFrameWalk'},
    Text = 'Speed method',
    Default = 'Boost',
    Multi = false,
    Tooltip = 'what method to use for speed',
    Callback = function(v)
        speedMethod = v
    end
})

group.left:AddLabel('Fly - F'):AddKeyPicker('cv6', {
    Default = 'F',
    NoUI = false,
    Text = 'Fly',
    Callback = function(v)
        flags.fly = v
    end
})
-- field of view
group.right:AddToggle('cv4', {
    Text = 'FOV',
    Default = true,
    Tooltip = 'changes fov',
    Callback = function(v)
        flags.fov = v
    end
})

group.right:AddSlider('cv5', {
    Text = 'FOV',
    Default = 120,
    Min = 70,
    Max = 120,
    Rounding = 1,
    Compact = true,
    Callback = function(v)
        warn(v)
        values.fieldOfView = v
    end
})

-- noclip
group.left2:AddLabel('Noclip'):AddKeyPicker('cv6', {
    Default = 'N',
    NoUI = false,
    Text = 'Noclip',
    Callback = function(v)
        flags.noclip = v
        if flags.noclip then
            for i, Part in character:GetDescendants() do
                if Part:IsA'BasePart' then
                    local CC = Part.CanCollide
                    task.spawn(function()
                        repeat
                            Part.CanCollide = false
                            task.wait()
                        until not flags.noclip
                        Part.CanCollide = CC
                    end)
                end
            end
        end
    end
})
-- esp
local promptsToAura = {}
local reachedPrompts = {}
local auraPrompts = {
    'ActivateEventPrompt',
    'HerbPrompt',
    'LootPrompt',
    'ModulePrompt'
}

local reach; do
    reach = 7
end

function roomAdded(newRoom)
    local room = workspace.CurrentRooms[latestRoom.Value]
    local assets = room:WaitForChild'Assets'
    
    task.spawn(function()
        repeat
            if flags.doorreach then
                room.Door.ClientOpen:FireServer()
            end
            task.wait()
        until room ~= workspace.CurrentRooms[latestRoom.Value]
    end)

    local function descendants(v)

        if AntiEntity.Obstruction then
            if v.Name == 'AnimatorPart' or v.Name == 'HurtPart' then
                v.CanTouch = false
                v.Transparency = 0.75
                v:ClearAllChildren()
            end
        end

        if AntiEntity.Seek then
            if v:IsA'BasePart' and v.Parent and v.Parent.Name == 'TriggerEventCollision' and v.Name == 'Collision' then
                v.CanTouch = false
            end
        end

        if v:IsA'ProximityPrompt' then
            if flags.clipPrompts then
                v.RequiresLineOfSight = false
            end
            if flags.reachPrompts then
                task.spawn(function()
                    repeat
                        v.MaxActivationDistance = 7 + reach
                        task.wait()
                    until v.Parent == nil
                end)
            end
            if table.find(auraPrompts, v.Name) and flags.auraPrompts then
                task.spawn(function()
                    repeat
                        if v:IsA'ProximityPrompt' then
                            local interaction = 'Interactions'..player.Name
                            if v:GetAttribute(interaction) then return end
                            firep(v)
                        end
                        task.wait()
                    until v.Parent == nil
                end)
            end
        end

        task.spawn(function()
            if v.Name == 'Key' then
                espAttempt(v:FindFirstAncestor'KeyObtain', 'item', 'Key')
            elseif v.Name == 'LiveHintBook' then
                espAttempt(v, 'item', 'Book')
            elseif v.Name == 'TimerLever' then
                espAttempt(v, 'item', 'Lever')
            elseif v.Name == 'FigureRagdoll' then
                if AntiEntity.Figure then
                    for i,v2 in v:GetDescendants() do
                        pcall(function()
                            v2.CanCollide = false
                        end)
                    end
    
                    task.spawn(function()
                        repeat
                            task.wait()
                            v:PivotTo(v:GetPivot() * CFrame.new(0, -2, 1) * CFrame.Angles(0, math.rad(10), 0))
                        until v.Parent == nil
                        notify('figure death :(((')
                    end)
                end

                espAttempt(v, 'entity', 'Figure')
            elseif v.Name == 'Snare' then
                
                if AntiEntity.Snare then
                    v.CanTouch = false
                end

                espAttempt(v, 'entity', 'Snare')
            elseif v.Name:match('Pickup') then
                espAttempt(v, 'item', v.Name:match'LiveBreakerPolePickup' and 'Breaker' or v.Name)
            elseif v.Name == 'GoldPile' then
                espAttempt(v, 'item', 'Gold [' .. v:GetAttribute'GoldValue' .. ']')
            end
        end)
    end

    for i,v in room:GetDescendants() do
        descendants(v)
    end

    room.DescendantAdded:Connect(descendants)
    
    for i,v in assets:GetChildren() do
        if v.Name == 'Wardrobe' or v.Name == 'Toolshed' or v.Name == 'Rooms_Locker' then
            espAttempt(v, 'roomAsset', 'Closet')
        end
    end
    espAttempt(room.Name == '50' and room.Door or floor.Value == 'Rooms' and room.Door or room.Door.Door, 'roomAsset', room:GetAttribute('RequiresKey') and 'Locked Door' or 'Door')
end

function workspaceAdded(entity)
    task.wait(0.01)
    if player:DistanceFromCharacter(entity.PrimaryPart.Position) <= 2000 then
        entity.PrimaryPart.Transparency = 0
        entity.PrimaryPart.Material = Enum.Material.SmoothPlastic
        espAttempt(entity, 'entity', entity.Name:split('Moving')[1] or entity.Name:split('Backdoor')[1] or entity.Name)
    end
end

workspace.ChildAdded:Connect(workspaceAdded)

roomAdded()

workspace.CurrentRooms.ChildAdded:Connect(roomAdded)

group.esp:AddToggle('cv8', {
    Text = 'ESP',
    Default = true,
    Tooltip = 'See items through walls',
    Callback = function(v)
        flags.esp = v
    end
})

group.esp:AddToggle('cv8m2', {
    Text = 'Rainbow Mode',
    Default = false,
    Tooltip = 'Makes the esp rainbow',
    Callback = function(v)
        flags.espRainbow = v
    end
})

group.esp:AddToggle('cv8m77', {
    Text = 'Tracer',
    Default = false,
    Tooltip = 'Line',
    Callback = function(v)
        flags.tracers = v
    end
})

group.esp:AddLabel('Item color'):AddColorPicker('cv9', {
    Title = 'Item color',
    Default = espColors[1],
    Callback = function(v)
        espColors[1] = v
    end
})

group.esp:AddLabel('Asset color'):AddColorPicker('cv10', {
    Title = 'Asset color',
    Default = espColors[2],
    Callback = function(v)
        espColors[2] = v
    end
})

group.esp:AddLabel('Entity color'):AddColorPicker('cv11', {
    Title = 'Entity color',
    Default = espColors[3],
    Callback = function(v)
        espColors[3] = v
    end
})
-- prompts

local exitKeys = {
    Enum.KeyCode.W,
    Enum.KeyCode.A,
    Enum.KeyCode.S,
    Enum.KeyCode.D
}

game:GetService('ProximityPromptService').PromptTriggered:Connect(function(prompt)
    if prompt.Parent.Name == 'Wardrobe' or prompt.Parent.Name == 'Toolshed' then
        local ogTransparency = {}
        for i, v in pairs(prompt.Parent:GetDescendants()) do
            pcall(function()
                ogTransparency[v.Name] = v.Transparency
                v.Transparency = .45
            end)
        end
        task.wait(1)
        task.spawn(function()
            repeat
                if not UserInputService:GetFocusedTextBox() and UserInputService:IsKeyDown(exitKeys[1]) or UserInputService:IsKeyDown(exitKeys[2]) or UserInputService:IsKeyDown(exitKeys[3]) or UserInputService:IsKeyDown(exitKeys[4]) then
                    for i, v in pairs(prompt.Parent:GetDescendants()) do
                        pcall(function()
                            v.Transparency = ogTransparency[v.Name]
                        end)
                    end
                    remoteFolder.CamLock:FireServer()
                    break
                end
                task.wait()
            until false
        end)
    end
end)
group.prompts:AddToggle('cv13m', {
    Text = 'Aura',
    Tooltip = 'Use prompts all around you',
    Default = true,
    Callback = function(v)
        flags.auraPrompts = v
    end
})
group.prompts:AddToggle('cv13', {
    Text = 'Clip',
    Tooltip = 'Allows prompts to be used through objects',
    Default = true,
    Callback = function(v)
        flags.clipPrompts = v
    end
})

group.prompts:AddToggle('cv14', {
    Text = 'Reach',
    Tooltip = 'Use prompts from a distance',
    Default = true,
    Callback = function(v)
        flags.reachPrompts = v
    end
})

group.prompts:AddSlider('cv15', {
    Text = 'Distance',
    Default = 7,
    Min = 7,
    Max = 30,
    Rounding = 1,
    Compact = true,
    Callback = function(v)
        reach = v
    end
})

-- connections
local frame = {
    Counter = 0,
    Timer = tick()
}
local fps = 0

local watermarkConnection = runService.RenderStepped:Connect(function(deltaTime)
    frame.Counter += 1
    
    if (tick() - frame.Timer) >= 1 then
        frame.Timer = tick()
        fps = frame.Counter
        frame.Counter = 0
    end

    local watermark = ('catlova x karlol.. | %s fps'):format(fps)

    lib:SetWatermark(watermark)
end)

-- mainscript
local speedbypassCVM = tick()
local flyvelo = Instance.new('BodyVelocity'); do
    flyvelo.Velocity = Vector3.zero
    flyvelo.MaxForce = Vector3.one * 9e9
end

local NoAccel = PhysicalProperties.new(100, 2, 0, 100, 0)
character.Collision.CollisionCrouch.Massless = true
character.Collision.CollisionCrouch.CanCollide = false

function OnCharacterAdded(Character)
    Character.ChildAdded:Connect(function(Object)
        task.wait(.5)
        if Object:IsA'Tool' then
            if Object.Name == 'LibraryHintPaper' and flags.padLock then
                notify(string.format('The current code is: %s', getPadlockCode(Object)))
                
                if tonumber(getPadlockCode(Object)) then
                    remoteFolder.PL:FireServer(tostring(getPadlockCode(Object)))
                end
            end
        end
    end)
end

player.CharacterAdded:Connect(OnCharacterAdded)
OnCharacterAdded(character)

runService.RenderStepped:Connect(function(delta)
    game:GetService('ProximityPromptService').MaxPromptsVisible = 50
    character = player.Character
    character.HumanoidRootPart.CustomPhysicalProperties = NoAccel
    character.Collision.CustomPhysicalProperties = NoAccel
    character.Collision.CollisionCrouch.CustomPhysicalProperties = NoAccel
    task.spawn(function()
        if flags.speed then -- speed
            if speedMethod == 'Walkspeed' then
                character.Humanoid.WalkSpeed = 15 + values.speedV
            elseif speedMethod == 'Boost' then
                character.Humanoid:SetAttribute('SpeedBoostBehind', values.speedV)
            elseif speedMethod == 'CFrameWalk' then
                character:TranslateBy(character.Humanoid.MoveDirection * (values.speedV / 1.23) / 100)
            end
            if (tick() - speedbypassCVM) >= values.speedbypassDelay then
                character.Collision.Massless = not character.Collision.Massless
                speedbypassCVM = tick()
            end
        else
            speedbypassCVM = tick()
        end
    end)
    
    task.spawn(function()
        if flags.fb then
            game:GetService('TweenService'):Create(lighting, TweenInfo.new(.9), {
                Ambient = Color3.fromRGB(255/2, 255/2, 255/2)
            }):Play()
        end
    end)

    task.spawn(function()
        if flags.voiddisabler then
            workspace.FallenPartsDestroyHeight = 0 / 0 -- otherwise known as nan! (a number below -math.huge)
        else
            workspace.FallenPartsDestroyHeight = OldVoid
        end
    end)

    task.spawn(function()
        if flags.thirdperson then
            camera.CameraType = Enum.CameraType.Custom
            character.HumanoidRootPart.CFrame = CFrame.new(character.HumanoidRootPart.Position) * CFrame.fromOrientation(0, ({camera.CFrame:ToOrientation()})[2], 0)
            character.Head.Transparency = 0
            for i, v in character:GetChildren() do
                if v:IsA'Accessory' and v.Name:lower():match('accessory') then
                    for name, src in v:GetChildren() do
                        pcall(function()
                            src.Transparency = 0
                        end)
                    end
                end
            end
        else
            camera.CameraType = Enum.CameraType.Scriptable
            character.Head.Transparency = 1
            for i, v in character:GetChildren() do
                if v:IsA'Accessory' and v.Name:lower():match('accessory') then
                    for name, src in v:GetChildren() do
                        pcall(function()
                            src.Transparency = 1
                        end)
                    end
                end
            end
        end
    end)

    task.spawn(function()
        flyvelo.Parent = flags.fly and character.HumanoidRootPart or nil
        flyvelo.Name = math.random(1000, 9999)
        if flags.fly then
            local add = Vector3.zero

            if UserInputService:IsKeyDown(Enum.KeyCode.W) then add += camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then add -= camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then add += camera.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then add -= camera.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then add += camera.CFrame.UpVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then add -= camera.CFrame.UpVector end

            flyvelo.Velocity = add * (values.speedV * 20) / 300 * 22
        end
    end)

    if eyesSpawned() then
        if AntiEntity.Eyes then
            if eyesSpawned() then
                remoteFolder.MotorReplication:FireServer(0, -90, 0, false)
            end
        end
    end

    task.spawn(function()
        if flags.fov then
            camera.FieldOfView = values.fieldOfView
        else
            camera.FieldOfView = 70
        end
    end)

    pcall(function()
        if AntiEntity.Screech then
            OldScreechModule.Name = 'lol'
        end

        if AntiEntity.A90 then
            OldA90Module.Name = 'blahhh'
        end
    end)
end)

-- finishing up

lib.ToggleKeybind = Options.MenuKeybind

local configuration = window:AddTab'Configuration'
do
    theme:SetLibrary(lib)
    save:SetLibrary(lib)
    save:IgnoreThemeSettings()
    save:SetIgnoreIndexes({ 'MenuKeybind' })
    theme:SetFolder('baa')
    save:SetFolder('baa/doors')
    save:BuildConfigSection(configuration)
    theme:ApplyToTab(configuration)
    save:LoadAutoloadConfig()
end
