local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("NOTINFECTED HUB (mm2)", "BloodTheme")
local mainTab = Window:NewTab("Main")
local othersTab = Window:NewTab("OthersTab")
local othersSection = othersTab:NewSection("...")
local autofarmSection = mainTab:NewSection("Autofarm")
local universalSection = mainTab:NewSection("Universal")
local plr = game.Players.LocalPlayer
--toggles and variables
_G.autofarm_toggle = false
_G.autofarm_speed = 15
_G.autofarm_cooldown = 0.4
_G.walkspeed = 16
_G.jumppower = 50
_G.autofarm_xp = false
_G.autofarm_done = false
_G.autograb_gun = false
_G.fling_counter = tick()
--configs
local function getCurrentMap()
    for _,value in pairs(workspace:GetChildren()) do
        if value:IsA("Model") and value:FindFirstChild("CoinContainer") then
            return value
        end
    end
end
local function getPlayerCharacter()
    if plr.Character then
        local char = plr.Character
        if char:FindFirstChildWhichIsA("Humanoid") and char.PrimaryPart then
            return char
        else return nil end
    else return nil end
end
local function getMurderer() 
    for _,player in game.Players:GetPlayers() do
        if player then
            if player.Character and player.Backpack:FindFirstChild("Knife") then
                return {player.Character,player,player.Backpack.Knife}
            elseif player.Character and player.Character:FindFirstChild("Knife") then
                return {player.Character,player,player.Character.Knife}
            end
        end
    end
    return nil
end
local function getCoin()
    local map = getCurrentMap()
    local char = getPlayerCharacter()
    local coin = nil
    if map and char then
        local distance = math.huge
        for _,e in map.CoinContainer:GetChildren() do
            if e and e:FindFirstChild("CoinVisual") and e.CoinVisual.Transparency==0 then
                if (char.PrimaryPart.Position-e.Position).Magnitude < distance  then
                    distance = (char.PrimaryPart.Position-e.Position).Magnitude
                    coin = e
                end
            end
        end
    end
    return coin
end


