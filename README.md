pcall(function()
    local Library = loadstring(game:HttpGet('https://raw.githubusercontent.com/Loco-CTO/UI-Library/main/VisionLibV2/source.lua'))()
    
    local Window = Library:Create({
       Name = "Fuck kiki XDD", -- String
       Footer = "Kiki trash", -- String
       ToggleKey = Enum.KeyCode.RightShift, -- Enum.KeyCode
       LoadedCallback = function()
           -- Function
       end,
    
       KeySystem = false, -- Boolean
       Key = "keyabc123", -- String
       MaxAttempts = 5, -- Integer
       DiscordLink = "https://discord.gg/Bp7wFcZeUn", -- String (Set it to nil if you do not have one, the button will not pop out)
       ToggledRelativeYOffset = 5 -- Number (Y Offset from bottom of your screen. Set it to nil if you want it to be centred)
    })

-------TABS
local MainTab = Window:Tab({
    Name = "Main", -- String
    Icon = "rbxassetid://11396131982", -- String
    Color = Color3.new(1, 0, 0)
 })

    local TPTab = Window:Tab({
       Name = "Player", -- String
       Icon = "rbxassetid://11396131982", -- String
       Color = Color3.new(1, 0, 0)
    })
    
    local BlatantTab = Window:Tab({
        Name = "Blatant", -- String
        Icon = "rbxassetid://11396131982", -- String
        Color = Color3.new(1, 0, 0)
     })

     local EspTab = Window:Tab({
        Name = "Esp", -- String
        Icon = "rbxassetid://11396131982", -- String
        Color = Color3.new(1, 0, 0)
     })

-------SECTIONS

local MainSection = MainTab:Section({
    Name = "Main" -- String
 })

 local TPSection = TPTab:Section({
    Name = "Player" -- String
 })

 local BlatantSection = BlatantTab:Section({
    Name = "Blatant" -- String
 })

 local AntiSection = BlatantTab:Section({
    Name = "Anti" -- String
 })

 local TimersSection = BlatantTab:Section({
    Name = "Last Looted Timers" -- String
 })

 local EspSection = EspTab:Section({
    Name = "Esp" -- String
 })
------ SCRIPTS

------ FLY
    local Slider = TPSection:Slider({
       Name = "Fly Speed", -- String
       Value = flySpeed,
       Max = 250, -- Integer
       Min = 0, -- Integer
       Default = 200, -- Integer
       Callback = function(value)
           flySpeed = value
         end
    })
    
    local Slider = TPSection:Slider({
       Name = "Fall Speed", -- String
       Value = fallSpeed,
       Max = 100, -- Integer
       Min = 0, -- Integer
       Default = 50, -- Integer
       Callback = function(value)
           fallSpeed = value
         end
    })
    
    local Toggle = TPSection:Toggle({
       Name = "Fly", -- String
       Default = false, -- Boolean
       Callback = function(value)
           flyEnabled = value
       end
    })
    
    local Keybind = TPSection:Keybind({
        Name = "Fly Keybind",
        Default = Enum.KeyCode.Return,
        Callback = function()
            Toggle:Set(not flyEnabled)
        end,
        UpdateKeyCallback = function(Key)
            -- Function
        end
    })

    game:GetService("Players").LocalPlayer.CharacterAdded:Connect(function(char)
       char:WaitForChild("Humanoid")
       game.Players.LocalPlayer.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
    end)
    
    game:GetService("RunService").RenderStepped:Connect(function()
       local char = game:GetService("Players").LocalPlayer.Character
       local velocity = Vector3.new()
       if flyEnabled then
           local lookVector = (game:GetService("Workspace").CurrentCamera.Focus.p - game:GetService("Workspace").CurrentCamera.CFrame.p).Unit
           if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.Space) then
               velocity = velocity + Vector3.new(0, flySpeed, 0)
           end
           if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
               velocity = velocity + lookVector * flySpeed
           end
           if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
               velocity = velocity - lookVector * flySpeed
           end
           if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.A) then
               velocity = velocity - Vector3.new(-lookVector.Z, 0, lookVector.X) * flySpeed
           end
           if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.D) then
               velocity = velocity + Vector3.new(-lookVector.Z, 0, lookVector.X) * flySpeed
           end
       velocity = velocity - Vector3.new(0, fallSpeed, 0)
       char.HumanoidRootPart.Velocity = velocity
       end
    end)

