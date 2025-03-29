local environment, replicatedstorage, players, net, client, modulepath, characterfolder, enemyfolder do 
    environment = (getgenv or getrenv or getfenv)() 
    replicatedstorage = game:GetService("ReplicatedStorage") 
    players = game:GetService("Players") 
    client = players.LocalPlayer 
    modulepath = replicatedstorage:WaitForChild("Modules") 
    net = modulepath:WaitForChild("Net") 
    characterfolder = workspace:WaitForChild("Characters") 
    enemyfolder = workspace:WaitForChild("Enemies") 
end

local RS = game:GetService("ReplicatedStorage")
local regAtk = RS.Modules.Net:FindFirstChild("RE/RegisterAttack")
local regHit = RS.Modules.Net:FindFirstChild("RE/RegisterHit")

local environment, replicatedstorage, players, client do
    environment = (getgenv or getrenv or getfenv)()
    replicatedstorage = game:GetService("ReplicatedStorage")
    players = game:GetService("Players")
    client = players.LocalPlayer
end

local modulepath = replicatedstorage:WaitForChild("Modules")
local net = modulepath:WaitForChild("Net")
local registerAttack = net:WaitForChild("RE/RegisterAttack")
local registerHit = net:WaitForChild("RE/RegisterHit")
local characterfolder = workspace:WaitForChild("Characters")
local enemyfolder = workspace:WaitForChild("Enemies")

local Module = {} 
Module.AttackCooldown = .0 
local CachedChars = {}

function Module.IsAlive(Char) 
    if not Char then return nil end 
    if CachedChars[Char] then return CachedChars[Char].Health > 0 end 
    local Hum = Char:FindFirstChildOfClass("Humanoid") 
    CachedChars[Char] = Hum 
    return Hum and Hum.Health > 0 
end

local Settings = { 
    ClickDelay = .0, 
    AutoClick = true 
}

Module.FastAttack = (function() 
    if environment._trash_attack then return environment._trash_attack end 
    local module = { 
        NextAttack = 0, 
        Distance = 100, 
        attackMobs = true, 
        attackPlayers = false 
    } 
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack") 
    local RegisterHit = net:WaitForChild("RE/RegisterHit")

    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function module:AttackNearest()
        local args = { nil, {} }
        for _, Enemy in ipairs(enemyfolder:GetChildren()) do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso")
                else
                    table.insert(args[2], { [1] = Enemy, [2] = Enemy:FindFirstChild("UpperTorso") })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Enemy in ipairs(characterfolder:GetChildren()) do
            if Enemy ~= client.Character then 
                self:AttackEnemy(Enemy:FindFirstChild("UpperTorso")) 
            end
        end
        if not self.FirstAttack then task.wait(.0) end
    end

    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = true
    end

    task.spawn(function()
        while true do
            if Settings.AutoClick and Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                module:BladeHits()
            end
            task.wait(.0)
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function zXy9(player)
    local lst = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj2, obj2.HumanoidRootPart})
        end
    end

    return lst
end

local yZn34 = false
spawn(function()
    while true do
        if Xz12 then
            yZn34 = true
            wait(.0)
        else
            yZn34 = false
            wait(.0)
        end
        if Xz12 then
            local cLst = zXy9(game.Players.LocalPlayer)
            if #cLst > 0 then
                regAtk:FireServer(.0)
                for _, tgt in next, cLst do
                    regHit:FireServer(cLst[_][2], cLst)
                end
            end
        end
    end
end)
Xz12 = true
spawn(function()
    while true do
        task.wait(.0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
                        
                        local args = {
                            [1] = v:FindFirstChild("RightHand"),
                            [2] = {}
                        }
                        
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)
while true do task.wait(.0)
for i, v in next, workspace.Enemies:GetChildren() do
        if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
            local args = {
                [1] = v:FindFirstChild("RightHand"),
                [2] = {}
            }
            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    table.insert(args[2], {
                        [1] = e,
                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                    })
                end
            end
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
        end
    end
end

local Module = {
    AttackCooldown = 0.000000007
}
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
    if not Char then return nil end
    local Hum = CachedChars[Char] or Char:FindFirstChildOfClass("Humanoid")
    if Hum then
        CachedChars[Char] = Hum
        return Hum.Health > 0
    end
    return false
end

local Settings = {
    ClickDelay = 0.000000007,
    AutoClick = true
}

Module.FastAttack = (function()
    if not _G['Fast Attack'] then return end
    if environment._trash_attack then return environment._trash_attack end

    local AttackModule = {
        NextAttack = 0,
        Distance = 123,
        attackMobs = true,
        attackPlayers = true
    }

    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")

    function AttackModule:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(Settings.ClickDelay or 0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function AttackModule:AttackNearest()
        local args = {nil, {}}
        for _, Enemy in ipairs(enemyFolder:GetChildren()) do
            local humanoidPart = Enemy:FindFirstChild("HumanoidRootPart")
            if humanoidPart and client:DistanceFromCharacter(humanoidPart.Position) < self.Distance then
                local upperTorso = Enemy:FindFirstChild("UpperTorso")
                if not args[1] then
                    args[1] = upperTorso
                else
                    table.insert(args[2], {Enemy, upperTorso})
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Char in ipairs(characterfolder:GetChildren()) do
            if Char ~= client.Character then
                self:AttackEnemy(Char:FindFirstChild("UpperTorso"))
            end
        end
        if not self.FirstAttack then
            task.wait(0.000000007)
        end
    end

    function AttackModule:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while task.wait(0.000000007) do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            AttackModule:BladeHits()
        end
    end)
    environment._trash_attack = AttackModule
    return AttackModule
end)()

local function zXy9(player)
    local lst = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj2, obj2.HumanoidRootPart})
        end
    end

    return lst
end

local yZn34 = false
spawn(function()
    while true do
        if Xz12 then
            yZn34 = true
            wait(0.000000007)
        else
            yZn34 = false
            wait(0.000000007)
        end
        if Xz12 then
            local cLst = zXy9(game.Players.LocalPlayer)
            if #cLst > 0 then
                regAtk:FireServer(0.000000007)
                for _, tgt in next, cLst do
                    regHit:FireServer(cLst[_][2], cLst)
                end
            end
        end
    end
end)
Xz12 = true
spawn(function()
    while true do
        task.wait(0.000000007)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.000000007)
                        
                        local args = {
                            [1] = v:FindFirstChild("RightHand"),
                            [2] = {}
                        }
                        
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)
while true do task.wait(0.000000007)
for i, v in next, workspace.Enemies:GetChildren() do
        if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.000000007)
            local args = {
                [1] = v:FindFirstChild("RightHand"),
                [2] = {}
            }
            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    table.insert(args[2], {
                        [1] = e,
                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                    })
                end
            end
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
        end
    end
end

local Module = {
    AttackCooldown = tick()
}
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
    if not Char then return nil end
    local Hum = CachedChars[Char] or Char:FindFirstChildOfClass("Humanoid")
    if Hum then
        CachedChars[Char] = Hum
        return Hum.Health > 0
    end
    return false
end

local Settings = {
    ClickDelay = 0.01,
    AutoClick = true
}

Module.FastAttack = (function()
    if not _G['Fast Attack'] then return end
    if environment._trash_attack then return environment._trash_attack end

    local AttackModule = {
        NextAttack = 0,
        Distance = 123,
        attackMobs = true,
        attackPlayers = true
    }

    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")

    function AttackModule:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(Settings.ClickDelay or 0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function AttackModule:AttackNearest()
        local args = {nil, {}}
        for _, Enemy in ipairs(enemyFolder:GetChildren()) do
            local humanoidPart = Enemy:FindFirstChild("HumanoidRootPart")
            if humanoidPart and client:DistanceFromCharacter(humanoidPart.Position) < self.Distance then
                local upperTorso = Enemy:FindFirstChild("UpperTorso")
                if not args[1] then
                    args[1] = upperTorso
                else
                    table.insert(args[2], {Enemy, upperTorso})
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Char in ipairs(characterfolder:GetChildren()) do
            if Char ~= client.Character then
                self:AttackEnemy(Char:FindFirstChild("UpperTorso"))
            end
        end
        if not self.FirstAttack then
            task.wait(0.01)
        end
    end

    function AttackModule:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while task.wait(0.01) do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            AttackModule:BladeHits()
        end
    end)
    environment._trash_attack = AttackModule
    return AttackModule
end)()

local function zXy9(player)
    local lst = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(lst, {obj2, obj2.HumanoidRootPart})
        end
    end

    return lst
end

local yZn34 = false
spawn(function()
    while true do
        if Xz12 then
            yZn34 = true
            wait(0.01)
        else
            yZn34 = false
            wait(0.01)
        end
        if Xz12 then
            local cLst = zXy9(game.Players.LocalPlayer)
            if #cLst > 0 then
                regAtk:FireServer(0.01)
                for _, tgt in next, cLst do
                    regHit:FireServer(cLst[_][2], cLst)
                end
            end
        end
    end
end)
Xz12 = true
spawn(function()
    while true do
        task.wait(0.01)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                        
                        local args = {
                            [1] = v:FindFirstChild("RightHand"),
                            [2] = {}
                        }
                        
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)
while true do task.wait(0.01)
for i, v in next, workspace.Enemies:GetChildren() do
        if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
            local args = {
                [1] = v:FindFirstChild("RightHand"),
                [2] = {}
            }
            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    table.insert(args[2], {
                        [1] = e,
                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                    })
                end
            end
            game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
        end
    end
end
local Module = {}
Module.AttackCooldown = 0.07
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
    if not Char then return nil end
    if CachedChars[Char] then return CachedChars[Char].Health > 0 end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = 0.07,
    AutoClick = true
}