local function SkidFling(TargetPlayer)
    local Character = getPlayerCharacter()
    local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")
    local RootPart = Humanoid and Humanoid.RootPart

    local TCharacter = TargetPlayer.Character
    local THumanoid = TCharacter and TCharacter:FindFirstChildOfClass("Humanoid")
    local TRootPart = THumanoid and THumanoid.RootPart
    local THead = TCharacter and TCharacter:FindFirstChild("Head")
    local Accessory = TCharacter and TCharacter:FindFirstChildOfClass("Accessory")
    local Handle = Accessory and Accessory:FindFirstChild("Handle")

    if Character and Humanoid and RootPart then
        if RootPart.Velocity.Magnitude < 50 then
            getgenv().OldPos = RootPart.CFrame
        end
        if THumanoid and THumanoid.Sit then
            return
        end
        if THead then
            workspace.CurrentCamera.CameraSubject = THead
        elseif Handle then
            workspace.CurrentCamera.CameraSubject = Handle
        else
            workspace.CurrentCamera.CameraSubject = THumanoid
        end
        if not TCharacter:FindFirstChildWhichIsA("BasePart") then
            return
        end
        
        local function FPos(BasePart, Pos, Ang)
            RootPart.CFrame = CFrame.new(BasePart.Position) * Pos * Ang
            Character:SetPrimaryPartCFrame(CFrame.new(BasePart.Position) * Pos * Ang)
            RootPart.Velocity = Vector3.new(9e7, 9e7 * 10, 9e7)
            RootPart.RotVelocity = Vector3.new(9e8, 9e8, 9e8)
        end
        
        local function SFBasePart(BasePart)
            local TimeToWait = 2
            local Time = tick()
            local Angle = 0

            repeat
                if RootPart and THumanoid then
                    if BasePart.Velocity.Magnitude < 50 then
                        Angle = Angle + 100

                        FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle),0 ,0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(2.25, 1.5, -2.25) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(-2.25, -1.5, 2.25) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection,CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection,CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()
                    else
                        FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, -THumanoid.WalkSpeed), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()
                        
                        FPos(BasePart, CFrame.new(0, 1.5, TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, -TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5 ,0), CFrame.Angles(math.rad(-90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                        task.wait()
                    end
                else
                    break
                end
            until BasePart.Velocity.Magnitude > 500 or BasePart.Parent ~= TargetPlayer.Character or TargetPlayer.Parent ~= Players or not TargetPlayer.Character == TCharacter or THumanoid.Sit or Humanoid.Health <= 0 or tick() > Time + TimeToWait
        end
        
        workspace.FallenPartsDestroyHeight = 0/0
        
        local BV = Instance.new("BodyVelocity")
        BV.Name = "EpixVel"
        BV.Parent = RootPart
        BV.Velocity = Vector3.new(9e8, 9e8, 9e8)
        BV.MaxForce = Vector3.new(1/0, 1/0, 1/0)
        
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
        
        if TRootPart and THead then
            if (TRootPart.CFrame.p - THead.CFrame.p).Magnitude > 5 then
                SFBasePart(THead)
            else
                SFBasePart(TRootPart)
            end
        elseif TRootPart and not THead then
            SFBasePart(TRootPart)
        elseif not TRootPart and THead then
            SFBasePart(THead)
        elseif not TRootPart and not THead and Accessory and Handle then
            SFBasePart(Handle)
        else
            return Message("Error Occurred", "Target is missing everything", 5)
        end
        
        BV:Destroy()
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, true)
        workspace.CurrentCamera.CameraSubject = Humanoid
        
        repeat
            RootPart.CFrame = getgenv().OldPos * CFrame.new(0, .5, 0)
            Character:SetPrimaryPartCFrame(getgenv().OldPos * CFrame.new(0, .5, 0))
            Humanoid:ChangeState("GettingUp")
            table.foreach(Character:GetChildren(), function(_, x)
                if x:IsA("BasePart") then
                    x.Velocity, x.RotVelocity = Vector3.new(), Vector3.new()
                end
            end)
            task.wait()
        until (RootPart.Position - getgenv().OldPos.p).Magnitude < 25
        workspace.FallenPartsDestroyHeight = getgenv().FPDH
    else
        return 
    end
end

--autofarm
autofarmSection:NewToggle("Autofarm", "ds", function(state)
    _G.autofarm_toggle = state
end)
autofarmSection:NewToggle("Autofarm XP", "sdfdsfa", function(state)
    _G.autofarm_xp = state
end)
autofarmSection:NewSlider("Autofarm Speed", "fd", 30, _G.autofarm_speed, function(s) -- 500 (MaxValue) | 0 (MinValue)
    _G.autofarm_speed = s
end)
autofarmSection:NewToggle("Auto Grab Gun", "sdfdsfa", function(state)
    _G.autograb_gun = state
end)
universalSection:NewSlider("WalkSpeed", "fd", 25, 16, function(s) -- 500 (MaxValue) | 0 (MinValue)
    _G.walkspeed = s
end)
universalSection:NewSlider("Jump power", "fd", 100, 50, function(s) -- 500 (MaxValue) | 0 (MinValue)
    _G.jumppower = s
end)
universalSection:NewButton("Teleport to map", ":)", function()
    local map = getCurrentMap()
    local char = getPlayerCharacter()
    if map and char then
        char.PrimaryPart.CFrame = map.Spawns:GetChildren()[math.random(1,#map.Spawns:GetChildren())].CFrame * CFrame.new(0,1,0)
    end
end)
universalSection:NewButton("Teleport to lobby", ":)", function()
    local lobby = workspace.Lobby
    local char = getPlayerCharacter()
    if lobby and char then
        char.PrimaryPart.CFrame = lobby.Spawns:GetChildren()[math.random(1,#lobby.Spawns:GetChildren())].CFrame * CFrame.new(0,1,0)
    end
end)
--others
othersSection:NewLabel("Script made by RobloxIs_GoodBoy")
othersSection:NewButton("Copy discord server link", "Thanks :DDDD", function()
    setclipboard("https://discord.gg/xqRBN8a79Y")
end)
othersSection:NewKeybind("Toggle UI", "sdfsdfssdf", Enum.KeyCode.LeftAlt, function()
	Library:ToggleUI()
end)
--check
game:GetService("RunService").RenderStepped:Connect(function ()
    local char = getPlayerCharacter()
    if char then
        char.Humanoid.WalkSpeed = _G.walkspeed
        char.Humanoid.JumpPower = _G.jumppower
        for _,v in pairs(char:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
        --autofarm xp time
        if _G.autofarm_done and _G.autofarm_xp then
            --either game ended or player collected full coins, so we need to check if game ended by checking murderer's existance
            local murderer = getMurderer()
            if murderer then
                if char == murderer[1] then
                    --player IS THE MURDERER
                    --we try to simulate?
                    local knife = murderer[3]
                    char.Humanoid:EquipTool(knife)
                    for _,player in game.Players:GetPlayers() do
                        if player.Character and plr.Character.PrimaryPart and player~=plr then
                            local prim = player.Character.PrimaryPart
                            prim.CFrame = char.PrimaryPart.CFrame*CFrame.new(0,0,-0.5)
                        end
                    end
                    knife:Activate()
                else 
                    --murderer didnt die
                    if tick() - _G.fling_counter > 10 then
                        if tick()-_G.fling_counter > 15 then
                            _G.fling_counter = tick()
                            local map = getCurrentMap()
                            local char = getPlayerCharacter()
                            if map and char then
                                char.PrimaryPart.CFrame = map.Spawns:GetChildren()[math.random(1,#map.Spawns:GetChildren())].CFrame * CFrame.new(0,1,0)
                            end
                        end
                        SkidFling(murderer[2])
                    end
                    
                end
            end
        end
        --autograb gun
        local map = getCurrentMap()
        if map then
            if _G.autograb_gun and map:FindFirstChild("GunDrop") then
                char.PrimaryPart.CFrame = map.GunDrop.CFrame
            end
        end
    end
end)

while task.wait(_G.autofarm_cooldown) do
    --autofarm
    local char = getPlayerCharacter()
    if char and _G.autofarm_toggle then
        
        local map = getCurrentMap()
        if map then
            local coinContainer = map.CoinContainer
            if #coinContainer:GetChildren() > 0 then
                local coinContainer_Table = coinContainer:GetChildren()
				local thisCoin = getCoin()
                if thisCoin then
                    _G.autofarm_done = false
                    if (thisCoin.Position-char.PrimaryPart.Position).Magnitude > 500 then
                        char.PrimaryPart.CFrame = thisCoin.CFrame
                    end
                    local lolno = tick()
                    local thisTween = game:GetService("TweenService"):Create(char.PrimaryPart,TweenInfo.new((thisCoin.Position-char.PrimaryPart.Position).Magnitude/_G.autofarm_speed),{CFrame = thisCoin.CFrame*CFrame.new(0,1,0)})
                    thisTween:Play()
                    repeat
                        task.wait()
                    until tick()-lolno>20 or thisTween.Completed or (not map)
                else
                    _G.autofarm_done = true
                end
            else
                _G.autofarm_done = false
            end
        else
            _G.autofarm_done = false
        end
    elseif char then
    end
end
