local P = game:GetService("Players")
local W = game:GetService("Workspace")
local RS = game:GetService("ReplicatedStorage")
local T = game:GetService("TweenService")
local CG = game:GetService("CoreGui")
local VU = game:GetService("VirtualUser")
local HS = game:GetService("HttpService")
local LP = P.LocalPlayer

local CFG = {AutoSteal=false,AutoPlace=false,AutoHatch=false,AutoClaim=false,Speed=5,Radius=800,AntiAdmin=true,Paused=false,Stealth=true}

local Running = true
local AdminPresent = false
local Pets = {}
local Remotes = {}

LP.Idled:Connect(function() VU:CaptureController() VU:ClickButton2(Vector2.new()) end)

local function ScanRemotes()
    Remotes = {}
    local function scan(c)
        for _, o in ipairs(c:GetDescendants()) do
            if o:IsA("RemoteEvent") or o:IsA("RemoteFunction") then
                local n = o.Name:lower()
                if not Remotes.Steal and (n:find("steal") or n:find("grab") or n:find("pick")) then Remotes.Steal = o end
                if not Remotes.Place and (n:find("place") or n:find("drop")) then Remotes.Place = o end
                if not Remotes.Hatch and (n:find("hatch") or n:find("open")) then Remotes.Hatch = o end
                if not Remotes.Claim and (n:find("claim") or n:find("collect")) then Remotes.Claim = o end
            end
        end
    end
    pcall(function() scan(RS) end)
    pcall(function() scan(W) end)
end

local function GetOwnerPlot(obj)
    local parent = obj
    for _ = 1, 6 do
        if not parent or not parent.Parent then break end
        parent = parent.Parent
        local n = parent.Name:lower()
        if n:find("base") or n:find("plot") or n:find("cage") then return parent end
    end
    return nil
end

local function GetPlotOwner(plot)
    if not plot then return nil end
    for _, o in ipairs(plot:GetDescendants()) do
        if o:IsA("ObjectValue") or o:IsA("StringValue") then
            local n = o.Name:lower()
            if n:find("owner") or n:find("player") then
                if typeof(o.Value) == "Instance" and o.Value:IsA("Player") then return o.Value end
                if typeof(o.Value) == "string" then return P:FindFirstChild(o.Value) end
            end
        end
    end
    for _, p in ipairs(P:GetPlayers()) do
        if plot.Name:find(p.Name) and p ~= LP then return p end
    end
    return nil
end

local function ScanPets()
    Pets = {}
    for _, o in ipairs(W:GetDescendants()) do
        if o:IsA("Model") or o:IsA("BasePart") then
            local n = o.Name:lower()
            if n:find("egg") or n:find("spawn") or n:find("portal") then continue end
            if n:find("pet") or n:find("brainrot") or n:find("animal") or n:find("creature") then
                local plot = GetOwnerPlot(o)
                if plot then
                    local owner = GetPlotOwner(plot)
                    if owner and owner ~= LP then
                        table.insert(Pets, {pet=o, plot=plot, owner=owner, name=o.Name})
                    end
                end
            end
        end
    end
end

local function IsAdmin(p)
    if not p or p == LP then return false end
    if p.UserId <= 1 then return true end
    local name = (p.Name .. p.DisplayName):lower()
    for _, k in ipairs({"admin","mod","owner","staff","dev"}) do
        if name:find(k) then return true end
    end
    local ok, rank = pcall(function() return p:GetRankInGroup(game.CreatorId) end)
    if ok and rank and rank >= 200 then return true end
    return false
end

local function ScanAdmins()
    if not CFG.AntiAdmin then return end
    for _, p in ipairs(P:GetPlayers()) do
        if IsAdmin(p) then AdminPresent = true return end
    end
    AdminPresent = false
end

local function Fire(r, ...)
    if not r or CFG.Paused then return end
    pcall(function()
        if r:IsA("RemoteEvent") then r:FireServer(...)
        elseif r:IsA("RemoteFunction") then r:InvokeServer(...) end
    end)
end

local function GetPos(o)
    if not o or not o.Parent then return nil end
    if o:IsA("Model") then
        local ok, pv = pcall(function() return o:GetPivot() end)
        if ok and pv then return pv.Position end
        local pp = o.PrimaryPart or o:FindFirstChildWhichIsA("BasePart")
        if pp then return pp.Position end
    elseif o:IsA("BasePart") then return o.Position end
    return nil
end