------ INFINITE JUMP

    local infJumpEnabled = false
    local isSpaceKeyDown = false
    local originalGravity = workspace.Gravity

    local InfiniteJumpToggle = TPSection:Toggle({
        Name = "Infinite Jump", -- String
        Default = false, -- Boolean
        Callback = function(value)
            infJumpEnabled = value
        
            if infJumpEnabled then
                game:GetService("UserInputService").InputBegan:Connect(function(input, IsTyping)
                    if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == Enum.KeyCode.Space then
                        if IsTyping then return end
                        isSpaceKeyDown = true
                    end
                end)
                
                game:GetService("UserInputService").InputEnded:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == Enum.KeyCode.Space then
                        isSpaceKeyDown = false
                    end
                end)
                
                while infJumpEnabled do
                    if isSpaceKeyDown then
                        game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
                    else
                        workspace.Gravity = originalGravity
                    end
                    wait()
                end
                
                workspace.Gravity = originalGravity 
            else
                workspace.Gravity = originalGravity 
            end
        end
    })

----- HEALER EDICT

local plr = game:GetService("Players").LocalPlayer.Character
local function setPlayerHealthDistance(toggleValue)
    for i, v in pairs(workspace.Live:GetChildren()) do
        if v ~= plr then
            local z = v:FindFirstChildWhichIsA("Humanoid")
            if z then
                z.HealthDisplayType = Enum.HumanoidHealthDisplayType.DisplayWhenDamaged
                if v:FindFirstChild("MonsterInfo") then
                    z.NameDisplayDistance = 0
                end
                z.HealthDisplayDistance = toggleValue and 35 or 0
                z.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.Subject
            end
        end
    end
end

    local HealerToggle = MainSection:Toggle({
        Name = "Healer Edict", -- String
        Default = false, -- Boolean
        Callback = function(value)
            setPlayerHealthDistance(Value)
        end
     })

--------- MODERATOR NOTIFIER

    local HealerToggle = MainSection:Toggle({
        Name = "Moderator Detector", -- String
        Default = false, -- Boolean
        Callback = function(value)
            task.spawn(function()
                local staff = {
                    "1x9x1x4x7",
                    "dadmqe30",
                    "Nuttoons",
                    "Selanto12",
                    "lulux44",
                    "LucidZero",
                    "NotASeraph",
                    "jassbux",
                    "fun135090",
                    "Divinos",
                    "pugrat447",
                    "huuc",
                    "baneH1",
                    "Orchamy",
                    "ZaeoMR",
                    "Potioncake",
                    "Sayaphic",
                    "Noah5254",
                    "Seoi_Ha",
                    "Torrera",
                    "v_sheep",
                    "Voidsealer",
                    "2Squids",
                    "pyfrog",
                    "crazywealthyman",
                    "warycoolio",
                    "marvindot",
                    "Taelyia",
                    "SuperEashan",
                    "lleirbag",
                    "lleirbag",
                    "Lord_Dusk",
                    "bluetetraninja",
                    "killer67564564643",
                    "Nuttoons",
                    "0OAmnesiaO0",
                    "SlrCIover",
                    "WinterFIare",
                    "WorkyCIock",
                    "LunarKaiser",
                    "Kark1n0s",
                    "HateKait",
                    "Frostdraw",
                    "iltria",
                    "yomamagamer1",
                    "Etrn_al",
                    "Thunder878712817",
                    "TheNotDave",
                    "Yuukisnoob",
                    "fiod1",
                    "GIovoc"    
                }
                Library:Notify({
                    Name = "Checking For Moderators", -- String
                    Text = "Notification!!", -- String
                    Icon = "rbxassetid://11401835376", -- String
                    Sound = "rbxassetid://6647898215", -- String
                    Duration = 5, -- Integer
                    Callback = function()
                        -- Function
                    end
                })

                while task.wait(10) do
                table.foreach(staff,function(a,b)
                for _,v in pairs(game:GetService("Players"):GetChildren()) do
                if v.Name == b then
                    local Sound = Instance.new("Sound")
                    Sound.SoundId = "rbxassetid://9120386436"
                    
                    Library:Notify({
                        Name = "Moderator Detected", -- String
                        Text = v.Name .. " is a Moderator", -- String
                        Icon = "rbxassetid://11401835376", -- String
                        Sound = "rbxassetid://6647898215", -- String
                        Duration = 5, -- Integer
                        Callback = function()
                            -- Function
                        end
                    })
                end
                end
                end)
                end
                end)
                end
                })

