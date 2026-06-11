--[[
DELTA HUB - UI ORIGINAL + TODOS OS COMANDOS (CONCH STYLE)
--]]

local l_Players = game:GetService("Players")
local l_UserInputService = game:GetService("UserInputService")
local l_RunService = game:GetService("RunService")
local l_LocalPlayer = l_Players.LocalPlayer
local l_Camera = workspace.CurrentCamera

if not l_LocalPlayer then
    l_LocalPlayer = l_Players.LocalPlayer or l_Players:WaitForChild("LocalPlayer")
end

local playerGui = l_LocalPlayer:WaitForChild("PlayerGui")

local CONFIG = {
    BG_COLOR = Color3.fromRGB(13, 24, 27),
    BG_TRANSPARENCY = 0.2,
    ACCENT = Color3.fromRGB(3, 182, 255),
    TEXT_NORMAL = Color3.fromRGB(220, 220, 220),
    TEXT_YELLOW = Color3.fromRGB(212, 175, 55),
    FONT = Enum.Font.Code
}

-- ==========================================
-- LISTA DE COMANDOS COMPLETA (CONCH STYLE)
-- ==========================================
local COMMANDS = {
    {name = "aimbot", info = "<força>", desc = "Mira automática (0.1 a 10)"},
    {name = "farm", info = "", desc = "Coleta moedas automaticamente"},
    {name = "teleport", info = "<player/spawn>", desc = "Teleporta para jogador ou spawn"},
    {name = "speed", info = "<valor>", desc = "Altera velocidade"},
    {name = "size", info = "<valor>", desc = "Altera tamanho do personagem"},
    {name = "godmode", info = "", desc = "Vida infinita"},
    {name = "fly", info = "", desc = "Voa pelo mapa"},
    {name = "esp", info = "", desc = "Mostra players através das paredes"},
    {name = "espnpc", info = "", desc = "Mostra NPCs através das paredes"},
    {name = "spin", info = "<velocidade>", desc = "Gira o personagem"},
    {name = "jumppower", info = "<valor>", desc = "Altura do pulo"},
    {name = "gravity", info = "<valor>", desc = "Muda gravidade"},
    {name = "noclip", info = "", desc = "Atravessa paredes"},
    {name = "clip", info = "", desc = "Reativa colisões"},
    {name = "infinitejump", info = "", desc = "Pulos infinitos"},
    {name = "invisible", info = "", desc = "Fica invisível"},
    {name = "rejoin", info = "", desc = "Reconecta ao servidor"},
    {name = "reset", info = "", desc = "Suicídio"},
    {name = "kill", info = "", desc = "Suicídio"},
    {name = "btools", info = "", desc = "Ferramentas de construção"},
    {name = "freezecam", info = "", desc = "Trava câmera"},
    {name = "unfreezecam", info = "", desc = "Destrava câmera"},
    {name = "clearlogs", info = "", desc = "Limpa histórico"},
    {name = "heal", info = "", desc = "Cura total"},
    {name = "anti-afk", info = "", desc = "Evita desconexão"},
    {name = "teleporttools", info = "", desc = "Ferramenta de teleporte"},
    {name = "destroyui", info = "", desc = "Remove o hub"},
    {name = "close-ui", info = "", desc = "Fecha painel de comandos"},
    {name = "speedhat", info = "", desc = "Velocidade por chapéus"},
    {name = "ff", info = "", desc = "Escudo de força"},
    {name = "unff", info = "", desc = "Remove escudo"},
    {name = "sit", info = "", desc = "Senta"},
    {name = "setfps", info = "<valor>", desc = "Limita FPS"},
    {name = "fhov", info = "<valor>", desc = "Campo de visão"},
    {name = "light", info = "", desc = "Lanterna no personagem"},
    {name = "unlight", info = "", desc = "Remove lanterna"},
    {name = "bighead", info = "", desc = "Cabeça grande"},
    {name = "normalhead", info = "", desc = "Cabeça normal"},
    {name = "jail", info = "<player>", desc = "Prende jogador"},
    {name = "unjail", info = "<player>", desc = "Solta jogador"},
    {name = "anim", info = "<id/player/stop>", desc = "Copia animação"},
    {name = "stopanim", info = "", desc = "Para animações"},
    {name = "time", info = "<0-24>", desc = "Muda hora"},
    {name = "brightness", info = "<valor>", desc = "Brilho"},
    {name = "fog", info = "<valor>", desc = "Neblina"},
    {name = "unfog", info = "", desc = "Remove neblina"},
    {name = "name", info = "<texto>", desc = "Nome exibido"},
    {name = "chat", info = "<msg>", desc = "Envia mensagem"},
    {name = "zoom", info = "<valor>", desc = "Zoom máximo"},
    {name = "freeze", info = "", desc = "Congela personagem"},
    {name = "unfreeze", info = "", desc = "Descongela"},
    {name = "platform", info = "", desc = "Cria plataforma"},
    {name = "unplatform", info = "", desc = "Remove plataforma"},
    {name = "attachcam", info = "<player>", desc = "Câmera segue player"},
    {name = "detachcam", info = "", desc = "Solta câmera"},
    {name = "rainbow", info = "", desc = "Cores coloridas"},
    {name = "unrainbow", info = "", desc = "Remove rainbow"},
    {name = "nametag", info = "<texto>", desc = "Tag flutuante"},
    {name = "removetag", info = "", desc = "Remove tag"},
    {name = "annoy", info = "<player>", desc = "Gruda no player"},
    {name = "unannoy", info = "", desc = "Para de grudar"},
    {name = "reach", info = "<valor>", desc = "Aumenta alcance"},
    {name = "morph", info = "<id/player>", desc = "Copia roupas/acessórios"},
}

-- ==========================================
-- VARIÁVEIS GLOBAIS (para os comandos)
-- ==========================================
local aimbotActive, aimbotConnection = false, nil
local farmActive, farmConnection = false, nil
local godmodeActive, godmodeConnection = false, nil
local flyActive, flyConnection, flySpeed = false, nil, 50
local espActive, espHighlights = false, {}
local espNpcActive, espNpcHighlights, npcConnection = false, {}, nil
local spinmodeActive, spinSpeed, spinConnection = false, 50, nil
local noclipActive, noclipConnection = false, nil
local infJumpActive, infJumpConnection = false, nil
local antiAfkConnection = nil
local loadedAnimTracks = {}
local rainbowConnection = nil
local annoyConnection, attachCamConnection, platformPart = nil, nil, nil

-- ==========================================
-- FUNÇÕES AUXILIARES (getPlayer)
-- ==========================================
local function getPlayer(name)
    for _, p in pairs(l_Players:GetPlayers()) do
        if p.Name:lower():find(name:lower()) and p ~= l_LocalPlayer then
            return p
        end
    end
    return nil
end

-- ==========================================
-- AIMBOT
-- ==========================================
local function getClosestEnemy()
    local closest, dist = nil, math.huge
    for _, p in pairs(l_Players:GetPlayers()) do
        if p ~= l_LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
            local d = (p.Character.Head.Position - l_LocalPlayer.Character.Head.Position).Magnitude
            if d < dist then dist = d; closest = p end
        end
    end
    return closest
end