Module.FastAttack = (function()
    if environment._trash_attack then return environment._trash_attack end
    
    local module = {
        NextAttack = 0,
        Distance = 150,
        attackMobs = true,
        attackPlayers = true
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            RegisterAttack:FireServer(0.07)
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {nil, {}}
        for _, Enemy in ipairs(enemyfolder:GetChildren()) do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso")
                else
                    table.insert(args[2], {Enemy, Enemy:FindFirstChild("UpperTorso")})
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Enemy in ipairs(characterfolder:GetChildren()) do
            if Enemy ~= client.Character then
                self:AttackEnemy(Enemy:FindFirstChild("UpperTorso"))
            end
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
    end
    
    task.spawn(function()
        local RunService = game:GetService("RunService")
        while true do
            RunService.Heartbeat:Wait()
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local CachedChars = {}
local function IsAlive(Char)
    if not Char then
        return false
    end

    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end

    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local function FindNearbyTargets(player, distance)
    local targets = {}
    
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    return targets
end

spawn(function()
    while true do task.wait(0)
        if _G['Fast Attack'] then
            pcall(function()
                for _, enemy in next, workspace.Enemies:GetChildren() do
                    if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
                       enemy:FindFirstChild("HumanoidRootPart") and 
                       (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        registerAttack:FireServer(0)
                        local hitPart = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                        local args = {
                            [1] = hitPart,
                            [2] = {}
                        }
                        for _, target in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = target,
                                    [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        registerHit:FireServer(unpack(args))
                    end
                end
            end)
        end
    end
end)

spawn(function()
    while true do wait(0)
        if _G['Fast Attack'] then
            local nearbyTargets = FindNearbyTargets(client, 200)
            if #nearbyTargets > 0 then
                registerAttack:FireServer(0)
                for _, target in next, nearbyTargets do
                    registerHit:FireServer(target[2], nearbyTargets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        if not IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        for _, enemy in next, workspace.Enemies:GetChildren() do
            if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
               enemy:FindFirstChild("HumanoidRootPart") and 
               (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                registerAttack:FireServer(0)
                local hitPart = enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                local hitTargets = {}
                for _, target in next, workspace.Enemies:GetChildren() do
                    if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                        table.insert(hitTargets, {
                            [1] = target,
                            [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                registerHit:FireServer(hitPart, hitTargets)
                break
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    NoAttackAnimation = false
}

local function FindAttackableTargets(player)
    local targetList = {}
    
    for _, obj in pairs(characterfolder:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChildOfClass("Humanoid") and
           obj:FindFirstChildOfClass("Humanoid").Health > 0 and
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    return targetList
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = 0,
        Distance = 65,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = false,
        CurrentAllMob = {},
        canHits = {}
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local Humanoid = Enemy:FindFirstChildOfClass("Humanoid")
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            if HRP and Humanoid and Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character then
                    local Player = players:GetPlayerFromCharacter(Character)
                    if Player and not Player:GetAttribute("PvpDisabled") then
                        self:AttackEnemy(Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart"))
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module.CurrentAllMob = {}
            module.canHits = {}
            local attackables = FindAttackableTargets(client)
            for _, target in pairs(attackables) do
                table.insert(module.CurrentAllMob, target[1])
                table.insert(module.canHits, target[2])
            end
            if #module.canHits > 0 then
                module:BladeHits()
            end
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChildOfClass("Humanoid") and v:FindFirstChildOfClass("Humanoid").Health > 0 
                       and v:FindFirstChild("HumanoidRootPart") 
                       and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChildOfClass("Humanoid") and e:FindFirstChildOfClass("Humanoid").Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local Priority = {
    Class = "Combat",
    Recently = "Attack",
    Activity = true
}

Priority.clear = function()
    Priority.Recently = "None"
    Priority.Activity = false
end

local AttackSignal = Instance.new("BindableEvent")
task.spawn(function()
    local CollectionService = game:GetService("CollectionService")
    local Client = players.LocalPlayer
    local Char = Client.Character or Client.CharacterAdded:Wait()
    local stacking = 0
    local printCooldown = 0
    local OldPriority = Priority.Recently
    dist = function(a,b,noHeight)
        if not b then
            b = Root.Position
        end
        return (Vector3.new(a.X,not noHeight and a.Y,a.Z) - Vector3.new(b.X,not noHeight and b.Y,b.Z)).magnitude
    end
    local function dist(pos)
        return (pos - Char.HumanoidRootPart.Position).Magnitude
    end
    while wait(.075) do
        local nearbymon = false
        table.clear(Module.FastAttack.CurrentAllMob)
        table.clear(Module.FastAttack.canHits)
        local mobs = CollectionService:GetTagged("ActiveRig")
        for i=1,#mobs do local v = mobs[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.Health > 0 and Human.RootPart and v ~= Char then
                local IsPlayer = players:GetPlayerFromCharacter(v)
                local IsAlly = IsPlayer and CollectionService:HasTag(IsPlayer,"Ally"..Client.Name)
                if not IsAlly then
                    Module.FastAttack.CurrentAllMob[#Module.FastAttack.CurrentAllMob + 1] = v
                    if not nearbymon and dist(Human.RootPart.Position) < 65 then
                        nearbymon = true
                    end
                end
            end
        end
        if nearbymon then
            local Enemies = workspace.Enemies:GetChildren()
            local Players = players:GetPlayers()
            for i=1,#Enemies do local v = Enemies[i]
                local Human = v:FindFirstChildOfClass("Humanoid")
                if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                    Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                end
            end
            for i=1,#Players do local v = Players[i].Character
                if not Players[i]:GetAttribute("PvpDisabled") and v and v ~= Client.Character then
                    local Human = v:FindFirstChildOfClass("Humanoid")
                    if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                        Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                    end
                end
            end
        end
        if OldPriority ~= Priority.Recently then
            OldPriority = Priority.Recently
            stacking = tick()
        end
        if tick() - stacking > 60 and OldPriority and OldPriority.Class == Priority.Class then
            Priority:clear()
        elseif tick() - printCooldown > 5 then
            printCooldown = tick()
        end
    end
end)

local CachedChars = {}
local function IsAlive(Char)
    if not Char then
        return false
    end

    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end

    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local function FindNearbyTargets(player, distance)
    local targets = {}
    
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    return targets
end

spawn(function()
    while true do task.wait(0.0000003)
        if _G['Fast Attack'] then
            pcall(function()
                for _, enemy in next, workspace.Enemies:GetChildren() do
                    if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
                       enemy:FindFirstChild("HumanoidRootPart") and 
                       (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        registerAttack:FireServer(0.0000003)
                        local hitPart = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                        local args = {
                            [1] = hitPart,
                            [2] = {}
                        }
                        for _, target in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = target,
                                    [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        registerHit:FireServer(unpack(args))
                    end
                end
            end)
        end
    end
end)

spawn(function()
    while true do wait(0.0000003)
        if _G['Fast Attack'] then
            local nearbyTargets = FindNearbyTargets(client, 200)
            if #nearbyTargets > 0 then
                registerAttack:FireServer(0.0000003)
                for _, target in next, nearbyTargets do
                    registerHit:FireServer(target[2], nearbyTargets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0.0000003)
        if not IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        for _, enemy in next, workspace.Enemies:GetChildren() do
            if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
               enemy:FindFirstChild("HumanoidRootPart") and 
               (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                registerAttack:FireServer(0.0000003)
                local hitPart = enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                local hitTargets = {}
                for _, target in next, workspace.Enemies:GetChildren() do
                    if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                        table.insert(hitTargets, {
                            [1] = target,
                            [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                registerHit:FireServer(hitPart, hitTargets)
                break
            end
        end
    end
end)

local Camera = nil
pcall(function()
    Camera = require(game.ReplicatedStorage.Util.CameraShaker)
    if Camera then
        Camera:Stop()
    end
end)

local Module = {}
Module.AttackCooldown = 0
local CachedChars = {}
local time_all_x = 0
local time_all_p = 0
local time_x = 0
local time_xeg = 0

function Module.IsAlive(Char)
    if not Char then
        return false
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.GetAllHits(Sizes)
    local Hits = {}
    local Client = game.Players.LocalPlayer
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        local Human = v:FindFirstChildOfClass("Humanoid")
        if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
            table.insert(Hits, {v, Human.RootPart})
        end
    end
    for _, v in pairs(workspace.Characters:GetChildren()) do
        if v ~= Client.Character then
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, {v, Human.RootPart})
            end
        end
    end
    return Hits
end

local RegisterAttack = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
local RegisterHit = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")

function Module.AttackNoCD()
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = Module.GetAllHits(80)
        if #targets > 0 then
            RegisterAttack:FireServer(.0)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_x = time_all_x + 1
            if time_x >= 2 then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

function Module.AttackPlayer(targetName)
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = {}
        for _, v in pairs(game.Workspace.Characters:GetChildren()) do
            if v:FindFirstChild('Humanoid') and v.Humanoid.Health > 0 and 
               (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 50 and 
               tostring(v.Name) == targetName then
                table.insert(targets, {v, v.HumanoidRootPart})
            end
        end
        if #targets > 0 then
            RegisterAttack:FireServer(.0)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_p = time_all_p + 1
            if time_x >= 2 and time_all_p >= 150 or game:GetService("Players").LocalPlayer.PlayerGui.Main.SafeZone.Visible == true then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = 0,
        Distance = 80,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or HRP
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or HRP
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Enemy in characterfolder:GetChildren() do
            if Enemy ~= client.Character then
                self:AttackEnemy(Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart"))
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while task.wait() do
        pcall(function()
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Stun") and
               game.Players.LocalPlayer.Character.Stun.Value == 0 then
                Module.AttackNoCD()
                wait(.0)
            else
                wait(.0)
            end
        end)
    end
end)

task.spawn(function()
    while game:GetService("RunService").Stepped:Wait() do
        if (tick() - Module.AttackCooldown) < 0 then continue end
        if not Module.IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        Module.FastAttack:BladeHits()
    end
end)

spawn(function()
    while true do task.wait(.0)
        pcall(function()
            if _G['Fast Attack'] then
                for _, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                       (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        RegisterAttack:FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        RegisterHit:FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    NoAttackAnimation = false
}

local function FindAttackableTargets(player)
    local targetList = {}
    
    for _, obj in pairs(characterfolder:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChildOfClass("Humanoid") and
           obj:FindFirstChildOfClass("Humanoid").Health > 0 and
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    return targetList
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = 0,
        Distance = 65,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = false,
        CurrentAllMob = {},
        canHits = {}
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local Humanoid = Enemy:FindFirstChildOfClass("Humanoid")
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            if HRP and Humanoid and Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character then
                    local Player = players:GetPlayerFromCharacter(Character)
                    if Player and not Player:GetAttribute("PvpDisabled") then
                        self:AttackEnemy(Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart"))
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module.CurrentAllMob = {}
            module.canHits = {}
            local attackables = FindAttackableTargets(client)
            for _, target in pairs(attackables) do
                table.insert(module.CurrentAllMob, target[1])
                table.insert(module.canHits, target[2])
            end
            if #module.canHits > 0 then
                module:BladeHits()
            end
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChildOfClass("Humanoid") and v:FindFirstChildOfClass("Humanoid").Health > 0 
                       and v:FindFirstChild("HumanoidRootPart") 
                       and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChildOfClass("Humanoid") and e:FindFirstChildOfClass("Humanoid").Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local Priority = {
    Class = "Combat",
    Recently = "Attack",
    Activity = true
}

Priority.clear = function()
    Priority.Recently = "None"
    Priority.Activity = false
end

local AttackSignal = Instance.new("BindableEvent")
task.spawn(function()
    local CollectionService = game:GetService("CollectionService")
    local Client = players.LocalPlayer
    local Char = Client.Character or Client.CharacterAdded:Wait()
    local stacking = 0
    local printCooldown = 0
    local OldPriority = Priority.Recently
    dist = function(a,b,noHeight)
        if not b then
            b = Root.Position
        end
        return (Vector3.new(a.X,not noHeight and a.Y,a.Z) - Vector3.new(b.X,not noHeight and b.Y,b.Z)).magnitude
    end
    local function dist(pos)
        return (pos - Char.HumanoidRootPart.Position).Magnitude
    end
    while wait(.075) do
        local nearbymon = false
        table.clear(Module.FastAttack.CurrentAllMob)
        table.clear(Module.FastAttack.canHits)
        local mobs = CollectionService:GetTagged("ActiveRig")
        for i=1,#mobs do local v = mobs[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.Health > 0 and Human.RootPart and v ~= Char then
                local IsPlayer = players:GetPlayerFromCharacter(v)
                local IsAlly = IsPlayer and CollectionService:HasTag(IsPlayer,"Ally"..Client.Name)
                if not IsAlly then
                    Module.FastAttack.CurrentAllMob[#Module.FastAttack.CurrentAllMob + 1] = v
                    if not nearbymon and dist(Human.RootPart.Position) < 65 then
                        nearbymon = true
                    end
                end
            end
        end
        if nearbymon then
            local Enemies = workspace.Enemies:GetChildren()
            local Players = players:GetPlayers()
            for i=1,#Enemies do local v = Enemies[i]
                local Human = v:FindFirstChildOfClass("Humanoid")
                if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                    Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                end
            end
            for i=1,#Players do local v = Players[i].Character
                if not Players[i]:GetAttribute("PvpDisabled") and v and v ~= Client.Character then
                    local Human = v:FindFirstChildOfClass("Humanoid")
                    if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                        Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                    end
                end
            end
        end
        if OldPriority ~= Priority.Recently then
            OldPriority = Priority.Recently
            stacking = tick()
        end
        if tick() - stacking > 60 and OldPriority and OldPriority.Class == Priority.Class then
            Priority:clear()
        elseif tick() - printCooldown > 5 then
            printCooldown = tick()
        end
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    NoAttackAnimation = true
}

local function FindAttackableTargets(player)
    local targetList = {}
    
    for _, obj in pairs(characterfolder:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChildOfClass("Humanoid") and
           obj:FindFirstChildOfClass("Humanoid").Health > 0 and
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    return targetList
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = 0,
        Distance = 65,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = false,
        CurrentAllMob = {},
        canHits = {}
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.001)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local Humanoid = Enemy:FindFirstChildOfClass("Humanoid")
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            if HRP and Humanoid and Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character then
                    local Player = players:GetPlayerFromCharacter(Character)
                    if Player and not Player:GetAttribute("PvpDisabled") then
                        self:AttackEnemy(Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart"))
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(0.001)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module.CurrentAllMob = {}
            module.canHits = {}
            local attackables = FindAttackableTargets(client)
            for _, target in pairs(attackables) do
                table.insert(module.CurrentAllMob, target[1])
                table.insert(module.canHits, target[2])
            end
            if #module.canHits > 0 then
                module:BladeHits()
            end
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0.001)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChildOfClass("Humanoid") and v:FindFirstChildOfClass("Humanoid").Health > 0 
                       and v:FindFirstChild("HumanoidRootPart") 
                       and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChildOfClass("Humanoid") and e:FindFirstChildOfClass("Humanoid").Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local CachedChars = {}
local function IsAlive(Char)
    if not Char then
        return false
    end

    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end

    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local function FindNearbyTargets(player, distance)
    local targets = {}
    
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    return targets
end

spawn(function()
    while true do task.wait(0.0000003)
        if _G['Fast Attack'] then
            pcall(function()
                for _, enemy in next, workspace.Enemies:GetChildren() do
                    if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
                       enemy:FindFirstChild("HumanoidRootPart") and 
                       (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        registerAttack:FireServer(0.0000003)
                        local hitPart = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                        local args = {
                            [1] = hitPart,
                            [2] = {}
                        }
                        for _, target in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = target,
                                    [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        registerHit:FireServer(unpack(args))
                    end
                end
            end)
        end
    end
end)

spawn(function()
    while true do wait(0.0000003)
        if _G['Fast Attack'] then
            local nearbyTargets = FindNearbyTargets(client, 200)
            if #nearbyTargets > 0 then
                registerAttack:FireServer(0.0000003)
                for _, target in next, nearbyTargets do
                    registerHit:FireServer(target[2], nearbyTargets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0.0000003)
        if not IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        for _, enemy in next, workspace.Enemies:GetChildren() do
            if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
               enemy:FindFirstChild("HumanoidRootPart") and 
               (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                registerAttack:FireServer(0.0000003)
                local hitPart = enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                local hitTargets = {}
                for _, target in next, workspace.Enemies:GetChildren() do
                    if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                        table.insert(hitTargets, {
                            [1] = target,
                            [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                registerHit:FireServer(hitPart, hitTargets)
                break
            end
        end
    end
end)

local Camera = nil
pcall(function()
    Camera = require(game.ReplicatedStorage.Util.CameraShaker)
    if Camera then
        Camera:Stop()
    end
end)

local Module = {}
Module.AttackCooldown = 0.0000001
local CachedChars = {}
local time_all_x = 0
local time_all_p = 0
local time_x = 0
local time_xeg = 0

function Module.IsAlive(Char)
    if not Char then
        return false
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.GetAllHits(Sizes)
    local Hits = {}
    local Client = game.Players.LocalPlayer
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        local Human = v:FindFirstChildOfClass("Humanoid")
        if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
            table.insert(Hits, {v, Human.RootPart})
        end
    end
    for _, v in pairs(workspace.Characters:GetChildren()) do
        if v ~= Client.Character then
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, {v, Human.RootPart})
            end
        end
    end
    return Hits
end

local RegisterAttack = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
local RegisterHit = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")

function Module.AttackNoCD()
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = Module.GetAllHits(80)
        if #targets > 0 then
            RegisterAttack:FireServer(0.0000001)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_x = time_all_x + 1
            if time_x >= 2 then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

function Module.AttackPlayer(targetName)
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = {}
        for _, v in pairs(game.Workspace.Characters:GetChildren()) do
            if v:FindFirstChild('Humanoid') and v.Humanoid.Health > 0 and 
               (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 50 and 
               tostring(v.Name) == targetName then
                table.insert(targets, {v, v.HumanoidRootPart})
            end
        end
        if #targets > 0 then
            RegisterAttack:FireServer(0.0000001)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_p = time_all_p + 1
            if time_x >= 2 and time_all_p >= 150 or game:GetService("Players").LocalPlayer.PlayerGui.Main.SafeZone.Visible == true then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = 0,
        Distance = 80,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.0000001)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or HRP
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or HRP
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Enemy in characterfolder:GetChildren() do
            if Enemy ~= client.Character then
                self:AttackEnemy(Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart"))
            end
        end
        if not self.FirstAttack then
            task.wait(0.0000001)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while task.wait() do
        pcall(function()
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Stun") and
               game.Players.LocalPlayer.Character.Stun.Value == 0 then
                Module.AttackNoCD()
                wait(0.0000001)
            else
                wait(0.0000001)
            end
        end)
    end
end)

task.spawn(function()
    while game:GetService("RunService").Stepped:Wait() do
        if (tick() - Module.AttackCooldown) < 0 then continue end
        if not Module.IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        Module.FastAttack:BladeHits()
    end
end)

spawn(function()
    while true do task.wait(0.0000001)
        pcall(function()
            if _G['Fast Attack'] then
                for _, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                       (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        RegisterAttack:FireServer(0.0000001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        RegisterHit:FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local CachedChars = {}
local function IsAlive(Char)
    if not Char then
        return false
    end

    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end

    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local function FindNearbyTargets(player, distance)
    local targets = {}
    
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    return targets
end

spawn(function()
    while true do task.wait(0.0000003)
        if _G['Fast Attack'] then
            pcall(function()
                for _, enemy in next, workspace.Enemies:GetChildren() do
                    if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
                       enemy:FindFirstChild("HumanoidRootPart") and 
                       (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        registerAttack:FireServer(0.0000003)
                        local hitPart = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                        local args = {
                            [1] = hitPart,
                            [2] = {}
                        }
                        for _, target in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = target,
                                    [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        registerHit:FireServer(unpack(args))
                    end
                end
            end)
        end
    end
end)

spawn(function()
    while true do wait(0.0000003)
        if _G['Fast Attack'] then
            local nearbyTargets = FindNearbyTargets(client, 200)
            if #nearbyTargets > 0 then
                registerAttack:FireServer(0.0000003)
                for _, target in next, nearbyTargets do
                    registerHit:FireServer(target[2], nearbyTargets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0.0000003)
        if not IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        for _, enemy in next, workspace.Enemies:GetChildren() do
            if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
               enemy:FindFirstChild("HumanoidRootPart") and 
               (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                registerAttack:FireServer(0.0000003)
                local hitPart = enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                local hitTargets = {}
                for _, target in next, workspace.Enemies:GetChildren() do
                    if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                        table.insert(hitTargets, {
                            [1] = target,
                            [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                registerHit:FireServer(hitPart, hitTargets)
                break
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

function CurrentWeapon()
	if not client.Character then return nil end
	return client.Character:FindFirstChildOfClass("Tool")
end

function getAllBladeHitsPlayers(Sizes)
	local Hits = {}
	for _, v in pairs(characterfolder:GetChildren()) do
		if v.Name ~= client.Name and v:FindFirstChildOfClass("Humanoid") and 
		   v:FindFirstChild("HumanoidRootPart") and 
		   v:FindFirstChildOfClass("Humanoid").Health > 0 and 
		   client:DistanceFromCharacter(v.HumanoidRootPart.Position) < Sizes+5 then
			table.insert(Hits, {
				[1] = v,
				[2] = v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart")
			})
		end
	end
	return Hits
end

function getAllBladeHits(Sizes)
	local Hits = {}
	for _, v in pairs(enemyfolder:GetChildren()) do
		if v:FindFirstChildOfClass("Humanoid") and 
		   v:FindFirstChild("HumanoidRootPart") and 
		   v:FindFirstChildOfClass("Humanoid").Health > 0 and 
		   client:DistanceFromCharacter(v.HumanoidRootPart.Position) < Sizes+5 then
			table.insert(Hits, {
				[1] = v,
				[2] = v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart")
			})
		end
	end
	return Hits
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    AttackDistance = 60
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end

    local module = {
        NextAttack = 0,
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = false
    }

    function module:DamageAura()
        local enemyHits = getAllBladeHits(150)
        local playerHits = getAllBladeHitsPlayers(150)
        if (#enemyHits > 0 or #playerHits > 0) and CurrentWeapon() then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            local targetHit = nil
            if #enemyHits > 0 then
                targetHit = enemyHits[1][2]
            elseif #playerHits > 0 then
                targetHit = playerHits[1][2]
            end
            if targetHit then
                local allTargets = {}
                for _, enemy in pairs(enemyHits) do
                    table.insert(allTargets, enemy)
                end
                for _, player in pairs(playerHits) do
                    table.insert(allTargets, player)
                end
                RegisterHit:FireServer(targetHit, allTargets)
            end
        else
            self.FirstAttack = false
        end
    end

    function module:AttackFunction()
        local enemyHits = getAllBladeHits(60)
        if #enemyHits > 0 and CurrentWeapon() then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(enemyHits[1][2], enemyHits)
        else
            self.FirstAttack = false
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(args[1], args[2])
        for _, Enemy in characterfolder:GetChildren() do
            if Enemy ~= client.Character then
                self:AttackEnemy(
                    Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart"),
                    {}
                )
            end
        end
        if not self.FirstAttack then
            task.wait(.3)
        end
    end

    function module:AttackEnemy(EnemyPart, Table)
        if EnemyPart and client:DistanceFromCharacter(EnemyPart.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyPart, Table or {})
        end
    end

    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if _G['Fast Attack'] then
                if (tick() - Module.AttackCooldown) < 0 then continue end
                if not Settings.AutoClick then continue end
                if not Module.IsAlive(client.Character) then continue end
                if not client.Character:FindFirstChildOfClass("Tool") then continue end
                module:AttackFunction()
                module:DamageAura()
            end
        end
    end)

    task.spawn(function()
        while task.wait(.3) do
            if _G['Fast Attack'] then
                module:BladeHits()
            end
        end
    end)

    task.spawn(function()
        while task.wait(.3) do
            pcall(function()
                if _G['Fast Attack'] then
                    for _, v in next, workspace.Enemies:GetChildren() do
                        if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                        (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                            RegisterAttack:FireServer(.3)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                                [2] = {}
                            }
                            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

task.spawn(function()
    while true do task.wait(.3)
        for _, v in next, workspace.Enemies:GetChildren() do
            if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
            (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                RegisterAttack:FireServer(.3)
                local args = {
                    [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                RegisterHit:FireServer(unpack(args))
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    FastAttackEnabled = true
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = 100,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("UpperTorso", true) or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character and Module.IsAlive(Enemy) then
                    local part = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart")
                    if part then
                        self:AttackEnemy(part)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function GetNearbyTargets(player, distance)
    local targets = {}
    distance = distance or 200
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            local part = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildOfClass("BasePart")
            if part then
                table.insert(targets, {obj, part})
            end
        end
    end
    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("Humanoid") and obj.Humanoid.Health > 0 and
           obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            local part = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildOfClass("BasePart")
            if part then
                table.insert(targets, {obj, part})
            end
        end
    end
    return targets
end

local function NewAttack(distance)
    distance = distance or 60
    pcall(function()
        for _, v in next, workspace.Enemies:GetChildren() do
            if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and 
               v:FindFirstChild("HumanoidRootPart") and 
               (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= distance then
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                local args = {
                    [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChild("UpperTorso") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                break
            end
        end
    end)
end

spawn(function()
    while true do task.wait(0)
        if _G['Fast Attack'] then
            Module.FastAttack:BladeHits()
            NewAttack(60)
            local targets = GetNearbyTargets(game.Players.LocalPlayer, 200)
            if #targets > 0 then
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
                for _, target in next, targets do
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        if _G['Fast Attack'] then
            pcall(function()
                local cLst = GetNearbyTargets(game.Players.LocalPlayer, 200)
                if #cLst > 0 then
                    local regAtk = game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
                    local regHit = game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")
                    regAtk:FireServer(.0)
                    for _, tgt in next, cLst do
                        regHit:FireServer(cLst[_][2], cLst)
                    end
                end
            end)
        end
    end
end)

local CachedChars = {}

local function IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
local RegisterHit = net:WaitForChild("RE/RegisterHit")

local function GetBladeHit()
    local tool = client.Character:FindFirstChildOfClass("Tool")
    if not tool then
        return nil
    end
    return tool
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    AttackRange = 200,
    AttackMobs = true,
    AttackPlayers = false,
    AttackCooldown = .0
}

local function GetTargets(player)
    local targets = {}
    local hash = {}
    for _, obj in pairs(characterfolder:GetChildren()) do
        if Settings.AttackPlayers and obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") then
            local distance = player:DistanceFromCharacter(obj.HumanoidRootPart.Position)
            if distance < Settings.AttackRange then
                local hitPart = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart")
                if hitPart and not hash[obj] then
                    table.insert(targets, {
                        [1] = obj,
                        [2] = hitPart
                    })
                    hash[obj] = true
                end
            end
        end
    end
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if Settings.AttackMobs and obj:FindFirstChild("HumanoidRootPart") then
            local distance = player:DistanceFromCharacter(obj.HumanoidRootPart.Position)
            if distance < Settings.AttackRange then
                local hitPart = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("RightHand") or obj:FindFirstChild("HumanoidRootPart")
                if hitPart and not hash[obj] then
                    table.insert(targets, {
                        [1] = obj,
                        [2] = hitPart
                    })
                    hash[obj] = true
                end
            end
        end
    end
    return targets
end

local function Attack()
    local targets = GetTargets(client)
    if #targets > 0 then
        RegisterAttack:FireServer(.0)
        local primaryTarget = targets[1][2]
        local args = {
            [1] = primaryTarget,
            [2] = targets
        }
        
        RegisterHit:FireServer(unpack(args))
    end
end

local Module = {}
Module.AttackCooldown = .0

Module.FastAttack = (function()
    if environment._advanced_attack then
        return environment._advanced_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.AttackRange,
        attackMobs = Settings.AttackMobs,
        attackPlayers = Settings.AttackPlayers,
        FirstAttack = false
    }
    
    function module:AttackEnemy(EnemyHead, TargetList)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, TargetList or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            if self.attackMobs then
                local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
                if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                    local HitPart = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or HRP
                    if not args[1] then
                        args[1] = HitPart
                    else
                        table.insert(args[2], {
                            [1] = Enemy,
                            [2] = HitPart
                        })
                    end
                end
            end
        end
        for _, Enemy in characterfolder:GetChildren() do
            if self.attackPlayers and Enemy ~= client.Character then
                local HitPart = Enemy:FindFirstChild("UpperTorso")
                if HitPart and client:DistanceFromCharacter(HitPart.Position) < self.Distance then
                    if not args[1] then
                        args[1] = HitPart
                    else
                        table.insert(args[2], {
                            [1] = Enemy,
                            [2] = HitPart
                        })
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait() do
            if _G['Fast Attack'] then
                for _, v in next, enemyfolder:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") then
                        local distance = (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude
                        if distance <= Settings.AttackRange then
                            RegisterAttack:FireServer(0)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                                [2] = {}
                            }
                            for _, e in next, enemyfolder:GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end
        end
    end)
    
    pcall(function()
        if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
            require(game.ReplicatedStorage.Util.CameraShaker):Stop()
        end
    end)
    
    task.spawn(function()
        game:GetService("RunService").RenderStepped:Connect(function()
            if _G['Fast Attack'] == true then
                if client.Character:FindFirstChild("Stun") then
                    client.Character.Stun.Value = 0
                end
                if client.Character:FindFirstChild("Busy") then
                    client.Character.Busy.Value = false
                end
            end
        end)
    end)
    environment._advanced_attack = module
    return module
end)()

task.spawn(function()
    while true do task.wait()
        if _G['Fast Attack'] then
            pcall(function()
                for _, v in next, enemyfolder:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") then
                        local distance = (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude
                        if distance <= Settings.AttackRange then
                            RegisterAttack:FireServer(0)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                                [2] = {}
                            }
                            for _, e in next, enemyfolder:GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end)
        end
    end
end)

local Settings = {
    ClickDelay = 0,
    AutoClick = true,
    AttackDistance = 60,
    FastAttackEnabled = true
}

local Module = {}
Module.AttackCooldown = 0
local CachedChars = {}

function Module.IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.FindEnemiesInRange()
    local playerPos = (client.Character or client.CharacterAdded:Wait()):GetPivot().Position
    local targetList = {}
    local primaryTarget = nil
    for _, enemy in pairs(enemyfolder:GetChildren()) do
        if enemy:FindFirstChildOfClass("Humanoid") and enemy:FindFirstChildOfClass("Humanoid").Health > 0 and not enemy:GetAttribute("IsBoat") then
            local head = enemy:FindFirstChild("Head") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    enemy,
                    head
                })
                primaryTarget = head
            end
        end
    end
    for _, player in pairs(players:GetPlayers()) do
        if player.Character and player ~= client then
            local head = player.Character:FindFirstChild("Head")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    player.Character,
                    head
                })
                if not primaryTarget then
                    primaryTarget = head
                end
            end
        end
    end
    return primaryTarget, targetList
end

function Module.AttackNoCoolDown()
    local primaryTarget, targetList = Module.FindEnemiesInRange()
    if not primaryTarget or #targetList == 0 then
        return
    end
    pcall(function()
        local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
        local RegisterHit = net:WaitForChild("RE/RegisterHit")
        RegisterAttack:FireServer(0)
        RegisterHit:FireServer(primaryTarget, targetList)
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    local module = {
        NextAttack = -math.huge,
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("Head") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character and Module.IsAlive(Character) then
                    local targetPart = Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart") or Character:FindFirstChildOfClass("BasePart")
                    if targetPart then
                        self:AttackEnemy(targetPart)
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
            Module.AttackNoCoolDown()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findTargetsInRange(player)
    local targetList = {}
    local playerPos = player.Character.HumanoidRootPart.Position
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and (obj.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and (obj2.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

task.spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findTargetsInRange(client)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                for _, target in pairs(targetList) do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targetList)
                end
            end
        end
        task.wait(0)
    end
end)

task.spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
                    (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in pairs(workspace:WaitForChild("Enemies"):GetChildren()) do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

task.spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G['Fast Attack'] then
            if client.Character:FindFirstChild("Stun") then
                client.Character.Stun.Value = 0
            end
            if client.Character:FindFirstChild("Humanoid") then
                client.Character.Humanoid.Sit = false
            end
            if client.Character:FindFirstChild("Busy") then
                client.Character.Busy.Value = false
            end
        end
    end)
end)

if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
    require(game.ReplicatedStorage.Util.CameraShaker):Stop()
end

task.spawn(function()
    while true do task.wait()
        for _, enemy in pairs(workspace.Enemies:GetChildren()) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
            (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                local args = {
                    [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in pairs(workspace.Enemies:GetChildren()) do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

local Settings = {
    ClickDelay = 0,
    AutoClick = true,
    AttackDistance = 60,
    FastAttackEnabled = true
}

local Module = {}
Module.AttackCooldown = 0.01
local CachedChars = {}

function Module.IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.FindEnemiesInRange()
    local playerPos = (client.Character or client.CharacterAdded:Wait()):GetPivot().Position
    local targetList = {}
    local primaryTarget = nil
    for _, enemy in pairs(enemyfolder:GetChildren()) do
        if enemy:FindFirstChildOfClass("Humanoid") and enemy:FindFirstChildOfClass("Humanoid").Health > 0 and not enemy:GetAttribute("IsBoat") then
            local head = enemy:FindFirstChild("Head") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    enemy,
                    head
                })
                primaryTarget = head
            end
        end
    end
    for _, player in pairs(players:GetPlayers()) do
        if player.Character and player ~= client then
            local head = player.Character:FindFirstChild("Head")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    player.Character,
                    head
                })
                if not primaryTarget then
                    primaryTarget = head
                end
            end
        end
    end
    return primaryTarget, targetList
end

function Module.AttackNoCoolDown()
    local primaryTarget, targetList = Module.FindEnemiesInRange()
    if not primaryTarget or #targetList == 0 then
        return
    end
    pcall(function()
        local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
        local RegisterHit = net:WaitForChild("RE/RegisterHit")
        RegisterAttack:FireServer(0.01)
        RegisterHit:FireServer(primaryTarget, targetList)
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    local module = {
        NextAttack = -math.huge,
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.01)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("Head") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character and Module.IsAlive(Character) then
                    local targetPart = Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart") or Character:FindFirstChildOfClass("BasePart")
                    if targetPart then
                        self:AttackEnemy(targetPart)
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(0.01)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
            Module.AttackNoCoolDown()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findTargetsInRange(player)
    local targetList = {}
    local playerPos = player.Character.HumanoidRootPart.Position
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and (obj.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and (obj2.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

task.spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findTargetsInRange(client)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                for _, target in pairs(targetList) do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targetList)
                end
            end
        end
        task.wait(0.01)
    end
end)

task.spawn(function()
    while true do task.wait(0.01)
        pcall(function()
            if _G['Fast Attack'] then
                for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
                    (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                        local args = {
                            [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in pairs(workspace:WaitForChild("Enemies"):GetChildren()) do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

task.spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G['Fast Attack'] then
            if client.Character:FindFirstChild("Stun") then
                client.Character.Stun.Value = 0
            end
            if client.Character:FindFirstChild("Humanoid") then
                client.Character.Humanoid.Sit = false
            end
            if client.Character:FindFirstChild("Busy") then
                client.Character.Busy.Value = false
            end
        end
    end)
end)

if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
    require(game.ReplicatedStorage.Util.CameraShaker):Stop()
end

task.spawn(function()
    while true do task.wait(0.01)
        for _, enemy in pairs(workspace.Enemies:GetChildren()) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
            (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                local args = {
                    [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in pairs(workspace.Enemies:GetChildren()) do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = 150,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("UpperTorso", true)
            if HRP and Module.IsAlive(Enemy) and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character and Module.IsAlive(Enemy) then
                    self:AttackEnemy(Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart"))
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    function module:getAllBladeHits(Sizes)
        local Hits = {}
        local Enemies = enemyfolder:GetChildren()
        for i=1,#Enemies do 
            local v = Enemies[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.RootPart and Human.Health > 0 and client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, Human.RootPart)
            end
        end
        return Hits
    end
    
    function module:getAllBladeHitsPlayers(Sizes)
        local Hits = {}
        local Characters = characterfolder:GetChildren()
        for i=1,#Characters do 
            local v = Characters[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if v.Name ~= client.Name and Human and Human.RootPart and Human.Health > 0 and client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, Human.RootPart)
            end
        end
        return Hits
    end

    function module:AttackFunction()
        local bladehit = self:getAllBladeHits(self.Distance)
        if #bladehit > 0 then
            RegisterAttack:FireServer(0)
            local args = {
                [1] = bladehit[1],
                [2] = {}
            }
            for _, hitPart in pairs(bladehit) do
                if _ > 1 then
                    local enemy = hitPart.Parent
                    table.insert(args[2], {
                        [1] = enemy,
                        [2] = hitPart
                    })
                end
            end
            RegisterHit:FireServer(unpack(args))
        end
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findNearbyTargets(player)
    local targetList = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findNearbyTargets(game.Players.LocalPlayer)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
                for _, target in next, targetList do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(targetList[_][2], targetList)
                end
            end
            wait(.0)
        else
            wait(.0)
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
                Module.FastAttack:AttackFunction()
            end
        end)
    end
end)

spawn(function()
    while task.wait() do
        pcall(function()
            if _G['Fast Attack'] then
                Module.FastAttack.Distance = 150
                Module.FastAttack:BladeHits()
            end
        end)
    end
end)
while true do task.wait()
    for i, v in next, workspace.Enemies:GetChildren() do
        if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
        (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
            replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
            local args = {
                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                [2] = {}
            }
            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    table.insert(args[2], {
                        [1] = e,
                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                    })
                end
            end
            replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
        end
    end
end

local Module = {}
Module.AttackCooldown = .0
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    AutoBuso = true,
    FastAttackRange = 60,
    WaitTime = 0,
    IncludeCharacters = true,
    IncludeEnemies = true
}

local lastAttackTime = tick()
local DeoCanOxy = 0
local OxyChuanBiNap = false

function CheckOxy()
  if type(DeoCanOxy) ~= "number" or not DeoCanOxy then
    DeoCanOxy = 0
  end
  DeoCanOxy = DeoCanOxy + 1
  if DeoCanOxy < 200 then
    return "VanOK"
  else
    return "Cuuchiemoi"
  end
end

function Hitoxy()
  if not OxyChuanBiNap then
    OxyChuanBiNap = true
    task.wait(5)
    DeoCanOxy = 0
    OxyChuanBiNap = false
  else
    return 'Dangnapoxy'
  end
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.FastAttackRange,
        attackMobs = Settings.IncludeEnemies,
        attackPlayers = Settings.IncludeCharacters,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if CheckOxy() == "Cuuchiemoi" then
            Hitoxy()
            return "tutubinhtinh"
        end
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
            pcall(function()
                if client.Character:FindFirstChildOfClass("Tool") then
                    for _, v in pairs(client.Character:FindFirstChildOfClass("Tool"):GetDescendants()) do
                        if v:IsA("Animation") then
                            v:Play(0.001, 0.001, 0.001)
                        end
                    end
                end
            end)
        end
    end
    
    function module:GetTargetsFromEnemies()
        local mainTarget = nil
        local otherTargets = {}
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            local UpperTorso = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and Enemy:FindFirstChild("Humanoid") and Enemy.Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not mainTarget then
                    mainTarget = UpperTorso
                else
                    table.insert(otherTargets, {
                        [1] = Enemy,
                        [2] = UpperTorso
                    })
                end
            end
        end
        return mainTarget, otherTargets
    end
    
    function module:AttackNearest()
        if Settings.AutoBuso and client.Character and not client.Character:FindFirstChild("HasBuso") then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
            end)
        end
        local mainTarget, otherTargets = self:GetTargetsFromEnemies()
        if mainTarget then
            self:AttackEnemy(mainTarget, otherTargets)
        end
        if Settings.IncludeCharacters then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character then
                    local UpperTorso = Enemy:FindFirstChild("UpperTorso")
                    if UpperTorso then
                        self:AttackEnemy(UpperTorso)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(Settings.WaitTime)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait(0) do
            pcall(function()
                if Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                    module:BladeHits()
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait()
        pcall(function()
            for i, v in next, workspace.Enemies:GetChildren() do
                if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                    local args = {
                        [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                        [2] = {}
                    }
                    for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                        if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                            table.insert(args[2], {
                                [1] = e,
                                [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                            })
                        end
                    end
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = 0.0001
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = .0,
    AutoClick = true,
    AutoBuso = true,
    FastAttackRange = 60,
    WaitTime = 0,
    IncludeCharacters = true,
    IncludeEnemies = true
}

local lastAttackTime = tick()
local DeoCanOxy = 0
local OxyChuanBiNap = false

function CheckOxy()
  if type(DeoCanOxy) ~= "number" or not DeoCanOxy then
    DeoCanOxy = 0
  end
  DeoCanOxy = DeoCanOxy + 1
  if DeoCanOxy < 200 then
    return "VanOK"
  else
    return "Cuuchiemoi"
  end
end

function Hitoxy()
  if not OxyChuanBiNap then
    OxyChuanBiNap = true
    task.wait(5)
    DeoCanOxy = 0
    OxyChuanBiNap = false
  else
    return 'Dangnapoxy'
  end
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.FastAttackRange,
        attackMobs = Settings.IncludeEnemies,
        attackPlayers = Settings.IncludeCharacters,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if CheckOxy() == "Cuuchiemoi" then
            Hitoxy()
            return "tutubinhtinh"
        end
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.0001)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
            pcall(function()
                if client.Character:FindFirstChildOfClass("Tool") then
                    for _, v in pairs(client.Character:FindFirstChildOfClass("Tool"):GetDescendants()) do
                        if v:IsA("Animation") then
                            v:Play(0.001, 0.001, 0.001)
                        end
                    end
                end
            end)
        end
    end
    
    function module:GetTargetsFromEnemies()
        local mainTarget = nil
        local otherTargets = {}
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            local UpperTorso = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and Enemy:FindFirstChild("Humanoid") and Enemy.Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not mainTarget then
                    mainTarget = UpperTorso
                else
                    table.insert(otherTargets, {
                        [1] = Enemy,
                        [2] = UpperTorso
                    })
                end
            end
        end
        return mainTarget, otherTargets
    end
    
    function module:AttackNearest()
        if Settings.AutoBuso and client.Character and not client.Character:FindFirstChild("HasBuso") then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
            end)
        end
        local mainTarget, otherTargets = self:GetTargetsFromEnemies()
        if mainTarget then
            self:AttackEnemy(mainTarget, otherTargets)
        end
        if Settings.IncludeCharacters then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character then
                    local UpperTorso = Enemy:FindFirstChild("UpperTorso")
                    if UpperTorso then
                        self:AttackEnemy(UpperTorso)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(Settings.WaitTime)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait(0.0001) do
            pcall(function()
                if Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                    module:BladeHits()
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            for i, v in next, workspace.Enemies:GetChildren() do
                if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                    local args = {
                        [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                        [2] = {}
                    }
                    for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                        if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                            table.insert(args[2], {
                                [1] = e,
                                [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                            })
                        end
                    end
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AutoBuso = true,
    FastAttackRange = tick(),
    WaitTime = tick(),
    IncludeCharacters = true,
    IncludeEnemies = true
}

local lastAttackTime = tick()
local DeoCanOxy = 0
local OxyChuanBiNap = false

function CheckOxy()
  if type(DeoCanOxy) ~= "number" or not DeoCanOxy then
    DeoCanOxy = 0
  end
  DeoCanOxy = DeoCanOxy + 1
  if DeoCanOxy < 200 then
    return "VanOK"
  else
    return "Cuuchiemoi"
  end
end

function Hitoxy()
  if not OxyChuanBiNap then
    OxyChuanBiNap = true
    task.wait(.0)
    DeoCanOxy = 0
    OxyChuanBiNap = false
  else
    return 'Dangnapoxy'
  end
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.FastAttackRange,
        attackMobs = Settings.IncludeEnemies,
        attackPlayers = Settings.IncludeCharacters,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if CheckOxy() == "Cuuchiemoi" then
            Hitoxy()
            return "tutubinhtinh"
        end
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
            pcall(function()
                if client.Character:FindFirstChildOfClass("Tool") then
                    for _, v in pairs(client.Character:FindFirstChildOfClass("Tool"):GetDescendants()) do
                        if v:IsA("Animation") then
                            v:Play(0, 0, 0)
                        end
                    end
                end
            end)
        end
    end
    
    function module:GetTargetsFromEnemies()
        local mainTarget = nil
        local otherTargets = {}
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            local UpperTorso = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and Enemy:FindFirstChild("Humanoid") and Enemy.Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not mainTarget then
                    mainTarget = UpperTorso
                else
                    table.insert(otherTargets, {
                        [1] = Enemy,
                        [2] = UpperTorso
                    })
                end
            end
        end
        return mainTarget, otherTargets
    end
    
    function module:AttackNearest()
        if Settings.AutoBuso and client.Character and not client.Character:FindFirstChild("HasBuso") then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
            end)
        end
        local mainTarget, otherTargets = self:GetTargetsFromEnemies()
        if mainTarget then
            self:AttackEnemy(mainTarget, otherTargets)
        end
        if Settings.IncludeCharacters then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character then
                    local UpperTorso = Enemy:FindFirstChild("UpperTorso")
                    if UpperTorso then
                        self:AttackEnemy(UpperTorso)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(Settings.WaitTime)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait(0.0001) do
            pcall(function()
                if Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                    module:BladeHits()
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            for i, v in next, workspace.Enemies:GetChildren() do
                if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                    local args = {
                        [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                        [2] = {}
                    }
                    for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                        if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                            table.insert(args[2], {
                                [1] = e,
                                [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                            })
                        end
                    end
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    NoAttackAnimation = false
}

local function FindAttackableTargets(player)
    local targetList = {}
    
    for _, obj in pairs(characterfolder:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChildOfClass("Humanoid") and
           obj:FindFirstChildOfClass("Humanoid").Health > 0 and
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 65 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    
    return targetList
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = tick(),
        Distance = 65,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = true,
        CurrentAllMob = {},
        canHits = {}
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local Humanoid = Enemy:FindFirstChildOfClass("Humanoid")
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            if HRP and Humanoid and Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character then
                    local Player = players:GetPlayerFromCharacter(Character)
                    if Player and not Player:GetAttribute("PvpDisabled") then
                        self:AttackEnemy(Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart"))
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module.CurrentAllMob = {}
            module.canHits = {}
            local attackables = FindAttackableTargets(client)
            for _, target in pairs(attackables) do
                table.insert(module.CurrentAllMob, target[1])
                table.insert(module.canHits, target[2])
            end
            if #module.canHits > 0 then
                module:BladeHits()
            end
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChildOfClass("Humanoid") and v:FindFirstChildOfClass("Humanoid").Health > 0 
                       and v:FindFirstChild("HumanoidRootPart") 
                       and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChildOfClass("Humanoid") and e:FindFirstChildOfClass("Humanoid").Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local Priority = {
    Class = "Combat",
    Recently = "Attack",
    Activity = true
}

Priority.clear = function()
    Priority.Recently = "None"
    Priority.Activity = false
end

local AttackSignal = Instance.new("BindableEvent")
task.spawn(function()
    local CollectionService = game:GetService("CollectionService")
    local Client = players.LocalPlayer
    local Char = Client.Character or Client.CharacterAdded:Wait()
    local stacking = 0
    local printCooldown = 0
    local OldPriority = Priority.Recently
    dist = function(a,b,noHeight)
        if not b then
            b = Root.Position
        end
        return (Vector3.new(a.X,not noHeight and a.Y,a.Z) - Vector3.new(b.X,not noHeight and b.Y,b.Z)).magnitude
    end
    local function dist(pos)
        return (pos - Char.HumanoidRootPart.Position).Magnitude
    end
    while wait(.075) do
        local nearbymon = false
        table.clear(Module.FastAttack.CurrentAllMob)
        table.clear(Module.FastAttack.canHits)
        local mobs = CollectionService:GetTagged("ActiveRig")
        for i=1,#mobs do local v = mobs[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.Health > 0 and Human.RootPart and v ~= Char then
                local IsPlayer = players:GetPlayerFromCharacter(v)
                local IsAlly = IsPlayer and CollectionService:HasTag(IsPlayer,"Ally"..Client.Name)
                if not IsAlly then
                    Module.FastAttack.CurrentAllMob[#Module.FastAttack.CurrentAllMob + 1] = v
                    if not nearbymon and dist(Human.RootPart.Position) < 65 then
                        nearbymon = true
                    end
                end
            end
        end
        if nearbymon then
            local Enemies = workspace.Enemies:GetChildren()
            local Players = players:GetPlayers()
            for i=1,#Enemies do local v = Enemies[i]
                local Human = v:FindFirstChildOfClass("Humanoid")
                if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                    Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                end
            end
            for i=1,#Players do local v = Players[i].Character
                if not Players[i]:GetAttribute("PvpDisabled") and v and v ~= Client.Character then
                    local Human = v:FindFirstChildOfClass("Humanoid")
                    if Human and Human.RootPart and Human.Health > 0 and dist(Human.RootPart.Position) < 65 then
                        Module.FastAttack.canHits[#Module.FastAttack.canHits+1] = Human.RootPart
                    end
                end
            end
        end
        if OldPriority ~= Priority.Recently then
            OldPriority = Priority.Recently
            stacking = tick()
        end
        if tick() - stacking > 60 and OldPriority and OldPriority.Class == Priority.Class then
            Priority:clear()
        elseif tick() - printCooldown > 5 then
            printCooldown = tick()
        end
    end
end)

local CachedChars = {}
local function IsAlive(Char)
    if not Char then
        return false
    end

    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end

    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local function FindNearbyTargets(player, distance)
    local targets = {}
    
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            table.insert(targets, {obj, obj.HumanoidRootPart})
        end
    end

    return targets
end

spawn(function()
    while true do task.wait(0.0000003)
        if _G['Fast Attack'] then
            pcall(function()
                for _, enemy in next, workspace.Enemies:GetChildren() do
                    if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
                       enemy:FindFirstChild("HumanoidRootPart") and 
                       (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        registerAttack:FireServer(0.0000003)
                        local hitPart = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                        local args = {
                            [1] = hitPart,
                            [2] = {}
                        }
                        for _, target in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = target,
                                    [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        registerHit:FireServer(unpack(args))
                    end
                end
            end)
        end
    end
end)

spawn(function()
    while true do wait(0.0000003)
        if _G['Fast Attack'] then
            local nearbyTargets = FindNearbyTargets(client, 200)
            if #nearbyTargets > 0 then
                registerAttack:FireServer(0.0000003)
                for _, target in next, nearbyTargets do
                    registerHit:FireServer(target[2], nearbyTargets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0.0000003)
        if not IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        for _, enemy in next, workspace.Enemies:GetChildren() do
            if enemy.Humanoid and enemy.Humanoid.Health > 0 and 
               enemy:FindFirstChild("HumanoidRootPart") and 
               (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                registerAttack:FireServer(0.0000003)
                local hitPart = enemy:FindFirstChild("UpperTorso") or enemy:FindFirstChild("HumanoidRootPart")
                local hitTargets = {}
                for _, target in next, workspace.Enemies:GetChildren() do
                    if target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
                        table.insert(hitTargets, {
                            [1] = target,
                            [2] = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                registerHit:FireServer(hitPart, hitTargets)
                break
            end
        end
    end
end)

local Camera = nil
pcall(function()
    Camera = require(game.ReplicatedStorage.Util.CameraShaker)
    if Camera then
        Camera:Stop()
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}
local time_all_x = tick()
local time_all_p = tick()
local time_x = tick()
local time_xeg = tick()

function Module.IsAlive(Char)
    if not Char then
        return false
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.GetAllHits(Sizes)
    local Hits = {}
    local Client = game.Players.LocalPlayer
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        local Human = v:FindFirstChildOfClass("Humanoid")
        if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
            table.insert(Hits, {v, Human.RootPart})
        end
    end
    for _, v in pairs(workspace.Characters:GetChildren()) do
        if v ~= Client.Character then
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, {v, Human.RootPart})
            end
        end
    end
    return Hits
end

local RegisterAttack = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
local RegisterHit = replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")

function Module.AttackNoCD()
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = Module.GetAllHits(80)
        if #targets > 0 then
            RegisterAttack:FireServer(0.0000001)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_x = time_all_x + 1
            if time_x >= 2 then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

function Module.AttackPlayer(targetName)
    pcall(function()
        if not Module.IsAlive(client.Character) then return end
        if not client.Character:FindFirstChildOfClass("Tool") then return end
        local targets = {}
        for _, v in pairs(game.Workspace.Characters:GetChildren()) do
            if v:FindFirstChild('Humanoid') and v.Humanoid.Health > 0 and 
               (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= 50 and 
               tostring(v.Name) == targetName then
                table.insert(targets, {v, v.HumanoidRootPart})
            end
        end
        if #targets > 0 then
            RegisterAttack:FireServer(0.0000001)
            local args = {
                [1] = targets[1][2],
                [2] = {}
            }
            for _, target in next, targets do
                table.insert(args[2], {
                    [1] = target[1],
                    [2] = target[2]
                })
            end
            RegisterHit:FireServer(unpack(args))
            time_x = time_x + 1
            time_all_p = time_all_p + 1
            if time_x >= 2 and time_all_p >= 150 or game:GetService("Players").LocalPlayer.PlayerGui.Main.SafeZone.Visible == true then
                local currentTool = client.Character:FindFirstChildOfClass("Tool")
                if currentTool then
                    game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange", tostring(currentTool))
                end
                time_x = 0
            end
        end
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = tick(),
        Distance = tick(),
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.0000001)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or HRP
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or HRP
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        for _, Enemy in characterfolder:GetChildren() do
            if Enemy ~= client.Character then
                self:AttackEnemy(Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart"))
            end
        end
        if not self.FirstAttack then
            task.wait(0.0000001)
        end
    end
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while task.wait() do
        pcall(function()
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Stun") and
               game.Players.LocalPlayer.Character.Stun.Value == 0 then
                Module.AttackNoCD()
                wait(0.0000001)
            else
                wait(0.0000001)
            end
        end)
    end
end)

task.spawn(function()
    while game:GetService("RunService").Stepped:Wait() do
        if (tick() - Module.AttackCooldown) < 0.000000001 then continue end
        if not Module.IsAlive(client.Character) then continue end
        if not client.Character:FindFirstChildOfClass("Tool") then continue end
        Module.FastAttack:BladeHits()
    end
end)

spawn(function()
    while true do task.wait(0.0000001)
        pcall(function()
            if _G['Fast Attack'] then
                for _, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                       (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        RegisterAttack:FireServer(0.0000001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        RegisterHit:FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

function CurrentWeapon()
	if not client.Character then return nil end
	return client.Character:FindFirstChildOfClass("Tool")
end

function getAllBladeHitsPlayers(Sizes)
	local Hits = {}
	for _, v in pairs(characterfolder:GetChildren()) do
		if v.Name ~= client.Name and v:FindFirstChildOfClass("Humanoid") and 
		   v:FindFirstChild("HumanoidRootPart") and 
		   v:FindFirstChildOfClass("Humanoid").Health > 0 and 
		   client:DistanceFromCharacter(v.HumanoidRootPart.Position) < Sizes+5 then
			table.insert(Hits, {
				[1] = v,
				[2] = v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart")
			})
		end
	end
	return Hits
end

function getAllBladeHits(Sizes)
	local Hits = {}
	for _, v in pairs(enemyfolder:GetChildren()) do
		if v:FindFirstChildOfClass("Humanoid") and 
		   v:FindFirstChild("HumanoidRootPart") and 
		   v:FindFirstChildOfClass("Humanoid").Health > 0 and 
		   client:DistanceFromCharacter(v.HumanoidRootPart.Position) < Sizes+5 then
			table.insert(Hits, {
				[1] = v,
				[2] = v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart")
			})
		end
	end
	return Hits
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AttackDistance = 60
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end

    local module = {
        NextAttack = tick(),
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = true,
        FirstAttack = false
    }

    function module:DamageAura()
        local enemyHits = getAllBladeHits(150)
        local playerHits = getAllBladeHitsPlayers(150)
        if (#enemyHits > 0 or #playerHits > 0) and CurrentWeapon() then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            local targetHit = nil
            if #enemyHits > 0 then
                targetHit = enemyHits[1][2]
            elseif #playerHits > 0 then
                targetHit = playerHits[1][2]
            end
            if targetHit then
                local allTargets = {}
                for _, enemy in pairs(enemyHits) do
                    table.insert(allTargets, enemy)
                end
                for _, player in pairs(playerHits) do
                    table.insert(allTargets, player)
                end
                RegisterHit:FireServer(targetHit, allTargets)
            end
        else
            self.FirstAttack = false
        end
    end

    function module:AttackFunction()
        local enemyHits = getAllBladeHits(60)
        if #enemyHits > 0 and CurrentWeapon() then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(enemyHits[1][2], enemyHits)
        else
            self.FirstAttack = false
        end
    end

    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(args[1], args[2])
        for _, Enemy in characterfolder:GetChildren() do
            if Enemy ~= client.Character then
                self:AttackEnemy(
                    Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart"),
                    {}
                )
            end
        end
        if not self.FirstAttack then
            task.wait(.3)
        end
    end

    function module:AttackEnemy(EnemyPart, Table)
        if EnemyPart and client:DistanceFromCharacter(EnemyPart.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.3)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyPart, Table or {})
        end
    end

    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if _G['Fast Attack'] then
                if (tick() - Module.AttackCooldown) < 0 then continue end
                if not Settings.AutoClick then continue end
                if not Module.IsAlive(client.Character) then continue end
                if not client.Character:FindFirstChildOfClass("Tool") then continue end
                module:AttackFunction()
                module:DamageAura()
            end
        end
    end)

    task.spawn(function()
        while task.wait(.3) do
            if _G['Fast Attack'] then
                module:BladeHits()
            end
        end
    end)

    task.spawn(function()
        while task.wait(.3) do
            pcall(function()
                if _G['Fast Attack'] then
                    for _, v in next, workspace.Enemies:GetChildren() do
                        if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                        (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                            RegisterAttack:FireServer(.3)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                                [2] = {}
                            }
                            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

task.spawn(function()
    while true do task.wait(.3)
        for _, v in next, workspace.Enemies:GetChildren() do
            if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
            (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                RegisterAttack:FireServer(.3)
                local args = {
                    [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                RegisterHit:FireServer(unpack(args))
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end

	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end

	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    FastAttackEnabled = true
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = 100,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("UpperTorso", true) or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character and Module.IsAlive(Enemy) then
                    local part = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart")
                    if part then
                        self:AttackEnemy(part)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function GetNearbyTargets(player, distance)
    local targets = {}
    distance = distance or 200
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            local part = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildOfClass("BasePart")
            if part then
                table.insert(targets, {obj, part})
            end
        end
    end
    for _, obj in pairs(workspace.Enemies:GetChildren()) do
        if obj:FindFirstChild("Humanoid") and obj.Humanoid.Health > 0 and
           obj:FindFirstChild("HumanoidRootPart") and 
           player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < distance then
            local part = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildOfClass("BasePart")
            if part then
                table.insert(targets, {obj, part})
            end
        end
    end
    return targets
end

local function NewAttack(distance)
    distance = distance or 60
    pcall(function()
        for _, v in next, workspace.Enemies:GetChildren() do
            if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and 
               v:FindFirstChild("HumanoidRootPart") and 
               (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= distance then
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                local args = {
                    [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChild("UpperTorso") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                break
            end
        end
    end)
end

spawn(function()
    while true do task.wait(0)
        if _G['Fast Attack'] then
            Module.FastAttack:BladeHits()
            NewAttack(60)
            local targets = GetNearbyTargets(game.Players.LocalPlayer, 200)
            if #targets > 0 then
                game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
                for _, target in next, targets do
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targets)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        if _G['Fast Attack'] then
            pcall(function()
                local cLst = GetNearbyTargets(game.Players.LocalPlayer, 200)
                if #cLst > tick() then
                    local regAtk = game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
                    local regHit = game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")
                    regAtk:FireServer(.0)
                    for _, tgt in next, cLst do
                        regHit:FireServer(cLst[_][2], cLst)
                    end
                end
            end)
        end
    end
end)

local CachedChars = {}

local function IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
local RegisterHit = net:WaitForChild("RE/RegisterHit")

local function GetBladeHit()
    local tool = client.Character:FindFirstChildOfClass("Tool")
    if not tool then
        return nil
    end
    return tool
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AttackRange = tick(),
    AttackMobs = true,
    AttackPlayers = false,
    AttackCooldown = .0
}

local function GetTargets(player)
    local targets = {}
    local hash = {}
    for _, obj in pairs(characterfolder:GetChildren()) do
        if Settings.AttackPlayers and obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") then
            local distance = player:DistanceFromCharacter(obj.HumanoidRootPart.Position)
            if distance < Settings.AttackRange then
                local hitPart = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("HumanoidRootPart")
                if hitPart and not hash[obj] then
                    table.insert(targets, {
                        [1] = obj,
                        [2] = hitPart
                    })
                    hash[obj] = true
                end
            end
        end
    end
    for _, obj in pairs(enemyfolder:GetChildren()) do
        if Settings.AttackMobs and obj:FindFirstChild("HumanoidRootPart") then
            local distance = player:DistanceFromCharacter(obj.HumanoidRootPart.Position)
            if distance < Settings.AttackRange then
                local hitPart = obj:FindFirstChild("UpperTorso") or obj:FindFirstChild("RightHand") or obj:FindFirstChild("HumanoidRootPart")
                if hitPart and not hash[obj] then
                    table.insert(targets, {
                        [1] = obj,
                        [2] = hitPart
                    })
                    hash[obj] = true
                end
            end
        end
    end
    return targets
end

local function Attack()
    local targets = GetTargets(client)
    if #targets > tick() then
        RegisterAttack:FireServer(.0)
        local primaryTarget = targets[1][2]
        local args = {
            [1] = primaryTarget,
            [2] = targets
        }
        
        RegisterHit:FireServer(unpack(args))
    end
end

local Module = {}
Module.AttackCooldown = tick()

Module.FastAttack = (function()
    if environment._advanced_attack then
        return environment._advanced_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.AttackRange,
        attackMobs = Settings.AttackMobs,
        attackPlayers = Settings.AttackPlayers,
        FirstAttack = false
    }
    
    function module:AttackEnemy(EnemyHead, TargetList)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, TargetList or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            if self.attackMobs then
                local HRP = Enemy:FindFirstChild("HumanoidRootPart", true)
                if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                    local HitPart = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or HRP
                    if not args[1] then
                        args[1] = HitPart
                    else
                        table.insert(args[2], {
                            [1] = Enemy,
                            [2] = HitPart
                        })
                    end
                end
            end
        end
        for _, Enemy in characterfolder:GetChildren() do
            if self.attackPlayers and Enemy ~= client.Character then
                local HitPart = Enemy:FindFirstChild("UpperTorso")
                if HitPart and client:DistanceFromCharacter(HitPart.Position) < self.Distance then
                    if not args[1] then
                        args[1] = HitPart
                    else
                        table.insert(args[2], {
                            [1] = Enemy,
                            [2] = HitPart
                        })
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait() do
            if _G['Fast Attack'] then
                for _, v in next, enemyfolder:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") then
                        local distance = (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude
                        if distance <= Settings.AttackRange then
                            RegisterAttack:FireServer(0)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                                [2] = {}
                            }
                            for _, e in next, enemyfolder:GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end
        end
    end)
    
    pcall(function()
        if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
            require(game.ReplicatedStorage.Util.CameraShaker):Stop()
        end
    end)
    
    task.spawn(function()
        game:GetService("RunService").RenderStepped:Connect(function()
            if _G['Fast Attack'] == true then
                if client.Character:FindFirstChild("Stun") then
                    client.Character.Stun.Value = 0
                end
                if client.Character:FindFirstChild("Busy") then
                    client.Character.Busy.Value = false
                end
            end
        end)
    end)
    environment._advanced_attack = module
    return module
end)()

task.spawn(function()
    while true do task.wait()
        if _G['Fast Attack'] then
            pcall(function()
                for _, v in next, enemyfolder:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") then
                        local distance = (v.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude
                        if distance <= Settings.AttackRange then
                            RegisterAttack:FireServer(0)
                            local args = {
                                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                                [2] = {}
                            }
                            for _, e in next, enemyfolder:GetChildren() do
                                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                    table.insert(args[2], {
                                        [1] = e,
                                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                    })
                                end
                            end
                            RegisterHit:FireServer(unpack(args))
                        end
                    end
                end
            end)
        end
    end
end)

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AttackDistance = 60,
    FastAttackEnabled = true
}

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.FindEnemiesInRange()
    local playerPos = (client.Character or client.CharacterAdded:Wait()):GetPivot().Position
    local targetList = {}
    local primaryTarget = nil
    for _, enemy in pairs(enemyfolder:GetChildren()) do
        if enemy:FindFirstChildOfClass("Humanoid") and enemy:FindFirstChildOfClass("Humanoid").Health > 0 and not enemy:GetAttribute("IsBoat") then
            local head = enemy:FindFirstChild("Head") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    enemy,
                    head
                })
                primaryTarget = head
            end
        end
    end
    for _, player in pairs(players:GetPlayers()) do
        if player.Character and player ~= client then
            local head = player.Character:FindFirstChild("Head")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    player.Character,
                    head
                })
                if not primaryTarget then
                    primaryTarget = head
                end
            end
        end
    end
    return primaryTarget, targetList
end

function Module.AttackNoCoolDown()
    local primaryTarget, targetList = Module.FindEnemiesInRange()
    if not primaryTarget or #targetList == tick() then
        return
    end
    pcall(function()
        local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
        local RegisterHit = net:WaitForChild("RE/RegisterHit")
        RegisterAttack:FireServer(0)
        RegisterHit:FireServer(primaryTarget, targetList)
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    local module = {
        NextAttack = -math.huge,
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("Head") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character and Module.IsAlive(Character) then
                    local targetPart = Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart") or Character:FindFirstChildOfClass("BasePart")
                    if targetPart then
                        self:AttackEnemy(targetPart)
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
            Module.AttackNoCoolDown()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findTargetsInRange(player)
    local targetList = {}
    local playerPos = player.Character.HumanoidRootPart.Position
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and (obj.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and (obj2.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

task.spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findTargetsInRange(client)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                for _, target in pairs(targetList) do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targetList)
                end
            end
        end
        task.wait(0)
    end
end)

task.spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
                    (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in pairs(workspace:WaitForChild("Enemies"):GetChildren()) do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

task.spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G['Fast Attack'] then
            if client.Character:FindFirstChild("Stun") then
                client.Character.Stun.Value = 0
            end
            if client.Character:FindFirstChild("Humanoid") then
                client.Character.Humanoid.Sit = false
            end
            if client.Character:FindFirstChild("Busy") then
                client.Character.Busy.Value = false
            end
        end
    end)
end)

if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
    require(game.ReplicatedStorage.Util.CameraShaker):Stop()
end

task.spawn(function()
    while true do task.wait()
        for _, enemy in pairs(workspace.Enemies:GetChildren()) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
            (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                local args = {
                    [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in pairs(workspace.Enemies:GetChildren()) do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AttackDistance = 60,
    FastAttackEnabled = true
}

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char)
    if not Char then
        return nil
    end
    if CachedChars[Char] then
        return CachedChars[Char].Health > 0
    end
    local Hum = Char:FindFirstChildOfClass("Humanoid")
    CachedChars[Char] = Hum
    return Hum and Hum.Health > 0
end

function Module.FindEnemiesInRange()
    local playerPos = (client.Character or client.CharacterAdded:Wait()):GetPivot().Position
    local targetList = {}
    local primaryTarget = nil
    for _, enemy in pairs(enemyfolder:GetChildren()) do
        if enemy:FindFirstChildOfClass("Humanoid") and enemy:FindFirstChildOfClass("Humanoid").Health > 0 and not enemy:GetAttribute("IsBoat") then
            local head = enemy:FindFirstChild("Head") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    enemy,
                    head
                })
                primaryTarget = head
            end
        end
    end
    for _, player in pairs(players:GetPlayers()) do
        if player.Character and player ~= client then
            local head = player.Character:FindFirstChild("Head")
            if head and (playerPos - head.Position).Magnitude <= Settings.AttackDistance then
                table.insert(targetList, {
                    player.Character,
                    head
                })
                if not primaryTarget then
                    primaryTarget = head
                end
            end
        end
    end
    return primaryTarget, targetList
end

function Module.AttackNoCoolDown()
    local primaryTarget, targetList = Module.FindEnemiesInRange()
    if not primaryTarget or #targetList == 0 then
        return
    end
    pcall(function()
        local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
        local RegisterHit = net:WaitForChild("RE/RegisterHit")
        RegisterAttack:FireServer(0.01)
        RegisterHit:FireServer(primaryTarget, targetList)
    end)
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    local module = {
        NextAttack = -math.huge,
        Distance = Settings.AttackDistance,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.01)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("Head") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        if self.attackPlayers then
            for _, Character in characterfolder:GetChildren() do
                if Character ~= client.Character and Module.IsAlive(Character) then
                    local targetPart = Character:FindFirstChild("UpperTorso") or Character:FindFirstChild("HumanoidRootPart") or Character:FindFirstChildOfClass("BasePart")
                    if targetPart then
                        self:AttackEnemy(targetPart)
                    end
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if not self.FirstAttack then
            task.wait(0.01)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if not Settings.FastAttackEnabled then continue end
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
            Module.AttackNoCoolDown()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findTargetsInRange(player)
    local targetList = {}
    local playerPos = player.Character.HumanoidRootPart.Position
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and (obj.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and (obj2.HumanoidRootPart.Position - playerPos).Magnitude <= Settings.AttackDistance then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

task.spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findTargetsInRange(client)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                for _, target in pairs(targetList) do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(target[2], targetList)
                end
            end
        end
        task.wait(0.01)
    end
end)

task.spawn(function()
    while true do task.wait(0.01)
        pcall(function()
            if _G['Fast Attack'] then
                for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
                    (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                        local args = {
                            [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in pairs(workspace:WaitForChild("Enemies"):GetChildren()) do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

task.spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G['Fast Attack'] then
            if client.Character:FindFirstChild("Stun") then
                client.Character.Stun.Value = 0
            end
            if client.Character:FindFirstChild("Humanoid") then
                client.Character.Humanoid.Sit = false
            end
            if client.Character:FindFirstChild("Busy") then
                client.Character.Busy.Value = false
            end
        end
    end)
end)

if game.ReplicatedStorage:FindFirstChild("Util") and game.ReplicatedStorage.Util:FindFirstChild("CameraShaker") then
    require(game.ReplicatedStorage.Util.CameraShaker):Stop()
end

task.spawn(function()
    while true do task.wait(0.01)
        for _, enemy in pairs(workspace.Enemies:GetChildren()) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 and enemy:FindFirstChild("HumanoidRootPart") and
            (enemy.HumanoidRootPart.Position - client.Character.HumanoidRootPart.Position).Magnitude <= Settings.AttackDistance then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                local args = {
                    [1] = enemy:FindFirstChild("RightHand") or enemy:FindFirstChild("HumanoidRootPart") or enemy:FindFirstChildOfClass("BasePart"),
                    [2] = {}
                }
                for _, e in pairs(workspace.Enemies:GetChildren()) do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true
}

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = 150,
        attackMobs = true,
        attackPlayers = false,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
        end
    end
    
    function module:AttackNearest()
        local args = {
            [1] = nil,
            [2] = {}
        }
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart", true) or Enemy:FindFirstChild("UpperTorso", true)
            if HRP and Module.IsAlive(Enemy) and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not args[1] then
                    args[1] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                else
                    table.insert(args[2], {
                        [1] = Enemy,
                        [2] = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("RightHand") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
                    })
                end
            end
        end
        self:AttackEnemy(unpack(args))
        if self.attackPlayers then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character and Module.IsAlive(Enemy) then
                    self:AttackEnemy(Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart"))
                end
            end
        end
        if not self.FirstAttack then
            task.wait(.0)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end
    
    function module:getAllBladeHits(Sizes)
        local Hits = {}
        local Enemies = enemyfolder:GetChildren()
        for i=1,#Enemies do 
            local v = Enemies[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if Human and Human.RootPart and Human.Health > 0 and client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, Human.RootPart)
            end
        end
        return Hits
    end
    
    function module:getAllBladeHitsPlayers(Sizes)
        local Hits = {}
        local Characters = characterfolder:GetChildren()
        for i=1,#Characters do 
            local v = Characters[i]
            local Human = v:FindFirstChildOfClass("Humanoid")
            if v.Name ~= client.Name and Human and Human.RootPart and Human.Health > 0 and client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
                table.insert(Hits, Human.RootPart)
            end
        end
        return Hits
    end

    function module:AttackFunction()
        local bladehit = self:getAllBladeHits(self.Distance)
        if #bladehit > 0 then
            RegisterAttack:FireServer(0)
            local args = {
                [1] = bladehit[1],
                [2] = {}
            }
            for _, hitPart in pairs(bladehit) do
                if _ > 1 then
                    local enemy = hitPart.Parent
                    table.insert(args[2], {
                        [1] = enemy,
                        [2] = hitPart
                    })
                end
            end
            RegisterHit:FireServer(unpack(args))
        end
    end
    
    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    environment._trash_attack = module
    return module
end)()

local function findNearbyTargets(player)
    local targetList = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

spawn(function()
    while true do
        if _G['Fast Attack'] then
            local targetList = findNearbyTargets(game.Players.LocalPlayer)
            if #targetList > 0 then
                replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(.0)
                for _, target in next, targetList do
                    replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(targetList[_][2], targetList)
                end
            end
            wait(.0)
        else
            wait(.0)
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
                Module.FastAttack:AttackFunction()
            end
        end)
    end
end)

spawn(function()
    while task.wait() do
        pcall(function()
            if _G['Fast Attack'] then
                Module.FastAttack.Distance = 150
                Module.FastAttack:BladeHits()
            end
        end)
    end
end)
while true do task.wait()
    for i, v in next, workspace.Enemies:GetChildren() do
        if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
        (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 60 then
            replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
            local args = {
                [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("HumanoidRootPart") or v:FindFirstChildOfClass("BasePart"),
                [2] = {}
            }
            for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    table.insert(args[2], {
                        [1] = e,
                        [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                    })
                end
            end
            replicatedstorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
        end
    end
end

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AutoBuso = true,
    FastAttackRange = tick(),
    WaitTime = tick(),
    IncludeCharacters = true,
    IncludeEnemies = true
}

local lastAttackTime = tick()
local DeoCanOxy = tick()
local OxyChuanBiNap = false

function CheckOxy()
  if type(DeoCanOxy) ~= "number" or not DeoCanOxy then
    DeoCanOxy = tick()
  end
  DeoCanOxy = DeoCanOxy + tick()
  if DeoCanOxy < 200 then
    return "VanOK"
  else
    return "Cuuchiemoi"
  end
end

function Hitoxy()
  if not OxyChuanBiNap then
    OxyChuanBiNap = true
    task.wait(5)
    DeoCanOxy = tick()
    OxyChuanBiNap = false
  else
    return 'Dangnapoxy'
  end
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.FastAttackRange,
        attackMobs = Settings.IncludeEnemies,
        attackPlayers = Settings.IncludeCharacters,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if CheckOxy() == "Cuuchiemoi" then
            Hitoxy()
            return "tutubinhtinh"
        end
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(.0)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
            pcall(function()
                if client.Character:FindFirstChildOfClass("Tool") then
                    for _, v in pairs(client.Character:FindFirstChildOfClass("Tool"):GetDescendants()) do
                        if v:IsA("Animation") then
                            v:Play(0.001, 0.001, 0.001)
                        end
                    end
                end
            end)
        end
    end
    
    function module:GetTargetsFromEnemies()
        local mainTarget = nil
        local otherTargets = {}
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            local UpperTorso = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and Enemy:FindFirstChild("Humanoid") and Enemy.Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not mainTarget then
                    mainTarget = UpperTorso
                else
                    table.insert(otherTargets, {
                        [1] = Enemy,
                        [2] = UpperTorso
                    })
                end
            end
        end
        return mainTarget, otherTargets
    end
    
    function module:AttackNearest()
        if Settings.AutoBuso and client.Character and not client.Character:FindFirstChild("HasBuso") then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
            end)
        end
        local mainTarget, otherTargets = self:GetTargetsFromEnemies()
        if mainTarget then
            self:AttackEnemy(mainTarget, otherTargets)
        end
        if Settings.IncludeCharacters then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character then
                    local UpperTorso = Enemy:FindFirstChild("UpperTorso")
                    if UpperTorso then
                        self:AttackEnemy(UpperTorso)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(Settings.WaitTime)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait(0) do
            pcall(function()
                if Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                    module:BladeHits()
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait()
        pcall(function()
            for i, v in next, workspace.Enemies:GetChildren() do
                if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                    local args = {
                        [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                        [2] = {}
                    }
                    for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                        if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                            table.insert(args[2], {
                                [1] = e,
                                [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                            })
                        end
                    end
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                end
            end
        end)
    end
end)

local Module = {}
Module.AttackCooldown = tick()
local CachedChars = {}

function Module.IsAlive(Char: Model?): boolean
	if not Char then
		return nil
	end
	if CachedChars[Char] then
		return CachedChars[Char].Health > 0
	end
	local Hum = Char:FindFirstChildOfClass("Humanoid")
	CachedChars[Char] = Hum
	return Hum and Hum.Health > 0
end

local Settings = {
    ClickDelay = tick(),
    AutoClick = true,
    AutoBuso = true,
    FastAttackRange = tick(),
    WaitTime = tick(),
    IncludeCharacters = true,
    IncludeEnemies = true
}

local lastAttackTime = tick()
local DeoCanOxy = 0
local OxyChuanBiNap = false

function CheckOxy()
  if type(DeoCanOxy) ~= "number" or not DeoCanOxy then
    DeoCanOxy = 0
  end
  DeoCanOxy = DeoCanOxy + 1
  if DeoCanOxy < 200 then
    return "VanOK"
  else
    return "Cuuchiemoi"
  end
end

function Hitoxy()
  if not OxyChuanBiNap then
    OxyChuanBiNap = true
    task.wait(5)
    DeoCanOxy = 0
    OxyChuanBiNap = false
  else
    return 'Dangnapoxy'
  end
end

Module.FastAttack = (function()
    if environment._trash_attack then
        return environment._trash_attack
    end
    
    local module = {
        NextAttack = (-math.huge^math.huge*math.huge),
        Distance = Settings.FastAttackRange,
        attackMobs = Settings.IncludeEnemies,
        attackPlayers = Settings.IncludeCharacters,
        FirstAttack = false
    }
    
    local RegisterAttack = net:WaitForChild("RE/RegisterAttack")
    local RegisterHit = net:WaitForChild("RE/RegisterHit")
    
    function module:AttackEnemy(EnemyHead, Table)
        if CheckOxy() == "Cuuchiemoi" then
            Hitoxy()
            return "tutubinhtinh"
        end
        if EnemyHead and client:DistanceFromCharacter(EnemyHead.Position) < self.Distance then
            if not self.FirstAttack then
                RegisterAttack:FireServer(0.0001)
                self.FirstAttack = true
            end
            RegisterHit:FireServer(EnemyHead, Table or {})
            pcall(function()
                if client.Character:FindFirstChildOfClass("Tool") then
                    for _, v in pairs(client.Character:FindFirstChildOfClass("Tool"):GetDescendants()) do
                        if v:IsA("Animation") then
                            v:Play(0.001, 0.001, 0.001)
                        end
                    end
                end
            end)
        end
    end
    
    function module:GetTargetsFromEnemies()
        local mainTarget = nil
        local otherTargets = {}
        for _, Enemy in enemyfolder:GetChildren() do
            local HRP = Enemy:FindFirstChild("HumanoidRootPart")
            local UpperTorso = Enemy:FindFirstChild("UpperTorso") or Enemy:FindFirstChild("HumanoidRootPart") or Enemy:FindFirstChildOfClass("BasePart")
            if HRP and Enemy:FindFirstChild("Humanoid") and Enemy.Humanoid.Health > 0 and client:DistanceFromCharacter(HRP.Position) < self.Distance then
                if not mainTarget then
                    mainTarget = UpperTorso
                else
                    table.insert(otherTargets, {
                        [1] = Enemy,
                        [2] = UpperTorso
                    })
                end
            end
        end
        return mainTarget, otherTargets
    end
    
    function module:AttackNearest()
        if Settings.AutoBuso and client.Character and not client.Character:FindFirstChild("HasBuso") then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
            end)
        end
        local mainTarget, otherTargets = self:GetTargetsFromEnemies()
        if mainTarget then
            self:AttackEnemy(mainTarget, otherTargets)
        end
        if Settings.IncludeCharacters then
            for _, Enemy in characterfolder:GetChildren() do
                if Enemy ~= client.Character then
                    local UpperTorso = Enemy:FindFirstChild("UpperTorso")
                    if UpperTorso then
                        self:AttackEnemy(UpperTorso)
                    end
                end
            end
        end
        if not self.FirstAttack then
            task.wait(Settings.WaitTime)
        end
    end
    
    function module:BladeHits()
        self:AttackNearest()
        self.FirstAttack = false
    end

    task.spawn(function()
        while game:GetService("RunService").Stepped:Wait() do
            if (tick() - Module.AttackCooldown) < 0 then continue end
            if not Settings.AutoClick then continue end
            if not Module.IsAlive(client.Character) then continue end
            if not client.Character:FindFirstChildOfClass("Tool") then continue end
            module:BladeHits()
        end
    end)
    
    task.spawn(function()
        while task.wait(0.0001) do
            pcall(function()
                if Module.IsAlive(client.Character) and client.Character:FindFirstChildOfClass("Tool") then
                    module:BladeHits()
                end
            end)
        end
    end)
    environment._trash_attack = module
    return module
end)()

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                        local args = {
                            [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait(0.0001)
        pcall(function()
            for i, v in next, workspace.Enemies:GetChildren() do
                if v.Humanoid and v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= Settings.FastAttackRange then
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.0001)
                    local args = {
                        [1] = v:FindFirstChild("RightHand") or v:FindFirstChild("UpperTorso") or v:FindFirstChild("HumanoidRootPart"),
                        [2] = {}
                    }
                    for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                        if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                            table.insert(args[2], {
                                [1] = e,
                                [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                            })
                        end
                    end
                    game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                end
            end
        end)
    end
end)

local CombatModule = {}
CombatModule.AttackCooldown = tick()
local CachedCharacters = {}

local RegisterAttack = NetworkModule:WaitForChild("RE/RegisterAttack")
local RegisterHit = NetworkModule:WaitForChild("RE/RegisterHit")

function CombatModule.IsCharacterAlive(Character: Model?): boolean
	if not Character then
		return nil
	end

	if CachedCharacters[Character] then
		return CachedCharacters[Character].Health > 0
	end

	local Humanoid = Character:FindFirstChildOfClass("Humanoid")
	CachedCharacters[Character] = Humanoid
	return Humanoid and Humanoid.Health > 0
end

local Settings = {
	ClickDelay = tick(),
	AutoClick = true
}

CombatModule.FastAttack = (function()
	if Environment._fast_attack then
		return Environment._fast_attack
	end

	local FastAttackModule = {
		NextAttack = (-math.huge^math.huge*math.huge),
		AttackRange = tick(),
		AttackMobs = true,
		AttackPlayers = true
	}

	function FastAttackModule:AttackTarget(TargetPart, AdditionalData)
		if TargetPart and Client:DistanceFromCharacter(TargetPart.Position) < self.AttackRange then
			if not self.InitialAttack then
				RegisterAttack:FireServer(.0)
				self.InitialAttack = true
			end
			RegisterHit:FireServer(TargetPart, AdditionalData or {})
		end
	end

	function FastAttackModule:AttackNearbyEntities()
		local attackArgs = {
			[1] = nil,
			[2] = {}
		}
		for _, Enemy in EnemyFolder:GetChildren() do
			local HumanoidRootPart = Enemy:FindFirstChild("HumanoidRootPart", true)
			if HumanoidRootPart and Client:DistanceFromCharacter(HumanoidRootPart.Position) < self.AttackRange then
				if not attackArgs[1] then
					attackArgs[1] = Enemy:FindFirstChild("UpperTorso")
				else
					table.insert(attackArgs[2], {
						[1] = Enemy,
						[2] = Enemy:FindFirstChild("UpperTorso")
					})
				end
			end
		end
		self:AttackTarget(unpack(attackArgs))
		for _, Character in CharacterFolder:GetChildren() do
			if Character ~= Client.Character then
				self:AttackTarget(Character:FindFirstChild("UpperTorso"))
			end
		end
		if not self.InitialAttack then
			task.wait(.0)
		end
	end

	function FastAttackModule:ExecuteBladeHits()
		self:AttackNearbyEntities()
		self.InitialAttack = false
	end

	task.spawn(function()
		while game:GetService("RunService").Stepped:Wait() do
			if (tick() - CombatModule.AttackCooldown) < 0 then continue end
			if not Settings.AutoClick then continue end
			if not CombatModule.IsCharacterAlive(Client.Character) then continue end
			if not Client.Character:FindFirstChildOfClass("Tool") then continue end
			FastAttackModule:ExecuteBladeHits()
		end
	end)
	Environment._fast_attack = FastAttackModule
	return FastAttackModule
end)()

local function GetNearbyTargets(player)
    local targetList = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

spawn(function()
    while true do
        if _G['Fast Attack'] then
            _G['Fast Attack'] = true
            wait(.0)
        else
            _G['Fast Attack'] = false
            wait(.0)
        end
        if _G['Fast Attack'] then
            local targetList = GetNearbyTargets(game.Players.LocalPlayer)
            if #targetList > 0 then
                RegisterAttack:FireServer(.0)
                for _, target in next, targetList do
                    RegisterHit:FireServer(targetList[_][2], targetList)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                        ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                        local args = {
                            [1] = v:FindFirstChild("RightHand"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait()
        for i, v in next, workspace.Enemies:GetChildren() do
            if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
            (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0)
                local args = {
                    [1] = v:FindFirstChild("RightHand"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

local CombatModule = {}
CombatModule.AttackCooldown = tick()
local CachedCharacters = {}

local RegisterAttack = NetworkModule:WaitForChild("RE/RegisterAttack")
local RegisterHit = NetworkModule:WaitForChild("RE/RegisterHit")

function CombatModule.IsCharacterAlive(Character: Model?): boolean
	if not Character then
		return nil
	end

	if CachedCharacters[Character] then
		return CachedCharacters[Character].Health > 0
	end

	local Humanoid = Character:FindFirstChildOfClass("Humanoid")
	CachedCharacters[Character] = Humanoid
	return Humanoid and Humanoid.Health > 0
end

local Settings = {
	ClickDelay = tick(),
	AutoClick = true
}

CombatModule.FastAttack = (function()
	if Environment._fast_attack then
		return Environment._fast_attack
	end

	local FastAttackModule = {
		NextAttack = (-math.huge^math.huge*math.huge),
		AttackRange = tick(),
		AttackMobs = true,
		AttackPlayers = true
	}

	function FastAttackModule:AttackTarget(TargetPart, AdditionalData)
		if TargetPart and Client:DistanceFromCharacter(TargetPart.Position) < self.AttackRange then
			if not self.InitialAttack then
				RegisterAttack:FireServer(0.01)
				self.InitialAttack = true
			end
			RegisterHit:FireServer(TargetPart, AdditionalData or {})
		end
	end

	function FastAttackModule:AttackNearbyEntities()
		local attackArgs = {
			[1] = nil,
			[2] = {}
		}
		for _, Enemy in EnemyFolder:GetChildren() do
			local HumanoidRootPart = Enemy:FindFirstChild("HumanoidRootPart", true)
			if HumanoidRootPart and Client:DistanceFromCharacter(HumanoidRootPart.Position) < self.AttackRange then
				if not attackArgs[1] then
					attackArgs[1] = Enemy:FindFirstChild("UpperTorso")
				else
					table.insert(attackArgs[2], {
						[1] = Enemy,
						[2] = Enemy:FindFirstChild("UpperTorso")
					})
				end
			end
		end
		self:AttackTarget(unpack(attackArgs))
		for _, Character in CharacterFolder:GetChildren() do
			if Character ~= Client.Character then
				self:AttackTarget(Character:FindFirstChild("UpperTorso"))
			end
		end
		if not self.InitialAttack then
			task.wait(0.01)
		end
	end

	function FastAttackModule:ExecuteBladeHits()
		self:AttackNearbyEntities()
		self.InitialAttack = false
	end

	task.spawn(function()
		while game:GetService("RunService").Stepped:Wait() do
			if (tick() - CombatModule.AttackCooldown) < 0 then continue end
			if not Settings.AutoClick then continue end
			if not CombatModule.IsCharacterAlive(Client.Character) then continue end
			if not Client.Character:FindFirstChildOfClass("Tool") then continue end
			FastAttackModule:ExecuteBladeHits()
		end
	end)
	Environment._fast_attack = FastAttackModule
	return FastAttackModule
end)()

local function GetNearbyTargets(player)
    local targetList = {}
    for _, obj in pairs(workspace.Characters:GetChildren()) do
        if obj ~= player.Character and obj:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj, obj.HumanoidRootPart})
        end
    end
    for _, obj2 in pairs(workspace.Enemies:GetChildren()) do
        if obj2:FindFirstChild("HumanoidRootPart") and player:DistanceFromCharacter(obj2.HumanoidRootPart.Position) < 200 then
            table.insert(targetList, {obj2, obj2.HumanoidRootPart})
        end
    end
    return targetList
end

spawn(function()
    while true do
        if _G['Fast Attack'] then
            _G['Fast Attack'] = true
            wait(0.01)
        else
            _G['Fast Attack'] = false
            wait(0.01)
        end
        if _G['Fast Attack'] then
            local targetList = GetNearbyTargets(game.Players.LocalPlayer)
            if #targetList > 0.01 then
                RegisterAttack:FireServer(0.01)
                for _, target in next, targetList do
                    RegisterHit:FireServer(targetList[_][2], targetList)
                end
            end
        end
    end
end)

spawn(function()
    while true do task.wait(0.01)
        pcall(function()
            if _G['Fast Attack'] then
                for i, v in next, workspace.Enemies:GetChildren() do
                    if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
                    (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                        ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                        local args = {
                            [1] = v:FindFirstChild("RightHand"),
                            [2] = {}
                        }
                        for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                            if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                                table.insert(args[2], {
                                    [1] = e,
                                    [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                                })
                            end
                        end
                        ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
                    end
                end
            end
        end)
    end
end)

spawn(function()
    while true do task.wait(0.01)
        for i, v in next, workspace.Enemies:GetChildren() do
            if v.Humanoid.Health > 0 and v:FindFirstChild("HumanoidRootPart") and 
            (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= tonumber(60) then
                ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack"):FireServer(0.01)
                local args = {
                    [1] = v:FindFirstChild("RightHand"),
                    [2] = {}
                }
                for _, e in next, workspace:WaitForChild("Enemies"):GetChildren() do
                    if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                        table.insert(args[2], {
                            [1] = e,
                            [2] = e:FindFirstChild("HumanoidRootPart") or e:FindFirstChildOfClass("BasePart")
                        })
                    end
                end
                ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
            end
        end
    end
end)

task.spawn(function()
    while true do
        if _G['Fast Attack'] then
            AttackModule:BladeHits()
        end
        task.wait(.0)
    end
end)

spawn(function()
    game:GetService("RunService").RenderStepped:Connect(function()
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end)
end)

spawn(function()
    while true do task.wait()
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(0.1)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(0.01)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(0.001)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(.0)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(.1)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(.2)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)

spawn(function()
    while true do task.wait(.3)
        if _G['Fast Attack'] then
             pcall(function()
                game:GetService'VirtualUser':CaptureController()
			    game:GetService'VirtualUser':Button1Down(Vector2.new(0,1,0,1))
            end)
        end
    end
end)