-------- Illusonist notifier

local illus = {}
local gnavs = {}
local notifierEnabled = false

local function enableNotifier()
    notifierEnabled = true
end

local function disableNotifier()
    notifierEnabled = false
end

local IlluToggle = MainSection:Toggle({
    Name = "Illusionist/Spec Notify", -- String
    Default = false, -- Boolean
    Callback = function(value)
        if value then
            enableNotifier()
        else
            disableNotifier()
        end
    end
 })

 spawn(function()
    while wait() do 
        for i, v in pairs(game.Players:GetPlayers()) do 
            if notifierEnabled then
                if v:FindFirstChild("Backpack") and v:FindFirstChild("Jester's Trick") and not table.find(gnavs, v) or v.Character and v.Character:FindFirstChild("Jester's Trick") and not table.find(gnavs, v) then 
                    table.insert(gnavs, v)
                    Library:Notify({
                        Name = "Spec User Detected", -- String
                        Text = "Player: "..v.Name.."\n Id: "..v.UserId.." \n Could Be Moderator, Be Cautious.", -- String
                        Icon = "rbxassetid://11401835376", -- String
                        Sound = "rbxassetid://6647898215", -- String
                        Duration = 5, -- Integer
                        Callback = function()
                            -- Function
                        end
                    })
                end
                if v:FindFirstChild("Backpack") and v.Backpack:FindFirstChild("Observe") and not table.find(illus, v) or v.Character and v.Character:FindFirstChild("Observe") and not table.find(illus, v) then 
                    table.insert(illus, v)
                    Library:Notify({
                        Name = "Illusionist In Server", -- String
                        Text = "Player: "..v.Name.."\n Id: "..v.UserId, -- String
                        Icon = "rbxassetid://11401835376", -- String
                        Sound = "rbxassetid://6647898215", -- String
                        Duration = 5, -- Integer
                        Callback = function()
                            -- Function
                        end
                    })
                end
            end
        end
    end
end)

------- BACK ATTACH