local Gui = Instance.new("ScreenGui")
Gui.Name = HS:GenerateGUID(false)
Gui.ResetOnSpawn = false
Gui.Parent = CG

local F = Instance.new("Frame", Gui)
F.Size = UDim2.new(0, 240, 0, 260)
F.Position = UDim2.new(0, 20, 0.5, -130)
F.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
F.BorderSizePixel = 0
F.Active = true
F.Draggable = true
Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
local S = Instance.new("UIStroke", F)
S.Color = Color3.fromRGB(60, 180, 100)
S.Thickness = 2

local H = Instance.new("Frame", F)
H.Size = UDim2.new(1, 0, 0, 35)
H.BackgroundColor3 = Color3.fromRGB(45, 130, 75)
H.BorderSizePixel = 0
Instance.new("UICorner", H).CornerRadius = UDim.new(0, 10)
local HF = Instance.new("Frame", H)
HF.Size = UDim2.new(1, 0, 0, 15)
HF.Position = UDim2.new(0, 0, 1, -15)
HF.BackgroundColor3 = Color3.fromRGB(45, 130, 75)
HF.BorderSizePixel = 0

local T2 = Instance.new("TextLabel", H)
T2.Size = UDim2.new(1, -60, 1, 0)
T2.Position = UDim2.new(0, 10, 0, 0)
T2.BackgroundTransparency = 1
T2.Text = "🍚 Nasi Rendanh"
T2.TextColor3 = Color3.fromRGB(255, 255, 255)
T2.TextSize = 14
T2.Font = Enum.Font.GothamBold
T2.TextXAlignment = Enum.TextXAlignment.Left

local Dot = Instance.new("Frame", H)
Dot.Size = UDim2.new(0, 10, 0, 10)
Dot.Position = UDim2.new(1, -45, 0.5, -5)
Dot.BackgroundColor3 = Color3.fromRGB(80, 200, 120)
Dot.BorderSizePixel = 0
Instance.new("UICorner", Dot).CornerRadius = UDim.new(1, 0)

local C = Instance.new("TextButton", H)
C.Size = UDim2.new(0, 25, 0, 25)
C.Position = UDim2.new(1, -32, 0.5, -12)
C.BackgroundColor3 = Color3.fromRGB(220, 70, 70)
C.Text = "×"
C.TextColor3 = Color3.fromRGB(255, 255, 255)
C.TextSize = 18
C.Font = Enum.Font.GothamBold
C.BorderSizePixel = 0
Instance.new("UICorner", C).CornerRadius = UDim.new(0, 6)
C.MouseButton1Click:Connect(function() Gui.Enabled = false end)

local Ct = Instance.new("Frame", F)
Ct.Size = UDim2.new(1, -20, 1, -50)
Ct.Position = UDim2.new(0, 10, 0, 45)
Ct.BackgroundTransparency = 1
local UL = Instance.new("UIListLayout", Ct)
UL.Padding = UDim.new(0, 6)
UL.SortOrder = Enum.SortOrder.LayoutOrder

local function Toggle(txt, ord, def, cb)
    local B = Instance.new("TextButton", Ct)
    B.Size = UDim2.new(1, 0, 0, 34)
    B.BackgroundColor3 = def and Color3.fromRGB(60, 150, 90) or Color3.fromRGB(40, 40, 50)
    B.Text = txt .. (def and " • ON" or " • OFF")
    B.TextColor3 = Color3.fromRGB(240, 240, 240)
    B.TextSize = 13
    B.Font = Enum.Font.GothamSemibold
    B.BorderSizePixel = 0
    B.LayoutOrder = ord
    Instance.new("UICorner", B).CornerRadius = UDim.new(0, 6)
    local s = def
    B.MouseButton1Click:Connect(function()
        s = not s
        B.Text = txt .. (s and " • ON" or " • OFF")
        B.BackgroundColor3 = s and Color3.fromRGB(60, 150, 90) or Color3.fromRGB(40, 40, 50)
        cb(s)
    end)
end