local function enableAimbot(s)
    if aimbotConnection then aimbotConnection:Disconnect() end
    s = math.max(0.1, math.min(tonumber(s) or 1, 10))
    aimbotConnection = l_RunService.RenderStepped:Connect(function()
        if not aimbotActive or not l_LocalPlayer.Character then return end
        local enemy = getClosestEnemy()
        if enemy and enemy.Character and enemy.Character:FindFirstChild("Head") then
            local targetPos = enemy.Character.Head.Position
            local camPos = l_Camera.CFrame.Position
            local dir = (targetPos - camPos).Unit
            l_Camera.CFrame = l_Camera.CFrame:Lerp(CFrame.new(camPos, camPos + dir), 0.1 * s)
        end
    end)
end
local function disableAimbot() if aimbotConnection then aimbotConnection:Disconnect() aimbotConnection = nil end end

-- ==========================================
-- FARM
-- ==========================================
local function enableFarm()
    if farmConnection then farmConnection:Disconnect() end
    farmConnection = l_RunService.Heartbeat:Connect(function()
        if not farmActive or not l_LocalPlayer.Character then return end
        for _, item in pairs(workspace:GetDescendants()) do
            if item:IsA("Part") or item:IsA("Model") then
                local name = item.Name:lower()
                if name:find("coin") or name:find("moeda") or name:find("money") or name:find("cash") then
                    if item:FindFirstChild("ClickDetector") then pcall(function() item.ClickDetector:FireServer() end)
                    elseif item.Parent and item.Parent:FindFirstChild("ClickDetector") then pcall(function() item.Parent.ClickDetector:FireServer() end) end
                end
            end
        end
    end)
end
local function disableFarm() if farmConnection then farmConnection:Disconnect() farmConnection = nil end end

-- ==========================================
-- GODMODE
-- ==========================================
local function enableGodmode()
    if godmodeConnection then godmodeConnection:Disconnect() end
    godmodeConnection = l_RunService.Heartbeat:Connect(function()
        if godmodeActive and l_LocalPlayer.Character then
            local h = l_LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if h then h.Health = h.MaxHealth end
        end
    end)
end
local function disableGodmode() if godmodeConnection then godmodeConnection:Disconnect() godmodeConnection = nil end end

-- ==========================================
-- FLY
-- ==========================================
local function disableFly()
    if flyConnection then flyConnection:Disconnect() flyConnection = nil end
    local folder = workspace:FindFirstChild("FlyLocalSystem"); if folder then folder:Destroy() end
    local char = l_LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if root then for _, obj in pairs(root:GetChildren()) do if obj:IsA("BodyVelocity") or obj:IsA("BodyGyro") then obj:Destroy() end end end
    if hum then hum.PlatformStand = false end
end

local function enableFly()
    if flyConnection then flyConnection:Disconnect() end
    local char = l_LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if not root or not hum then return end
    local old = workspace:FindFirstChild("FlyLocalSystem"); if old then old:Destroy() end
    local flyFolder = Instance.new("Folder", workspace); flyFolder.Name = "FlyLocalSystem"
    local bg = Instance.new("BodyGyro", root); bg.maxTorque = Vector3.new(9e9,9e9,9e9); bg.p = 9e4; bg.cframe = root.CFrame
    local bv = Instance.new("BodyVelocity", root); bv.velocity = Vector3.new(0,0.1,0); bv.maxForce = Vector3.new(9e9,9e9,9e9)
    hum.PlatformStand = true
    flyConnection = l_RunService.RenderStepped:Connect(function()
        if not flyActive or not l_LocalPlayer.Character or not root.Parent then disableFly() return end
        local move = Vector3.new()
        if l_UserInputService:IsKeyDown(Enum.KeyCode.W) then move = move + l_Camera.CFrame.LookVector end
        if l_UserInputService:IsKeyDown(Enum.KeyCode.S) then move = move - l_Camera.CFrame.LookVector end
        if l_UserInputService:IsKeyDown(Enum.KeyCode.A) then move = move - l_Camera.CFrame.RightVector end
        if l_UserInputService:IsKeyDown(Enum.KeyCode.D) then move = move + l_Camera.CFrame.RightVector end
        if l_UserInputService:IsKeyDown(Enum.KeyCode.Space) then move = move + Vector3.new(0,1,0) end
        if l_UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then move = move - Vector3.new(0,1,0) end
        bg.cframe = l_Camera.CFrame
        bv.velocity = move.Magnitude > 0 and move.Unit * flySpeed or Vector3.new(0,0.1,0)
    end)
end

-- ==========================================
-- SPIN
-- ==========================================
local function disableSpinmode()
    if spinConnection then spinConnection:Disconnect() spinConnection = nil end
    local char = l_LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hum then hum.AutoRotate = true end
    if hrp and hrp:FindFirstChild("SpinVelocity") then hrp.SpinVelocity:Destroy() end
end

local function enableSpinmode()
    if spinConnection then spinConnection:Disconnect() end
    local char = l_LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp or not hum then return end
    hum.AutoRotate = false
    local bv = hrp:FindFirstChild("SpinVelocity") or Instance.new("BodyAngularVelocity"); bv.Name = "SpinVelocity"
    bv.AngularVelocity = Vector3.new(0, spinSpeed, 0); bv.MaxTorque = Vector3.new(0, math.huge, 0); bv.Parent = hrp
    spinConnection = l_RunService.Heartbeat:Connect(function()
        if not spinmodeActive or not l_LocalPlayer.Character then disableSpinmode() return end
        local h = l_LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        local sv = h and h:FindFirstChild("SpinVelocity")
        if sv then sv.AngularVelocity = Vector3.new(0, spinSpeed, 0) end
    end)
end

-- ==========================================
-- TELEPORT
-- ==========================================
local function teleportToPlayer(name)
    local p = getPlayer(name)
    if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
        if l_LocalPlayer.Character and l_LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            l_LocalPlayer.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame + Vector3.new(5,0,0)
            return true
        end
    end
    return false
end

local function teleportToSpawn()
    local spawn = workspace:FindFirstChild("SpawnLocation") or workspace:FindFirstChildOfClass("SpawnLocation")
    if spawn and l_LocalPlayer.Character and l_LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        l_LocalPlayer.Character.HumanoidRootPart.CFrame = spawn.CFrame + Vector3.new(0,5,0)
        return true
    end
    return false
end

-- ==========================================
-- MORPH
-- ==========================================
local function applyMorph(id, targetPlayer)
    local char = l_LocalPlayer.Character
    if not char then return false end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return false end
    for _,c in pairs(char:GetChildren()) do
        if c:IsA("Shirt") or c:IsA("Pants") or c:IsA("ShirtGraphic") then c:Destroy() end
        if c:IsA("Accessory") then c:Destroy() end
    end
    if type(id)=="number" or (type(id)=="string" and tonumber(id)) then
        local aid = tonumber(id)
        local ns = Instance.new("Shirt", char); ns.ShirtTemplate = "rbxassetid://"..aid
        local np = Instance.new("Pants", char); np.PantsTemplate = "rbxassetid://"..aid
        return true
    elseif targetPlayer and targetPlayer.Character then
        local tChar = targetPlayer.Character
        for _,acc in pairs(tChar:GetChildren()) do
            if acc:IsA("Accessory") then
                local new = acc:Clone(); new.Parent = char
                local h = new:FindFirstChild("Handle")
                if h then h.CFrame = (acc:FindFirstChild("Handle") and acc.Handle.CFrame) or CFrame.new() end
            end
        end
        local shirt = tChar:FindFirstChild("Shirt"); if shirt then local ns=Instance.new("Shirt",char); ns.ShirtTemplate = shirt.ShirtTemplate end
        local pants = tChar:FindFirstChild("Pants"); if pants then local np=Instance.new("Pants",char); np.PantsTemplate = pants.PantsTemplate end
        local graphic = tChar:FindFirstChild("ShirtGraphic"); if graphic then local ng=Instance.new("ShirtGraphic",char); ng.Graphic = graphic.Graphic end
        return true
    end
    return false