local BackAttachSlider = BlatantSection:Slider({
    Name = "Attach To Back Space", -- String
    Value = 0,
    Max = 5, -- Integer
    Min = 0, -- Integer
    Default = 0, -- Integer
    Precision = 1,
    Callback = function(Value, OldValue)
        SpaceValue = Value
      end
 })

 local Attached = false 

 local BackAttach = BlatantSection:Button({
	Name = "Attach To Back (Press X)", -- String
	Callback = function()
		pcall(function()
            local UIS = game:GetService("UserInputService")
            local Player = game:GetService("Players").LocalPlayer
            local Mouse = Player:GetMouse()
            local t2
            
            UIS.InputBegan:Connect(function(i, t)
                if i.KeyCode == Enum.KeyCode.X and not t then
                    local target = Mouse.Target
                    if target ~= nil and target.Parent:FindFirstChild("Humanoid") and not t then
                        t2 = true
                        if (Player.Character.Head.Position - target.Parent.Head.Position).Magnitude < 100  and t2 then
                            while wait() do
                                Player.Character.HumanoidRootPart.CFrame = target.Parent.HumanoidRootPart.CFrame * CFrame.new(0,0,SpaceValue)
                                if t2 == false then
                                    break
                                end
                            end
                        end
                    else
                        t2 = false
                        target = nil
                    end
                end
            end)
         end)
	end
})
-------------- trinket esp
 local TrinketToggle = EspSection:Button({
    Name = "Trinket Esp", -- String
    Callback = function(value)
	 local RunService = game:GetService("RunService")
 local Workspace = game:GetService("Workspace")
 
 local RenderDistance = 120000
 
 local Camera = Workspace.CurrentCamera
 local RenderStepped = RunService.RenderStepped
 
 local Cache = {}
 local TrinketType = loadstring(game:HttpGet("https://pastebin.com/raw/6hX7DEpP"))()
 
 local function NewDrawing(Type, Properties)
     local Drawing = Drawing.new(Type)
     
     for i, v in pairs(Properties) do
         Drawing[i] = v
     end
     
     return Drawing
 end
 
 local function NewESP(Object, Info)
     local ESP = {}
     
     ESP.Box = NewDrawing('Square', {
         Thickness = 1,
         ZIndex = 1
     })
     
     ESP.BoxOutline = NewDrawing('Square', {
         Color = Color3.fromRGB(0, 0, 0),
         Thickness = 3,
     })
     
     ESP.Distance = NewDrawing('Text', {
         Center = true,
         Font = 0,
         Outline = true,
         Size = 24
     })
     
     ESP.Name = NewDrawing('Text', {
         Center = true,
         Font = 0,
         Outline = true,
         Size = 24
     })
     
     Cache[Object] = {
         Info = Info,
         ESP = ESP
     }
 end
 
 local function RemoveESP(Object)
     if Cache[Object] then
         for _, v in pairs(Cache[Object].ESP) do
             v:Remove()
         end
         
         Cache[Object] = nil
     end
 end
 
 local function Update()
     for i, v in pairs(Cache) do
         if not i or not i.Parent then
             RemoveESP(i); continue
         end

         local Distance = math.floor((Camera.CFrame.Position - i.Position).Magnitude + 0.5)
         local Pos, InView = Camera:WorldToViewportPoint(i.Position)
         
         if not InView or Distance > RenderDistance then
             v.ESP.Box.Visible = false; v.ESP.BoxOutline.Visible = false; v.ESP.Distance.Visible = false; v.ESP.Name.Visible = false; continue
         end
         
         local ScaleFactor = 1 / (Pos.Z * math.tan(math.rad(Camera.FieldOfView / 2)) * 2) * 100
         local Size = 16 * ScaleFactor
         
         v.ESP.Box.Color = v.Info.Color
         v.ESP.Box.Position = Vector2.new(Pos.X - (Size / 2), Pos.Y - (Size / 2))
         v.ESP.Box.Size = Vector2.new(Size, Size)
         v.ESP.Box.Visible = true
         
         v.ESP.BoxOutline.Position = v.ESP.Box.Position
         v.ESP.BoxOutline.Size = v.ESP.Box.Size
         v.ESP.BoxOutline.Visible = true
         
         v.ESP.Distance.Color = v.Info.Color
         v.ESP.Distance.Text = tostring(Distance)
         v.ESP.Distance.Position = Vector2.new(Pos.X, Pos.Y + Size / 2)
         v.ESP.Distance.Visible = true
         
         v.ESP.Name.Color = v.Info.Color
         v.ESP.Name.Text = v.Info.Name
         v.ESP.Name.Position = Vector2.new(Pos.X, Pos.Y - Size / 2 - v.ESP.Name.TextBounds.Y)
         v.ESP.Name.Visible = true
     end
 end
 
 for _, v in pairs(Workspace:GetChildren()) do
     if v:FindFirstChildWhichIsA('ClickDetector', true) and v:FindFirstChild('ID') then
         local Info = TrinketType(v)
         NewESP(v, Info)
     end
 end
 
 Workspace.ChildAdded:Connect(function(Object)
     if Object:FindFirstChildWhichIsA('ClickDetector', true) and Object:FindFirstChild('ID') then
         local Info = TrinketType(Object)
         NewESP(Object, Info)
     end
 end)
 
 RenderStepped:Connect(Update)
    end
 })