local function Slider(txt, ord, mn, mx, def, cb)
    local B = Instance.new("Frame", Ct)
    B.Size = UDim2.new(1, 0, 0, 48)
    B.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    B.BorderSizePixel = 0
    B.LayoutOrder = ord
    Instance.new("UICorner", B).CornerRadius = UDim.new(0, 6)
    local L = Instance.new("TextLabel", B)
    L.Size = UDim2.new(1, -20, 0, 18)
    L.Position = UDim2.new(0, 10, 0, 4)
    L.BackgroundTransparency = 1
    L.Text = txt .. ": " .. def
    L.TextColor3 = Color3.fromRGB(240, 240, 240)
    L.TextSize = 12
    L.Font = Enum.Font.GothamSemibold
    L.TextXAlignment = Enum.TextXAlignment.Left
    local Bar = Instance.new("Frame", B)
    Bar.Size = UDim2.new(1, -20, 0, 6)
    Bar.Position = UDim2.new(0, 10, 0, 30)
    Bar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    Bar.BorderSizePixel = 0
    Instance.new("UICorner", Bar).CornerRadius = UDim.new(0, 3)
    local Fl = Instance.new("Frame", Bar)
    Fl.Size = UDim2.new((def - mn) / (mx - mn), 0, 1, 0)
    Fl.BackgroundColor3 = Color3.fromRGB(60, 180, 100)
    Fl.BorderSizePixel = 0
    Instance.new("UICorner", Fl).CornerRadius = UDim.new(0, 3)
    local dg = false
    local function up(i)
        local r = math.clamp((i.Position.X - Bar.AbsolutePosition.X) / Bar.AbsoluteSize.X, 0, 1)
        Fl.Size = UDim2.new(r, 0, 1, 0)
        local v = math.floor(mn + (mx - mn) * r)
        L.Text = txt .. ": " .. v
        cb(v)
    end
    Bar.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dg = true up(i) end end)
    Bar.InputChanged:Connect(function(i) if dg and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then up(i) end end)
    Bar.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dg = false end end)
end

Toggle("Auto Steal Pet", 1, false, function(v) CFG.AutoSteal = v end)
Toggle("Auto Place Egg", 2, false, function(v) CFG.AutoPlace = v end)
Toggle("Auto Hatch", 3, false, function(v) CFG.AutoHatch = v end)
Toggle("Auto Claim", 4, false, function(v) CFG.AutoClaim = v end)
Toggle("Anti Admin", 5, true, function(v) CFG.AntiAdmin = v end)
Slider("Speed", 6, 1, 10, 5, function(v) CFG.Speed = v end)
Slider("Radius", 7, 100, 3000, 800, function(v) CFG.Radius = v end)

task.spawn(function() task.wait(0.5) ScanRemotes() ScanPets() end)

task.spawn(function()
    while Running do
        task.wait(1 / math.max(CFG.Speed, 1))
        if CFG.AntiAdmin then
            ScanAdmins()
            Dot.BackgroundColor3 = AdminPresent and Color3.fromRGB(255, 80, 80) or Color3.fromRGB(80, 200, 120)
            CFG.Paused = AdminPresent
        end
        if CFG.AutoSteal and not CFG.Paused then
            local ch = LP.Character
            local hr = ch and ch:FindFirstChild("HumanoidRootPart")
            if hr then
                local best, bd, bp = nil, CFG.Radius, nil
                for _, d in ipairs(Pets) do
                    local pos = GetPos(d.pet)
                    if pos then
                        local dist = (pos - hr.Position).Magnitude
                        if dist < bd then bd = dist best = d bp = pos end
                    end
                end
                if best and bp then
                    pcall(function()
                        local tw = T:Create(hr, TweenInfo.new(0.15, Enum.EasingStyle.Linear), {CFrame = CFrame.new(bp + Vector3.new(0, 2, 0))})
                        tw:Play() tw.Completed:Wait()
                        task.wait(0.08)
                        if Remotes.Steal then
                            Fire(Remotes.Steal, best.pet)
                            task.wait(0.05)
                            Fire(Remotes.Steal, best.pet.Name)
                            task.wait(0.05)
                            if best.owner then
                                Fire(Remotes.Steal, best.pet, best.owner)
                                task.wait(0.05)
                                Fire(Remotes.Steal, best.owner.Name)
                            end
                        end
                        local tool = ch:FindFirstChildOfClass("Tool")
                        if tool then pcall(function() tool:Activate() end) end
                    end)
                end
            end
        end
        if CFG.AutoPlace and Remotes.Place then Fire(Remotes.Place) task.wait(0.1) end
        if CFG.AutoHatch and Remotes.Hatch then Fire(Remotes.Hatch) task.wait(0.1) end
        if CFG.AutoClaim and Remotes.Claim then Fire(Remotes.Claim) task.wait(0.1) end
    end
end)

task.spawn(function()
    while Running do
        task.wait(10)
        ScanPets()
        if not Remotes.Steal then ScanRemotes() end
    end
end)