end

-- ==========================================
-- ANIMAÇÕES
-- ==========================================
local function getWorkspaceAnimations()
    local found, seen = {}, {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Humanoid") and obj.Parent ~= l_LocalPlayer.Character then
            local model = obj.Parent and obj.Parent.Name or "Unknown"
            local animator = obj:FindFirstChildOfClass("Animator")
            if animator then
                for _, track in pairs(animator:GetPlayingAnimationTracks()) do
                    if track.Animation and track.Animation.AnimationId ~= "" then
                        local id = track.Animation.AnimationId:gsub("rbxassetid://","")
                        local label = model.." ("..(track.Name~="" and track.Name or id)..")"
                        if not seen[label] then seen[label]=true; table.insert(found,{name=label,id=id}) end
                    end
                end
            end
        end
    end
    return found
end

local function stopAllAnims()
    for _,track in pairs(loadedAnimTracks) do pcall(function() track:Stop() end) end
    loadedAnimTracks = {}
end

local function playAnim(animId)
    local char = l_LocalPlayer.Character; local hum = char and char:FindFirstChildOfClass("Humanoid")
    if not hum then return false end
    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then animator = Instance.new("Animator", hum) end
    stopAllAnims()
    local anim = Instance.new("Animation"); anim.AnimationId = "rbxassetid://"..animId
    local ok, track = pcall(function() return animator:LoadAnimation(anim) end)
    if ok and track then track.Priority = Enum.AnimationPriority.Action4; track:Play(); table.insert(loadedAnimTracks, track); return true end
    return false
end

-- ==========================================
-- ESP
-- ==========================================
local function createESP(obj, isNpc)
    if not obj or not obj:FindFirstChild("Head") then return end
    local f = Instance.new("Folder", obj); f.Name = "ConchESP_"..obj.Name
    local h = Instance.new("Highlight", f); h.Adornee = obj; h.FillColor = Color3.fromRGB(255,0,0); h.OutlineColor = Color3.fromRGB(200,0,0); h.FillTransparency = 0.4
    local bb = Instance.new("BillboardGui", f); bb.Adornee = obj.Head; bb.Size = UDim2.new(0,200,0,50); bb.StudsOffset = Vector3.new(0,2.5,0); bb.AlwaysOnTop = true
    local lbl = Instance.new("TextLabel", bb); lbl.Size = UDim2.new(1,0,1,0); lbl.BackgroundTransparency = 1; lbl.Text = (isNpc and "[NPC] " or "")..obj.Name; lbl.TextColor3 = Color3.fromRGB(255,0,0); lbl.TextStrokeTransparency = 0; lbl.Font = CONFIG.FONT; lbl.TextSize = 16
    if isNpc then espNpcHighlights[obj] = f else espHighlights[obj] = f end
end

local function removeESP(obj, isNpc)
    local cache = isNpc and espNpcHighlights or espHighlights
    if cache[obj] then cache[obj]:Destroy(); cache[obj] = nil end
end

local function enableESP()
    for _,p in pairs(l_Players:GetPlayers()) do if p~=l_LocalPlayer and p.Character then createESP(p.Character,false) end end
    l_Players.PlayerAdded:Connect(function(p) p.CharacterAdded:Connect(function(c) if espActive then task.wait(0.2); createESP(c,false) end end) end)
end
local function disableESP() for obj in pairs(espHighlights) do removeESP(obj,false) end espHighlights = {} end

local function enableNpcESP()
    local function check(v) if v:IsA("Model") and v:FindFirstChildOfClass("Humanoid") and v:FindFirstChild("Head") and not l_Players:GetPlayerFromCharacter(v) and v~=l_LocalPlayer.Character then createESP(v,true) end end
    for _,v in pairs(workspace:GetDescendants()) do check(v) end
    npcConnection = workspace.DescendantAdded:Connect(function(v) if espNpcActive then task.wait(0.2); check(v) end end)
end
local function disableNpcESP() if npcConnection then npcConnection:Disconnect(); npcConnection=nil end for obj in pairs(espNpcHighlights) do removeESP(obj,true) end espNpcHighlights={} end

-- ==========================================
-- JAIL
-- ==========================================
local function createJail(p)
    if not p or not p.Character or not p.Character:FindFirstChild("HumanoidRootPart") then return false end
    local name = "JailOf_"..p.Name; local old = workspace:FindFirstChild(name); if old then old:Destroy() end
    local pos = p.Character.HumanoidRootPart.Position; local folder = Instance.new("Folder", workspace); folder.Name = name
    local s = 10; local t = 0.5
    local walls = {
        {cf = CFrame.new(pos+Vector3.new(0,s/2,-s/2)), sz = Vector3.new(s,s,t)},
        {cf = CFrame.new(pos+Vector3.new(0,s/2,s/2)), sz = Vector3.new(s,s,t)},
        {cf = CFrame.new(pos+Vector3.new(-s/2,s/2,0)), sz = Vector3.new(t,s,s)},
        {cf = CFrame.new(pos+Vector3.new(s/2,s/2,0)), sz = Vector3.new(t,s,s)},
        {cf = CFrame.new(pos+Vector3.new(0,s,0)), sz = Vector3.new(s,t,s)},
        {cf = CFrame.new(pos+Vector3.new(0,0,0)), sz = Vector3.new(s,t,s)},
    }
    for _,w in pairs(walls) do
        local part = Instance.new("Part", folder); part.Anchored = true; part.CanCollide = true; part.CFrame = w.cf; part.Size = w.sz; part.Transparency = 1; part.Material = Enum.Material.Glass; part.Name = "JailWall"
    end
    return true
end
local function removeJail(p)
    local jail = workspace:FindFirstChild("JailOf_"..p.Name)
    if jail then jail:Destroy(); return true end
    return false
end

-- ==========================================
-- INTERFACE ORIGINAL (idêntica ao script "somente kick")
-- ==========================================
local ScreenGui = Instance.new("ScreenGui", playerGui)
ScreenGui.Name = "Delta_Conch_Hub"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999
ScreenGui.IgnoreGuiInset = true

local ToggleButton = Instance.new("TextButton", ScreenGui)
ToggleButton.Size = UDim2.new(0, 44, 0, 44)
ToggleButton.Position = UDim2.new(0, 10, 0, 120)
ToggleButton.AnchorPoint = Vector2.new(0, 0)
ToggleButton.BackgroundColor3 = CONFIG.BG_COLOR
ToggleButton.BackgroundTransparency = 0.1
ToggleButton.Text = ">_"
ToggleButton.TextColor3 = CONFIG.ACCENT
ToggleButton.Font = CONFIG.FONT
ToggleButton.TextSize = 18
ToggleButton.ZIndex = 10
ToggleButton.Visible = true
Instance.new("UICorner", ToggleButton).CornerRadius = UDim.new(1, 0)

local Container = Instance.new("Frame", ScreenGui)
Container.Size = UDim2.new(0.98, 0, 0, 0)
Container.Position = UDim2.new(0.5, 0, 1, -10)
Container.AnchorPoint = Vector2.new(0.5, 1)
Container.BackgroundTransparency = 1
Container.AutomaticSize = Enum.AutomaticSize.Y
Container.Visible = false
Container.ZIndex = 9

local UIList = Instance.new("UIListLayout", Container)
UIList.VerticalAlignment = Enum.VerticalAlignment.Bottom
UIList.SortOrder = Enum.SortOrder.LayoutOrder
UIList.Padding = UDim.new(0, 6)

local LogFrame = Instance.new("Frame", Container)
LogFrame.Name = "1_LogFrame"
LogFrame.Size = UDim2.new(1, 0, 0, 0)
LogFrame.AutomaticSize = Enum.AutomaticSize.Y
LogFrame.BackgroundColor3 = CONFIG.BG_COLOR
LogFrame.BackgroundTransparency = CONFIG.BG_TRANSPARENCY
LogFrame.BorderSizePixel = 0
LogFrame.LayoutOrder = 1
Instance.new("UICorner", LogFrame).CornerRadius = UDim.new(0, 4)

local logPadding = Instance.new("UIPadding", LogFrame)
logPadding.PaddingLeft = UDim.new(0, 14)
logPadding.PaddingTop = UDim.new(0, 10)
logPadding.PaddingBottom = UDim.new(0, 10)

local logList = Instance.new("UIListLayout", LogFrame)
logList.SortOrder = Enum.SortOrder.LayoutOrder

local TextBox = Instance.new("TextBox", Container)
TextBox.Name = "2_TextBox"
TextBox.Size = UDim2.new(1, 0, 0, 38)
TextBox.BackgroundColor3 = CONFIG.BG_COLOR
TextBox.BackgroundTransparency = CONFIG.BG_TRANSPARENCY
TextBox.TextColor3 = CONFIG.TEXT_NORMAL
TextBox.Font = CONFIG.FONT
TextBox.TextSize = 18
TextBox.TextXAlignment = Enum.TextXAlignment.Left
TextBox.PlaceholderText = "Enter your command"
TextBox.Text = ""
TextBox.BorderSizePixel = 0
TextBox.LayoutOrder = 2
Instance.new("UICorner", TextBox).CornerRadius = UDim.new(0, 4)

local textPadding = Instance.new("UIPadding", TextBox)
textPadding.PaddingLeft = UDim.new(0, 14)

local SuggestionFrame = Instance.new("Frame", TextBox)
SuggestionFrame.Size = UDim2.new(0, 380, 0, 0)
SuggestionFrame.Position = UDim2.new(0, 14, 0, -6)
SuggestionFrame.AnchorPoint = Vector2.new(0, 1)
SuggestionFrame.BackgroundColor3 = CONFIG.BG_COLOR
SuggestionFrame.BackgroundTransparency = 0.1
SuggestionFrame.Visible = false
SuggestionFrame.BorderSizePixel = 0
Instance.new("UIListLayout", SuggestionFrame).SortOrder = Enum.SortOrder.LayoutOrder

local function addLog(txt, col)
    local l = Instance.new("TextLabel", LogFrame)
    l.BackgroundTransparency = 1
    l.Size = UDim2.new(1, 0, 0, 20)
    l.Font = CONFIG.FONT
    l.TextSize = 18
    l.TextColor3 = col or CONFIG.TEXT_NORMAL
    l.Text = txt
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.RichText = true
    l.TextWrapped = true
end

local function showInfo()
    addLog("Conch 0.2.x", Color3.fromRGB(255,255,255))
    addLog("Copyright (c) alicesays_hallo - Licensed under MIT, you can view the included license with 'license'", Color3.fromRGB(255,255,255))
    addLog("If you got here accidentally, run the 'close-ui' command to close this UI.", Color3.fromRGB(232,183,54))
end
showInfo()

local simbolos = {"-", "/", "|", "\\"}
local function runLoadingInInput(seconds)
    local startTime = tick()
    local i = 1
    TextBox.TextEditable = false
    while tick() - startTime < seconds do
        TextBox.Text = simbolos[i] .. " " .. string.format("%.1f", tick() - startTime) .. "s"
        i = (i % #simbolos) + 1
        task.wait(0.1)
    end
    TextBox.Text = ""
    TextBox.TextEditable = true
end

-- ==========================================
-- SUGESTÕES DINÂMICAS (adaptada para todos os comandos)
-- ==========================================
TextBox:GetPropertyChangedSignal("Text"):Connect(function()
    if not TextBox.TextEditable then return end
    for _, v in pairs(SuggestionFrame:GetChildren()) do
        if v:IsA("Frame") or v:IsA("TextButton") or v:IsA("TextLabel") then v:Destroy() end
    end
    local text = TextBox.Text
    if text == "" then SuggestionFrame.Visible = false return end
    local newXPosition = 14 + (#text * 10.8)
    SuggestionFrame.Position = UDim2.new(0, newXPosition, 0, -6)
    local args = string.split(text, " ")
    local cmdPart = args[1]:lower()
    local count = 0

    -- Comandos que aceitam jogador como argumento
    local playerCmds = {
        teleport=true, jail=true, unjail=true, annoy=true, attachcam=true, morph=true
    }

    if #args > 1 then
        if playerCmds[cmdPart] then
            local typed = args[2]:lower()
            for _, p in pairs(l_Players:GetPlayers()) do
                if p ~= l_LocalPlayer and p.Name:lower():sub(1, #typed) == typed then
                    count = count + 1
                    if count > 6 then break end
                    local frame = Instance.new("Frame", SuggestionFrame)
                    frame.Size = UDim2.new(1, 0, 0, 32)
                    frame.BackgroundTransparency = 1
                    local btn = Instance.new("TextButton", frame)
                    btn.Size = UDim2.new(1, 0, 1, 0)
                    btn.BackgroundTransparency = 1
                    btn.Text = ""
                    local nameLabel = Instance.new("TextLabel", frame)
                    nameLabel.Size = UDim2.new(0.5, 0, 1, 0)
                    nameLabel.Position = UDim2.new(0, 8, 0, 0)
                    nameLabel.BackgroundTransparency = 1
                    nameLabel.Text = p.Name
                    nameLabel.TextColor3 = CONFIG.TEXT_YELLOW
                    nameLabel.Font = CONFIG.FONT
                    nameLabel.TextSize = 18
                    nameLabel.TextXAlignment = Enum.TextXAlignment.Left
                    local descLabel = Instance.new("TextLabel", frame)
                    descLabel.Size = UDim2.new(0.45, 0, 1, 0)
                    descLabel.Position = UDim2.new(0.52, 0, 0, 0)
                    descLabel.BackgroundTransparency = 1
                    descLabel.Text = "@" .. p.Name
                    descLabel.TextColor3 = Color3.fromRGB(200,200,200)
                    descLabel.Font = CONFIG.FONT
                    descLabel.TextSize = 18
                    descLabel.TextXAlignment = Enum.TextXAlignment.Left
                    btn.MouseButton1Click:Connect(function()
                        TextBox.Text = cmdPart .. " " .. p.Name
                        TextBox:CaptureFocus()
                    end)
                end
            end
        elseif cmdPart == "anim" then
            local typed = args[2] and args[2]:lower() or ""
            local anims = getWorkspaceAnimations()
            if #anims == 0 then
                count = 1
                local label = Instance.new("TextLabel", SuggestionFrame)
                label.Size = UDim2.new(1, 0, 0, 32)
                label.BackgroundTransparency = 1
                label.Text = "  Nenhuma animação encontrada"
                label.TextColor3 = Color3.fromRGB(255,80,80)
                label.Font = CONFIG.FONT
                label.TextSize = 18
                label.TextXAlignment = Enum.TextXAlignment.Left
            else
                for _, anim in pairs(anims) do
                    if typed == "" or anim.name:lower():find(typed) then
                        count = count + 1
                        if count > 6 then break end
                        local frame = Instance.new("Frame", SuggestionFrame)
                        frame.Size = UDim2.new(1, 0, 0, 32)
                        frame.BackgroundTransparency = 1
                        local btn = Instance.new("TextButton", frame)
                        btn.Size = UDim2.new(1, 0, 1, 0)
                        btn.BackgroundTransparency = 1
                        btn.Text = ""
                        local nameLabel = Instance.new("TextLabel", frame)
                        nameLabel.Size = UDim2.new(1, 0, 1, 0)
                        nameLabel.Position = UDim2.new(0, 8, 0, 0)
                        nameLabel.BackgroundTransparency = 1
                        nameLabel.Text = "  " .. anim.name
                        nameLabel.TextColor3 = CONFIG.TEXT_YELLOW
                        nameLabel.Font = CONFIG.FONT
                        nameLabel.TextSize = 16
                        nameLabel.TextXAlignment = Enum.TextXAlignment.Left
                        btn.MouseButton1Click:Connect(function()
                            TextBox.Text = "anim " .. anim.id
                            TextBox:CaptureFocus()
                        end)
                    end
                end
            end
        end
    else
        for _, data in pairs(COMMANDS) do
            if data.name:sub(1, #cmdPart) == cmdPart and text:lower() ~= data.name then
                count = count + 1
                local frame = Instance.new("Frame", SuggestionFrame)
                frame.Size = UDim2.new(1, 0, 0, 38)
                frame.BackgroundTransparency = 1
                local btn = Instance.new("TextButton", frame)
                btn.Size = UDim2.new(1, 0, 1, 0)
                btn.BackgroundTransparency = 1
                btn.Text = ""
                local cmdLabel = Instance.new("TextLabel", frame)
                cmdLabel.Size = UDim2.new(0.4, 0, 1, 0)
                cmdLabel.Position = UDim2.new(0, 8, 0, 0)
                cmdLabel.BackgroundTransparency = 1
                cmdLabel.Text = data.name
                cmdLabel.TextColor3 = CONFIG.ACCENT
                cmdLabel.Font = CONFIG.FONT
                cmdLabel.TextSize = 18
                cmdLabel.TextXAlignment = Enum.TextXAlignment.Left
                local descLabel = Instance.new("TextLabel", frame)
                descLabel.Size = UDim2.new(0.55, 0, 1, 0)
                descLabel.Position = UDim2.new(0.42, 0, 0, 0)
                descLabel.BackgroundTransparency = 1
                descLabel.Text = data.desc
                descLabel.TextColor3 = Color3.fromRGB(255,255,255)
                descLabel.Font = CONFIG.FONT
                descLabel.TextSize = 18
                descLabel.TextXAlignment = Enum.TextXAlignment.Left
                btn.MouseButton1Click:Connect(function()
                    TextBox.Text = data.name .. " "
                    TextBox:CaptureFocus()
                end)
            end
        end
    end
    SuggestionFrame.Visible = (count > 0)
    SuggestionFrame.Size = UDim2.new(0, 550, 0, count * 42)
end)

-- ==========================================
-- EXECUÇÃO DE TODOS OS COMANDOS (bloco resumido porém completo)
-- ==========================================
TextBox.FocusLost:Connect(function(enter)
    if enter and TextBox.Text ~= "" then
        local raw = TextBox.Text
        addLog(raw, CONFIG.TEXT_NORMAL)
        local args = string.split(raw:gsub("^%s*(.-)%s*$", "%1"), " ")
        local cmdName = args[1]:lower()
        SuggestionFrame.Visible = false

        task.spawn(function()
            local found = false
            for _, c in pairs(COMMANDS) do if c.name == cmdName then found = true break end end
            if not found and cmdName ~= "close-ui" then
                addLog('<font color="#FF6464">Unknown command: ' .. cmdName .. '</font>')
                TextBox.Text = ""
                return
            end
            runLoadingInInput(0.6)

            local character = l_LocalPlayer.Character
            local humanoid = character and character:FindFirstChildOfClass("Humanoid")

            -- ======================
            -- COMANDOS (todos implementados)
            -- ======================
            if cmdName == "aimbot" then
                aimbotActive = not aimbotActive
                if aimbotActive then enableAimbot(tonumber(args[2]) or 1) addLog('<font color="#00FF00">Aimbot ATIVADO!</font>')
                else disableAimbot() addLog('<font color="#FF6464">Aimbot DESATIVADO!</font>') end

            elseif cmdName == "farm" then
                farmActive = not farmActive
                if farmActive then enableFarm() addLog('<font color="#00FF00">Farm ATIVADO!</font>')
                else disableFarm() addLog('<font color="#FF6464">Farm DESATIVADO!</font>') end

            elseif cmdName == "teleport" then
                local target = args[2] or "spawn"
                local success = target:lower() == "spawn" and teleportToSpawn() or teleportToPlayer(target)
                if success then addLog('<font color="#00FF00">Teleportado para ' .. target .. '!</font>')
                else addLog('<font color="#FF6464">Falha ao teleportar!</font>') end

            elseif cmdName == "speed" then
                local v = tonumber(args[2]) or 16
                if humanoid then humanoid.WalkSpeed = v addLog('<font color="#00FF00">Velocidade: ' .. v .. '</font>') end

            elseif cmdName == "size" then
                local s = tonumber(args[2]) or 1
                if character and humanoid then
                    local ok, desc = pcall(function() return humanoid:GetAppliedDescription() end)
                    if ok and desc then
                        desc.DepthScale=s; desc.HeadScale=s; desc.HeightScale=s; desc.ProportionScale=s; desc.WidthScale=s
                        pcall(function() humanoid:ApplyDescription(desc) end)
                        addLog('<font color="#00FF00">Tamanho: ' .. s .. '</font>')
                    else addLog('<font color="#FF6464">Falha ao alterar tamanho!</font>') end
                end

            elseif cmdName == "godmode" then
                godmodeActive = not godmodeActive
                if godmodeActive then enableGodmode() addLog('<font color="#00FF00">Godmode ATIVADO!</font>')
                else disableGodmode() addLog('<font color="#FF6464">Godmode DESATIVADO!</font>') end

            elseif cmdName == "fly" then
                flyActive = not flyActive
                if flyActive then enableFly() addLog('<font color="#00FF00">Fly ATIVADO!</font>')
                else disableFly() addLog('<font color="#FF6464">Fly DESATIVADO!</font>') end

            elseif cmdName == "esp" then
                espActive = not espActive
                if espActive then enableESP() addLog('<font color="#FF3232">ESP Players ATIVADO!</font>')
                else disableESP() addLog('<font color="#FF6464">ESP Players DESATIVADO!</font>') end

            elseif cmdName == "espnpc" then
                espNpcActive = not espNpcActive
                if espNpcActive then enableNpcESP() addLog('<font color="#FF3232">ESP NPCs ATIVADO!</font>')
                else disableNpcESP() addLog('<font color="#FF6464">ESP NPCs DESATIVADO!</font>') end

            elseif cmdName == "spin" then
                spinmodeActive = not spinmodeActive
                if spinmodeActive then spinSpeed = tonumber(args[2]) or 50; enableSpinmode() addLog('<font color="#00FF00">Spin ATIVADO!</font>')
                else disableSpinmode() addLog('<font color="#FF6464">Spin DESATIVADO!</font>') end

            elseif cmdName == "jumppower" then
                local jp = tonumber(args[2]) or 50
                if humanoid then humanoid.JumpPower = jp addLog('<font color="#00FF00">JumpPower: ' .. jp .. '</font>') end

            elseif cmdName == "gravity" then
                workspace.Gravity = tonumber(args[2]) or 196.2
                addLog('<font color="#00FF00">Gravidade: ' .. workspace.Gravity .. '</font>')

            elseif cmdName == "noclip" then
                noclipActive = not noclipActive
                if noclipActive then
                    noclipConnection = l_RunService.Stepped:Connect(function()
                        if l_LocalPlayer.Character then
                            for _, part in pairs(l_LocalPlayer.Character:GetDescendants()) do
                                if part:IsA("BasePart") then part.CanCollide = false end
                            end
                        end
                    end)
                    addLog('<font color="#00FF00">Noclip ATIVADO!</font>')
                else
                    if noclipConnection then noclipConnection:Disconnect() end
                    addLog('<font color="#FF6464">Noclip DESATIVADO!</font>')
                end

            elseif cmdName == "clip" then
                noclipActive = false
                if noclipConnection then noclipConnection:Disconnect() end
                if l_LocalPlayer.Character then
                    for _, part in pairs(l_LocalPlayer.Character:GetDescendants()) do
                        if part:IsA("BasePart") then part.CanCollide = true end
                    end
                end
                addLog('<font color="#00FF00">Colisões restauradas!</font>')

            elseif cmdName == "infinitejump" then
                infJumpActive = not infJumpActive
                if infJumpActive then
                    infJumpConnection = l_UserInputService.JumpRequest:Connect(function()
                        if humanoid then humanoid:ChangeState("Jumping") end
                    end)
                    addLog('<font color="#00FF00">Pulo Infinito ATIVADO!</font>')
                else
                    if infJumpConnection then infJumpConnection:Disconnect() end
                    addLog('<font color="#FF6464">Pulo Infinito DESATIVADO!</font>')
                end

            elseif cmdName == "invisible" then
                if l_LocalPlayer.Character then
                    for _, part in pairs(l_LocalPlayer.Character:GetDescendants()) do
                        if part:IsA("BasePart") or part:IsA("Decal") then
                            part.Transparency = part.Transparency == 1 and 0 or 1
                        end
                    end
                    addLog('<font color="#00FF00">Invisibilidade alternada!</font>')
                end

            elseif cmdName == "rejoin" then
                game:GetService("TeleportService"):Teleport(game.PlaceId, l_LocalPlayer)

            elseif cmdName == "reset" or cmdName == "kill" then
                if humanoid then humanoid.Health = 0 end

            elseif cmdName == "btools" then
                local bp = l_LocalPlayer:WaitForChild("Backpack")
                Instance.new("HopperBin", bp).BinType = 1
                Instance.new("HopperBin", bp).BinType = 3
                Instance.new("HopperBin", bp).BinType = 4
                addLog('<font color="#00FF00">BTools inseridas!</font>')

            elseif cmdName == "freezecam" then
                l_Camera.CameraType = Enum.CameraType.Scriptable
                addLog('<font color="#FF6464">Câmera travada!</font>')

            elseif cmdName == "unfreezecam" then
                l_Camera.CameraType = Enum.CameraType.Custom
                addLog('<font color="#00FF00">Câmera destravada!</font>')

            elseif cmdName == "clearlogs" then
                for _, child in pairs(LogFrame:GetChildren()) do
                    if child:IsA("TextLabel") then child:Destroy() end
                end
                showInfo()

            elseif cmdName == "heal" then
                if humanoid then humanoid.Health = humanoid.MaxHealth addLog('<font color="#00FF00">Vida restaurada!</font>') end

            elseif cmdName == "anti-afk" then
                if not antiAfkConnection then
                    antiAfkConnection = l_LocalPlayer.Idled:Connect(function()
                        game:GetService("VirtualUser"):Button2Down(Vector2.new(0,0), l_Camera.CFrame)
                        task.wait(1)
                        game:GetService("VirtualUser"):Button2Up(Vector2.new(0,0), l_Camera.CFrame)
                    end)
                    addLog('<font color="#00FF00">Anti-AFK ativado!</font>')
                else
                    antiAfkConnection:Disconnect(); antiAfkConnection = nil
                    addLog('<font color="#FF6464">Anti-AFK desativado!</font>')
                end

            elseif cmdName == "teleporttools" then
                local tool = Instance.new("Tool")
                tool.Name = "Click Teleport"
                tool.RequiresHandle = false
                tool.Activated:Connect(function()
                    local pos = l_LocalPlayer:GetMouse().Hit.Position
                    if l_LocalPlayer.Character then l_LocalPlayer.Character:MoveTo(pos) end
                end)
                tool.Parent = l_LocalPlayer.Backpack
                addLog('<font color="#00FF00">Ferramenta de clique-tp criada!</font>')

            elseif cmdName == "destroyui" then
                ScreenGui:Destroy()

            elseif cmdName == "close-ui" then
                Container.Visible = false

            elseif cmdName == "speedhat" then
                if character and humanoid then
                    local hatCount = 0
                    for _, acc in pairs(character:GetChildren()) do if acc:IsA("Accessory") then hatCount = hatCount + 1 end end
                    humanoid.WalkSpeed = 16 + (hatCount * 10)
                    addLog('<font color="#00FF00">Velocidade: ' .. humanoid.WalkSpeed .. '</font>')
                end

            elseif cmdName == "ff" then
                if character then
                    if not character:FindFirstChildOfClass("ForceField") then Instance.new("ForceField", character) end
                    addLog('<font color="#00FF00">ForceField gerado!</font>')
                end

            elseif cmdName == "unff" then
                if character then
                    local ff = character:FindFirstChildOfClass("ForceField")
                    if ff then ff:Destroy() end
                    addLog('<font color="#FF6464">ForceField removido!</font>')
                end

            elseif cmdName == "sit" then
                if humanoid then humanoid.Sit = not humanoid.Sit end
                addLog('<font color="#00FF00">Personagem ' .. (humanoid and humanoid.Sit and "sentado" or "levantado") .. '.</font>')

            elseif cmdName == "setfps" then
                local fps = tonumber(args[2]) or 60
                if setfpscap then setfpscap(fps) addLog('<font color="#00FF00">FPS limitado: ' .. fps .. '</font>')
                else addLog('<font color="#FF6464">Executor sem suporte.</font>') end

            elseif cmdName == "fhov" then
                l_Camera.FieldOfView = math.clamp(tonumber(args[2]) or 70, 1, 120)
                addLog('<font color="#00FF00">FOV: ' .. l_Camera.FieldOfView .. '</font>')

            elseif cmdName == "light" then
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local hrp = character.HumanoidRootPart
                    if not hrp:FindFirstChild("HubLight") then
                        local light = Instance.new("PointLight", hrp)
                        light.Name = "HubLight"
                        light.Range = 40
                        light.Brightness = 3
                        light.Color = CONFIG.ACCENT
                    end
                    addLog('<font color="#00FF00">Luz acoplada!</font>')
                end

            elseif cmdName == "unlight" then
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local light = character.HumanoidRootPart:FindFirstChild("HubLight")
                    if light then light:Destroy() end
                    addLog('<font color="#FF6464">Luz removida!</font>')
                end

            elseif cmdName == "bighead" then
                if character and character:FindFirstChild("Head") then
                    local head = character.Head
                    local mesh = head:FindFirstChildOfClass("SpecialMesh")
                    if mesh then mesh.Scale = Vector3.new(3,3,3) else head.Size = Vector3.new(4,4,4) end
                    addLog('<font color="#00FF00">Cabeça ampliada!</font>')
                end

            elseif cmdName == "normalhead" then
                if character and character:FindFirstChild("Head") then
                    local head = character.Head
                    local mesh = head:FindFirstChildOfClass("SpecialMesh")
                    if mesh then mesh.Scale = Vector3.new(1,1,1) else head.Size = Vector3.new(2,1,1) end
                    addLog('<font color="#00FF00">Cabeça restaurada.</font>')
                end

            elseif cmdName == "jail" then
                local target = args[2]
                if not target then addLog('<font color="#FF6464">Use: jail <player></font>')
                else
                    local p = getPlayer(target)
                    if createJail(p) then addLog('<font color="#00FF00">Jail criado para ' .. (p and p.Name or target) .. '!</font>')
                    else addLog('<font color="#FF6464">Player não encontrado!</font>') end
                end

            elseif cmdName == "unjail" then
                local target = args[2]
                if not target then addLog('<font color="#FF6464">Use: unjail <player></font>')
                else
                    local p = getPlayer(target)
                    if p and removeJail(p) then addLog('<font color="#FF6464">Jail removido de ' .. p.Name .. '!</font>')
                    else addLog('<font color="#FF6464">Jail não encontrado!</font>') end
                end

            elseif cmdName == "anim" then
                local animTarget = args[2]
                if not animTarget then addLog('<font color="#FF6464">Use: anim <id> ou anim <player> ou anim stop</font>')
                elseif animTarget:lower() == "stop" then
                    stopAllAnims()
                    addLog('<font color="#FF6464">Todas as animações paradas!</font>')
                else
                    local animId = tonumber(animTarget)
                    if animId then
                        if playAnim(animId) then addLog('<font color="#00FF00">Animação ' .. animId .. ' carregada!</font>')
                        else addLog('<font color="#FF6464">ID inválido!</font>') end
                    else
                        local targetChar = nil
                        for _, p in pairs(l_Players:GetPlayers()) do
                            if p.Name:lower():find(animTarget:lower()) and p.Character then targetChar = p.Character; break end
                        end
                        if not targetChar then
                            for _, m in pairs(workspace:GetDescendants()) do
                                if m:IsA("Model") and m.Name:lower():find(animTarget:lower()) and m:FindFirstChildOfClass("Humanoid") and m ~= l_LocalPlayer.Character then
                                    targetChar = m; break
                                end
                            end
                        end
                        if targetChar then
                            local tHum = targetChar:FindFirstChildOfClass("Humanoid")
                            if tHum then
                                local tAnim = tHum:FindFirstChildOfClass("Animator")
                                if tAnim then
                                    local tracks = tAnim:GetPlayingAnimationTracks()
                                    if #tracks > 0 then
                                        stopAllAnims()
                                        local myHum = l_LocalPlayer.Character and l_LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
                                        if myHum then
                                            local myAnim = myHum:FindFirstChildOfClass("Animator")
                                            if not myAnim then myAnim = Instance.new("Animator", myHum) end
                                            local loaded = 0
                                            for _, tr in pairs(tracks) do
                                                if tr.Animation and tr.Animation.AnimationId ~= "" then
                                                    local anim = Instance.new("Animation")
                                                    anim.AnimationId = tr.Animation.AnimationId
                                                    local ok, newTr = pcall(function() return myAnim:LoadAnimation(anim) end)
                                                    if ok and newTr then
                                                        newTr.Priority = tr.Priority
                                                        newTr:Play()
                                                        table.insert(loadedAnimTracks, newTr)
                                                        loaded = loaded + 1
                                                    end
                                                end
                                            end
                                            if loaded > 0 then addLog('<font color="#00FF00">Animação copiada de ' .. targetChar.Name .. '!</font>')
                                            else addLog('<font color="#FF6464">Nenhuma animação válida!</font>') end
                                        else addLog('<font color="#FF6464">Sem Humanoid!</font>') end
                                    else addLog('<font color="#FF6464">Alvo não está animado!</font>') end
                                else addLog('<font color="#FF6464">Alvo sem Animator!</font>') end
                            else addLog('<font color="#FF6464">Alvo sem Humanoid!</font>') end
                        else addLog('<font color="#FF6464">Player/modelo não encontrado: ' .. animTarget .. '</font>') end
                    end
                end

            elseif cmdName == "stopanim" then
                stopAllAnims()
                addLog('<font color="#FF6464">Todas as animações paradas!</font>')

            elseif cmdName == "time" then
                local t = math.clamp(tonumber(args[2]) or 12, 0, 24)
                game:GetService("Lighting").TimeOfDay = string.format("%02d:00:00", t)
                addLog('<font color="#00FF00">Hora: ' .. t .. 'h</font>')

            elseif cmdName == "brightness" then
                local b = tonumber(args[2]) or 2
                game:GetService("Lighting").Brightness = b
                addLog('<font color="#00FF00">Brilho: ' .. b .. '</font>')

            elseif cmdName == "fog" then
                local d = tonumber(args[2]) or 100
                game:GetService("Lighting").FogEnd = d
                game:GetService("Lighting").FogStart = 0
                addLog('<font color="#00FF00">Fog: ' .. d .. ' studs</font>')

            elseif cmdName == "unfog" then
                game:GetService("Lighting").FogEnd = 100000
                game:GetService("Lighting").FogStart = 0
                addLog('<font color="#00FF00">Fog removido!</font>')

            elseif cmdName == "name" then
                local newName = args[2] or l_LocalPlayer.Name
                if character and character:FindFirstChild("Head") then
                    local old = playerGui:FindFirstChild("ConchOverheadName")
                    if old then old:Destroy() end
                    local bb = Instance.new("BillboardGui", playerGui)
                    bb.Name = "ConchOverheadName"
                    bb.Adornee = character.Head
                    bb.Size = UDim2.new(0,200,0,50)
                    bb.StudsOffset = Vector3.new(0,2,0)
                    bb.AlwaysOnTop = false
                    local lbl = Instance.new("TextLabel", bb)
                    lbl.Size = UDim2.new(1,0,1,0)
                    lbl.BackgroundTransparency = 1
                    lbl.Text = newName
                    lbl.TextColor3 = Color3.fromRGB(255,255,255)
                    lbl.TextStrokeTransparency = 0
                    lbl.Font = CONFIG.FONT
                    lbl.TextSize = 18
                    addLog('<font color="#00FF00">Nome exibido: ' .. newName .. '</font>')
                end

            elseif cmdName == "chat" then
                local msg = table.concat(args, " ", 2)
                if msg ~= "" then
                    local rs = game:GetService("ReplicatedStorage")
                    local chatEvents = rs:FindFirstChild("DefaultChatSystemChatEvents")
                    if chatEvents then
                        local sayMsg = chatEvents:FindFirstChild("SayMessageRequest")
                        if sayMsg then pcall(function() sayMsg:FireServer(msg, "All") end) end
                    end
                    addLog('<font color="#00FF00">Mensagem enviada!</font>')
                end

            elseif cmdName == "zoom" then
                local z = tonumber(args[2]) or 400
                l_LocalPlayer.CameraMaxZoomDistance = z
                addLog('<font color="#00FF00">Zoom máximo: ' .. z .. '</font>')

            elseif cmdName == "freeze" then
                if character and character:FindFirstChild("HumanoidRootPart") then
                    character.HumanoidRootPart.Anchored = true
                    if humanoid then humanoid.WalkSpeed = 0; humanoid.JumpPower = 0 end
                    addLog('<font color="#FF6464">Personagem congelado!</font>')
                end

            elseif cmdName == "unfreeze" then
                if character and character:FindFirstChild("HumanoidRootPart") then
                    character.HumanoidRootPart.Anchored = false
                    if humanoid then humanoid.WalkSpeed = 16; humanoid.JumpPower = 50 end
                    addLog('<font color="#00FF00">Personagem descongelado!</font>')
                end

            elseif cmdName == "platform" then
                if character and character:FindFirstChild("HumanoidRootPart") then
                    if platformPart then platformPart:Destroy() end
                    platformPart = Instance.new("Part", workspace)
                    platformPart.Name = "ConchPlatform"
                    platformPart.Anchored = true
                    platformPart.Size = Vector3.new(10,1,10)
                    platformPart.CFrame = character.HumanoidRootPart.CFrame - Vector3.new(0,3,0)
                    platformPart.BrickColor = BrickColor.new("Bright blue")
                    platformPart.Material = Enum.Material.SmoothPlastic
                    addLog('<font color="#00FF00">Plataforma criada!</font>')
                end

            elseif cmdName == "unplatform" then
                if platformPart then platformPart:Destroy(); platformPart = nil end
                addLog('<font color="#FF6464">Plataforma removida!</font>')

            elseif cmdName == "attachcam" then
                local target = args[2]
                if not target then addLog('<font color="#FF6464">Use: attachcam <player></font>')
                else
                    local p = getPlayer(target)
                    if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                        l_Camera.CameraType = Enum.CameraType.Scriptable
                        if attachCamConnection then attachCamConnection:Disconnect() end
                        attachCamConnection = l_RunService.RenderStepped:Connect(function()
                            if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                                l_Camera.CFrame = p.Character.HumanoidRootPart.CFrame * CFrame.new(0,5,12)
                            end
                        end)
                        addLog('<font color="#00FF00">Câmera anexada a ' .. p.Name .. '!</font>')
                    else addLog('<font color="#FF6464">Player não encontrado!</font>') end
                end

            elseif cmdName == "detachcam" then
                if attachCamConnection then attachCamConnection:Disconnect(); attachCamConnection = nil end
                l_Camera.CameraType = Enum.CameraType.Custom
                addLog('<font color="#00FF00">Câmera retornada ao normal!</font>')

            elseif cmdName == "rainbow" then
                if rainbowConnection then
                    rainbowConnection:Disconnect()
                    rainbowConnection = nil
                    addLog('<font color="#FF6464">Rainbow DESATIVADO!</font>')
                else
                    rainbowConnection = l_RunService.Heartbeat:Connect(function()
                        local char = l_LocalPlayer.Character
                        if not char then return end
                        local hue = tick() % 5 / 5
                        for _, part in pairs(char:GetDescendants()) do
                            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                                part.Color = Color3.fromHSV(hue, 1, 1)
                            end
                        end
                    end)
                    addLog('<font color="#00FF00">Rainbow ATIVADO!</font>')
                end

            elseif cmdName == "unrainbow" then
                if rainbowConnection then rainbowConnection:Disconnect(); rainbowConnection = nil end
                addLog('<font color="#FF6464">Rainbow DESATIVADO!</font>')

            elseif cmdName == "nametag" then
                local tagText = table.concat(args, " ", 2)
                if tagText == "" then tagText = "★" end
                if character and character:FindFirstChild("Head") then
                    local old = playerGui:FindFirstChild("ConchNameTag")
                    if old then old:Destroy() end
                    local bb = Instance.new("BillboardGui", playerGui)
                    bb.Name = "ConchNameTag"
                    bb.Adornee = character.Head
                    bb.Size = UDim2.new(0,250,0,40)
                    bb.StudsOffset = Vector3.new(0,3.5,0)
                    bb.AlwaysOnTop = true
                    local lbl = Instance.new("TextLabel", bb)
                    lbl.Size = UDim2.new(1,0,1,0)
                    lbl.BackgroundTransparency = 1
                    lbl.Text = tagText
                    lbl.TextColor3 = CONFIG.ACCENT
                    lbl.TextStrokeTransparency = 0
                    lbl.Font = CONFIG.FONT
                    lbl.TextSize = 20
                    addLog('<font color="#00FF00">Nametag: ' .. tagText .. '</font>')
                end

            elseif cmdName == "removetag" then
                local tag = playerGui:FindFirstChild("ConchNameTag")
                if tag then tag:Destroy() end
                addLog('<font color="#FF6464">Nametag removida!</font>')

            elseif cmdName == "annoy" then
                local target = args[2]
                if not target then addLog('<font color="#FF6464">Use: annoy <player></font>')
                else
                    local p = getPlayer(target)
                    if p then
                        if annoyConnection then annoyConnection:Disconnect() end
                        annoyConnection = l_RunService.Heartbeat:Connect(function()
                            local myChar = l_LocalPlayer.Character
                            if p.Character and p.Character:FindFirstChild("HumanoidRootPart") and myChar and myChar:FindFirstChild("HumanoidRootPart") then
                                myChar.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame
                            end
                        end)
                        addLog('<font color="#00FF00">Seguindo ' .. p.Name .. '!</font>')
                    else addLog('<font color="#FF6464">Player não encontrado!</font>') end
                end

            elseif cmdName == "unannoy" then
                if annoyConnection then annoyConnection:Disconnect(); annoyConnection = nil end
                addLog('<font color="#FF6464">Parou de seguir!</font>')

            elseif cmdName == "reach" then
                local r = tonumber(args[2]) or 10
                local tool = character and character:FindFirstChildOfClass("Tool")
                if tool and tool:FindFirstChild("Handle") then
                    tool.Handle.Size = Vector3.new(r, 0.1, 0.1)
                    addLog('<font color="#00FF00">Reach: ' .. r .. '</font>')
                else addLog('<font color="#FF6464">Segure uma ferramenta primeiro!</font>') end

            elseif cmdName == "morph" then
                local morphTarget = args[2]
                if not morphTarget then addLog('<font color="#FF6464">Use: morph <id> ou morph <player></font>')
                else
                    local p = getPlayer(morphTarget)
                    if p then
                        if applyMorph(nil, p) then addLog('<font color="#00FF00">Morph aplicado de ' .. p.Name .. ' com acessórios!</font>')
                        else addLog('<font color="#FF6464">Falha ao copiar o visual!</font>') end
                    else
                        local aid = tonumber(morphTarget)
                        if aid then
                            if applyMorph(aid) then addLog('<font color="#00FF00">Morph aplicado com ID: ' .. aid .. '!</font>')
                            else addLog('<font color="#FF6464">Falha ao aplicar morph!</font>') end
                        else addLog('<font color="#FF6464">Player ou ID inválido!</font>') end
                    end
                end

            else
                addLog('<font color="#FF6464">Comando não implementado (isso não deveria acontecer).</font>')
            end
        end)
        TextBox.Text = ""
    end
end)

local function toggleUI()
    Container.Visible = not Container.Visible
    if Container.Visible then TextBox:CaptureFocus() end
end

ToggleButton.MouseButton1Click:Connect(toggleUI)
l_UserInputService.InputBegan:Connect(function(input, gpe)
    if not gpe and input.KeyCode == Enum.KeyCode.X then toggleUI() end
end)

print("✓ Delta Conch Hub carregado com todos os comandos e UI 100% original.")