---------- Reset
local ResetButton = MainSection:Button({
	Name = "Instant Log", -- String
	Callback = function()
        plr.Character:Destroy()
wait()
player:Kick("Instant Logged")
	end
})
---------- Server hop
local ServerButton = MainSection:Button({
	Name = "Server Hop", -- String
	Callback = function()
    game:GetService("StarterGui"):SetCore("PromptBlockPlayer", game.Players:GetChildren()[2])
    wait(4)
    game:GetService("TeleportService"):Teleport(game.PlaceId, game:GetService("Players").LocalPlayer)
	end
})
--------- No Stun
local NoStun = false
local NoStunButton = AntiSection:Button({
	Name = "Anti Stun", -- String
	Callback = function()
        NoStun = value
        if NoStun then
            game.Players.LocalPlayer.Character.ChildAdded:Connect(function(child)
                if child.Name == "Stun" or child.Name == "NoJump" or child.Name == "Action" then 
                    task.wait()
                    child:Destroy()
                end
            end)
        end
	end
})
-------- ANTI FIRE
local FireToggle = AntiSection:Toggle({
    Name = "Anti Fire", -- String
    Default = false, -- Boolean
    Callback = function(value)
        if value then 
            local character = game.Players.LocalPlayer.Character

            if character and character:FindFirstChild("Burning") then 
                game.ReplicatedStorage.Remotes.Dodge:FireServer({4, math_random()})
            end
        end
    end
 })

------- NO KILLBRICKS
local KillToggle = AntiSection:Toggle({
    Name = "Anti Killbricks", -- String
    Default = false, -- Boolean
    Callback = function(value)
        KBSEnabled = value
        local kbs = {}
        for i, v in pairs(game.Workspace:GetDescendants()) do
            if v:FindFirstChild("TouchInterest") and v.Name ~= "OrderField" and v.Name ~= "SolanBall" and v.Name ~= "SolansGate" and v.Name ~= "TeleportOut" and v.Name ~= "BaalField" and v.Name ~= "Elevator" and v.Name ~= "MageField" and v.Name ~= "TeleportIn" then
                table.insert(kbs,v)
            end
        end
        for i, v in pairs(kbs) do
            v.CanTouch = not KBSEnabled
        end
    end
 })
 ------- FULLBRIGHT
 local FULLButton = AntiSection:Button({
	Name = "Full Bright", -- String
	Callback = function()
		if not _G.FullBrightExecuted then

            _G.FullBrightEnabled = false
        
            _G.NormalLightingSettings = {
                Brightness = game:GetService("Lighting").Brightness,
                ClockTime = game:GetService("Lighting").ClockTime,
                FogEnd = game:GetService("Lighting").FogEnd,
                GlobalShadows = game:GetService("Lighting").GlobalShadows,
                Ambient = game:GetService("Lighting").Ambient
            }
        
            game:GetService("Lighting"):GetPropertyChangedSignal("Brightness"):Connect(function()
                if game:GetService("Lighting").Brightness ~= 1 and game:GetService("Lighting").Brightness ~= _G.NormalLightingSettings.Brightness then
                    _G.NormalLightingSettings.Brightness = game:GetService("Lighting").Brightness
                    if not _G.FullBrightEnabled then
                        repeat
                            wait()
                        until _G.FullBrightEnabled
                    end
                    game:GetService("Lighting").Brightness = 1
                end
            end)
        
            game:GetService("Lighting"):GetPropertyChangedSignal("ClockTime"):Connect(function()
                if game:GetService("Lighting").ClockTime ~= 12 and game:GetService("Lighting").ClockTime ~= _G.NormalLightingSettings.ClockTime then
                    _G.NormalLightingSettings.ClockTime = game:GetService("Lighting").ClockTime
                    if not _G.FullBrightEnabled then
                        repeat
                            wait()
                        until _G.FullBrightEnabled
                    end
                    game:GetService("Lighting").ClockTime = 12
                end
            end)
        
            game:GetService("Lighting"):GetPropertyChangedSignal("FogEnd"):Connect(function()
                if game:GetService("Lighting").FogEnd ~= 786543 and game:GetService("Lighting").FogEnd ~= _G.NormalLightingSettings.FogEnd then
                    _G.NormalLightingSettings.FogEnd = game:GetService("Lighting").FogEnd
                    if not _G.FullBrightEnabled then
                        repeat
                            wait()
                        until _G.FullBrightEnabled
                    end
                    game:GetService("Lighting").FogEnd = 786543
                end
            end)
        
            game:GetService("Lighting"):GetPropertyChangedSignal("GlobalShadows"):Connect(function()
                if game:GetService("Lighting").GlobalShadows ~= false and game:GetService("Lighting").GlobalShadows ~= _G.NormalLightingSettings.GlobalShadows then
                    _G.NormalLightingSettings.GlobalShadows = game:GetService("Lighting").GlobalShadows
                    if not _G.FullBrightEnabled then
                        repeat
                            wait()
                        until _G.FullBrightEnabled
                    end
                    game:GetService("Lighting").GlobalShadows = false
                end
            end)
        
            game:GetService("Lighting"):GetPropertyChangedSignal("Ambient"):Connect(function()
                if game:GetService("Lighting").Ambient ~= Color3.fromRGB(178, 178, 178) and game:GetService("Lighting").Ambient ~= _G.NormalLightingSettings.Ambient then
                    _G.NormalLightingSettings.Ambient = game:GetService("Lighting").Ambient
                    if not _G.FullBrightEnabled then
                        repeat
                            wait()
                        until _G.FullBrightEnabled
                    end
                    game:GetService("Lighting").Ambient = Color3.fromRGB(178, 178, 178)
                end
            end)
        
            game:GetService("Lighting").Brightness = 1
            game:GetService("Lighting").ClockTime = 12
            game:GetService("Lighting").FogEnd = 786543
            game:GetService("Lighting").GlobalShadows = false
            game:GetService("Lighting").Ambient = Color3.fromRGB(178, 178, 178)
        
            local LatestValue = true
            spawn(function()
                repeat
                    wait()
                until _G.FullBrightEnabled
                while wait() do
                    if _G.FullBrightEnabled ~= LatestValue then
                        if not _G.FullBrightEnabled then
                            game:GetService("Lighting").Brightness = _G.NormalLightingSettings.Brightness
                            game:GetService("Lighting").ClockTime = _G.NormalLightingSettings.ClockTime
                            game:GetService("Lighting").FogEnd = _G.NormalLightingSettings.FogEnd
                            game:GetService("Lighting").GlobalShadows = _G.NormalLightingSettings.GlobalShadows
                            game:GetService("Lighting").Ambient = _G.NormalLightingSettings.Ambient
                        else
                            game:GetService("Lighting").Brightness = 1
                            game:GetService("Lighting").ClockTime = 12
                            game:GetService("Lighting").FogEnd = 786543
                            game:GetService("Lighting").GlobalShadows = false
                            game:GetService("Lighting").Ambient = Color3.fromRGB(178, 178, 178)
                        end
                        LatestValue = not LatestValue
                    end
                end
            end)
            end
	end
})
------- Anti Mental Injuries
local AntiMentalInjuries = false

local mental_injuries = {
    Hallucinations = true,
    PsychoInjury = true,
    AttackExcept = true,
    Whispering = true,
    Quivering = true,
    NoControl = true,
    Careless = true,
    Maniacal = true,
    Fearful = true
}

local AntiMentalToggle = AntiSection:Toggle({
	Name = "Anti Mental Injuries", -- String
	Default = false, -- Boolean
	Callback = function(value)
		AntiMentalInjuries = value
	end
})

game.Players.LocalPlayer.CharacterAdded:Connect(function(character)
    if AntiMentalInjuries then
        local character_children = character:GetChildren()

        for index = 1, #character_children do
            local child = character_children[index]

            if child and mental_injuries[child.Name] then
                child:Destroy()
            end
        end
    end
end)
------- Player ESP
local Sense = loadstring(game:HttpGet('https://raw.githubusercontent.com/DominykasTA/daddy/main/somethin.lua'))()
Sense.teamSettings.enemy.enabled = false
Sense.teamSettings.enemy.box = true
Sense.teamSettings.enemy.healthBar = true
Sense.teamSettings.enemy.distance = true
Sense.teamSettings.enemy.name = true
Sense.teamSettings.enemy.weapon = true
Sense.teamSettings.enemy.boxColor[1] = Color3.new(0, 0.25, 0.75)
Sense.Load()


    EspSection:Toggle({
        Name = "Player Esp",
        Default = false,
        Callback = function(value)
        Sense.teamSettings.enemy.enabled = value

        end
    })

    EspSection:Toggle({
        Name = "Tracers",
        Default = false,
        Callback = function(value)
        Sense.teamSettings.enemy.tracer = value

        end
    })
    
    EspSection:Toggle({
        Name = "Arrows",
        Default = false,
        Callback = function(value)
        Sense.teamSettings.enemy.offScreenArrow = value

        end
    })
    
    EspSection:Toggle({
        Name = "Chams",
        Default = false,
        Callback = function(value)
        Sense.teamSettings.enemy.chams = value

        end
    })
------- RESET
local ResetButton = MainSection:Button({
	Name = "Reset", -- String
	Callback = function()
		game.Players.LocalPlayer.Character.Humanoid.Health = 0
	end
})
------- Wipe
local WipeButton = MainSection:Button({
	Name = "Wipe", -- String
	Callback = function()
		game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game:GetService("Workspace").Map.KillBrick.CFrame
	end
})
------ Chat Logger
local ChatButton = MainSection:Button({
	Name = "Chat Logger", -- String
	Callback = function()
		if game:service('RunService'):IsStudio() then print('!STUDIO!') else
            if game:service('CoreGui'):findFirstChild('LogHolder') then return nil
            end
        end
        
        local LogHolder = Instance.new("ScreenGui")
        local Logs = Instance.new("Frame")
        local Scroll = Instance.new("ScrollingFrame")
        local Template = Instance.new("TextLabel")
        
        LogHolder.Name = "LogHolder"
        if game:service('RunService'):IsStudio() then LogHolder.Parent = game.Players.LocalPlayer.PlayerGui else
            LogHolder.Parent = game.CoreGui
        end
        
        Logs.Name = "Logs"
        Logs.Parent = LogHolder
        Logs.AnchorPoint = Vector2.new(0.5, 0.5)
        Logs.BackgroundColor3 = Color3.new(1, 1, 1)
        Logs.Position = UDim2.new(0.200000003, 0, 0.200000003, 0)
        Logs.Size = UDim2.new(0, 400, 0, 250)
        Logs.Style = Enum.FrameStyle.DropShadow
        
        Scroll.Name = "Scroll"
        Scroll.Parent = Logs
        Scroll.BackgroundColor3 = Color3.new(1, 1, 1)
        Scroll.BackgroundTransparency = 1
        Scroll.BorderSizePixel = 0
        Scroll.Size = UDim2.new(1, 0, 1, 0)
        Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
        Scroll.ScrollBarThickness = 6
        
        Template.Name = "Template"
        Template.Parent = Logs
        Template.BackgroundColor3 = Color3.new(1, 1, 1)
        Template.BackgroundTransparency = 1
        Template.Position = UDim2.new(0, 0, 0, -25)
        Template.Size = UDim2.new(1, 0, 0, 20)
        Template.Font = Enum.Font.ArialBold
        Template.Text = ""
        Template.TextColor3 = Color3.new(1, 1, 1)
        Template.TextSize = 15
        Template.TextXAlignment = Enum.TextXAlignment.Left
        Template.TextWrap = true
        
        Logs.Active = true
        Logs.Draggable = true
        
        local loggedTable = {}
        
        local getTotalSize = function()
        local totalSize = UDim2.new(0, 0, 0, 0)
            
            for i, v in next, loggedTable do
                totalSize = totalSize + UDim2.new(0, 0, 0, v.Size.Y.Offset)
            end
            
            return totalSize
        end
        
        local BUD = UDim2.new(0, 0, 0, 0)
        local TotalNum = 0
        
        local function GenLog(txt, colo, time)
            local oldColo = Color3.fromRGB(0, 0, 0)	
            
            local Temp = Template:Clone()
            Temp.Parent = Scroll
            Temp.Name = txt..'Logged'
            Temp.Text = tostring(txt)
            Temp.Visible = true
            Temp.Position = BUD + UDim2.new(0, 0, 0, 0)
            if colo then oldColo = colo Temp.TextColor3 = colo elseif not colo then Temp.TextColor3 = Color3.fromRGB(200, 200, 200) end
        
            local timeVal = Instance.new('StringValue', Temp)
            timeVal.Name = 'TimeVal'
            timeVal.Value = time
        
            TotalNum = TotalNum + 1
            
            if not Temp.TextFits then repeat Temp.Size = UDim2.new(Temp.Size.X.Scale, Temp.Size.X.Offset, Temp.Size.Y.Scale, Temp.Size.Y.Offset + 10)
                Temp.Text = txt
            until Temp.TextFits 
        end
        
            BUD = BUD + UDim2.new(0, 0, 0, Temp.Size.Y.Offset)
            
            table.insert(loggedTable, Temp)
            
            local totSize = getTotalSize()
            
            if totSize.Y.Offset >= Scroll.CanvasSize.Y.Offset then Scroll.CanvasSize = UDim2.new(totSize.X.Scale, totSize.X.Offset, totSize.Y.Scale, totSize.Y.Offset + 100)
            Scroll.CanvasPosition = Scroll.CanvasPosition + Vector2.new(0, totSize.Y.Offset) 
            end
            
            return Temp
        end
        
        local ChatData = ""
        
        local function SaveToFile()
            local t = os.date("*t")
            local dateDat = t['hour']..' '..t['min']..' '..t['sec']..' '..t['day']..'.'..t['month']..'.'..t['year']
            
            ChatData = ""
            
            for i, v in pairs(Scroll:GetChildren()) do
                ChatData = ChatData..v.TimeVal.Value..' '..v.Text..'\n'
            end
            
            writefile('ChatLogs '..dateDat..'.txt', ChatData)
        end
        
        
        local function Clear()
            loggedTable = {}
            ChatData = ""
            Scroll.CanvasPosition = Vector2.new(0, 0)
            for i, v in pairs(Scroll:GetChildren()) do
                v:Destroy()
            end
            Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
            BUD = UDim2.new(0, 0, 0, 0)
        end
        
        local LogPlr = function(plr)
                    plr.Chatted:connect(function(msg)
                        
                    local t = os.date("*t")
                    local dateDat = t['hour']..':'..t['min']..':'..t['sec']
            
                    if string.len(msg) >= 1000 then return nil end
                    if string.lower(msg) == 'clear' and plr == game:service('Players').LocalPlayer then Clear() return nil end
                    if string.lower(msg) == 'savetofile' and plr == game:service('Players').LocalPlayer then SaveToFile() return nil end
                    if string.sub(msg, 1, 1):match('%p') and string.sub(msg, 2, 2):match('%a') and string.len(msg) >= 5 then GenLog(plr.Name..': '..msg, Color3.new(255, 0, 0), dateDat) else
                    GenLog(plr.Name..': '..msg, Color3.new(255, 255, 255), dateDat)
                    end
            end)
        end
        
        for i, v in pairs(game.Players:GetChildren()) do
            LogPlr(v)
        end
        
        game.Players.PlayerAdded:connect(function(plr)
            LogPlr(plr)
        end)
	end
})
------- Last looted timers
local crock = workspace.MonsterSpawns.Triggers.CastleRockSnake:FindFirstChild("LastSpawned")
local sunken = workspace.MonsterSpawns.Triggers.evileye1:FindFirstChild("LastSpawned")
local tundra2 = Workspace.MonsterSpawns.Triggers.MazeSnakes:FindFirstChild("LastSpawned")
local minutes = " Minutes Ago"

local FFLabel = TimersSection:Label({
    Name = "Label", -- String
    })
    
    local FLabel = TimersSection:Label({
    Name = "Label", -- String
    })
    
    local FFFLabel = TimersSection:Label({
    Name = "Label", -- String
    })

    while true do
        if FFLabel then
            FFLabel:SetName("Crock Last Looted: " .. tostring(math.floor((os.time() -  crock.Value) / 60)) .. minutes)
            FLabel:SetName("Sunken Last Looted: " .. tostring(math.floor((os.time() -  sunken.Value) / 60)) .. minutes)
            FFFLabel:SetName("Tunda 2 Last Looted: " .. tostring(math.floor((os.time() -  tundra2.Value) / 60)) .. minutes)
        end
        wait(60)
     end   
------- ENDS SCRIPT 
end)
