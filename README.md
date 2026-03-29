local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- 상태값
local flying, noclip, speedOn, infJump = false, false, false, false
local godMode, fullbright, antiAfk = false, false, false
local espOn, superJump, lowGravity = false, false, false
local invisible, clickTP, infStamina = false, false, false
local giantOn, miniOn, rainbow = false, false, false
local autoSpin, superDash, aimLock = false, false, false
local slowmo = false
local flySpeed = 100
local bodyVelocity, bodyGyro = nil, nil
local espHighlights = {}
local aimTarget = nil
local spinAngle = 0

-- =====================
-- GUI 생성
-- =====================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodCheatV3"
screenGui.ResetOnSpawn = false
screenGui.Parent = player.PlayerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 1000)
frame.Position = UDim2.new(0, 20, 0.5, -500)
frame.BackgroundColor3 = Color3.fromRGB(8, 8, 8)
frame.BackgroundTransparency = 0.05
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 16)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 42)
title.BackgroundColor3 = Color3.fromRGB(0, 60, 255)
title.BorderSizePixel = 0
title.Text = "💀 GOD CHEAT v3.0"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = frame
Instance.new("UICorner", title).CornerRadius = UDim.new(0, 16)

local function makeButton(text, posY, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.88, 0, 0, 36)
    btn.Position = UDim2.new(0.06, 0, 0, posY)
    btn.BackgroundColor3 = color
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.BorderSizePixel = 0
    btn.Parent = frame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    return btn
end

local Y = 50
local function nb(text, color) local b = makeButton(text, Y, color) Y = Y + 44 return b end

local flyBtn       = nb("✈️ FLY [E] : OFF",            Color3.fromRGB(30, 100, 220))
local noclipBtn    = nb("👻 NOCLIP [R] : OFF",          Color3.fromRGB(120, 30, 200))
local speedBtn     = nb("⚡ SPEED [F] : OFF",           Color3.fromRGB(200, 120, 0))
local jumpBtn      = nb("🚀 INF JUMP [G] : OFF",        Color3.fromRGB(20, 160, 80))
local godBtn       = nb("🛡️ GOD MODE [H] : OFF",        Color3.fromRGB(200, 30, 30))
local brightBtn    = nb("🌟 FULLBRIGHT [J] : OFF",      Color3.fromRGB(170, 140, 0))
local afkBtn       = nb("💤 ANTI-AFK [K] : OFF",        Color3.fromRGB(0, 140, 140))
local espBtn       = nb("👁️ ESP [L] : OFF",              Color3.fromRGB(0, 160, 100))
local superJumpBtn = nb("🌙 SUPER JUMP [Z] : OFF",      Color3.fromRGB(80, 0, 200))
local gravBtn      = nb("🪐 LOW GRAVITY [X] : OFF",     Color3.fromRGB(30, 80, 160))
local giantBtn     = nb("🔎 GIANT [C] : OFF",           Color3.fromRGB(140, 60, 0))
local miniBtn      = nb("🐜 MINI [B] : OFF",            Color3.fromRGB(0, 100, 80))
local invisBtn     = nb("🎭 INVISIBLE [V] : OFF",       Color3.fromRGB(50, 50, 80))
local clickTPBtn   = nb("⚡ CLICK TP [Q] : OFF",        Color3.fromRGB(180, 0, 180))
local slowmoBtn    = nb("🌀 SLOWMO [M] : OFF",          Color3.fromRGB(0, 120, 180))
local staminaBtn   = nb("🔥 INF STAMINA [N] : OFF",     Color3.fromRGB(200, 50, 0))
local rainbowBtn   = nb("🌈 RAINBOW [1] : OFF",         Color3.fromRGB(255, 0, 100))
local spinBtn      = nb("🔄 AUTO SPIN [2] : OFF",       Color3.fromRGB(100, 0, 160))
local dashBtn      = nb("🌊 SUPER DASH [3] : OFF",      Color3.fromRGB(0, 160, 200))
local aimBtn       = nb("🎯 AIM LOCK [4] : OFF",        Color3.fromRGB(160, 0, 0))
local tpPlayerBtn  = nb("📍 TP NEAREST [Y]",            Color3.fromRGB(120, 0, 100))
local resetBtn     = nb("💀 RESET",                     Color3.fromRGB(100, 0, 0))

-- =====================
-- ✈️ FLY
-- =====================
local function startFlying()
    flying = true
    flyBtn.Text = "✈️ FLY [E] : ON"
    flyBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
    humanoid.PlatformStand = true
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.zero
    bodyVelocity.MaxForce = Vector3.new(1e5, 1e5, 1e5)
    bodyVelocity.Parent = hrp
    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
    bodyGyro.P = 1e4
    bodyGyro.Parent = hrp
end

local function stopFlying()
    flying = false
    flyBtn.Text = "✈️ FLY [E] : OFF"
    flyBtn.BackgroundColor3 = Color3.fromRGB(30, 100, 220)
    humanoid.PlatformStand = false
    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end

flyBtn.MouseButton1Click:Connect(function()
    if flying then stopFlying() else startFlying() end
end)

-- =====================
-- 👻 NOCLIP
-- =====================
local function setNoclip(state)
    noclip = state
    noclipBtn.Text = "👻 NOCLIP [R] : " .. (noclip and "ON" or "OFF")
    noclipBtn.BackgroundColor3 = noclip and Color3.fromRGB(200, 0, 255) or Color3.fromRGB(120, 30, 200)
end
noclipBtn.MouseButton1Click:Connect(function() setNoclip(not noclip) end)

RunService.Stepped:Connect(function()
    if noclip and character then
        for _, p in ipairs(character:GetDescendants()) do
            if p:IsA("BasePart") then p.CanCollide = false end
        end
    end
end)

-- =====================
-- ⚡ SPEED
-- =====================
local function setSpeed(state)
    speedOn = state
    speedBtn.Text = "⚡ SPEED [F] : " .. (speedOn and "ON" or "OFF")
    speedBtn.BackgroundColor3 = speedOn and Color3.fromRGB(255, 180, 0) or Color3.fromRGB(200, 120, 0)
    humanoid.WalkSpeed = speedOn and 150 or 16
end
speedBtn.MouseButton1Click:Connect(function() setSpeed(not speedOn) end)

-- =====================
-- 🚀 INF JUMP
-- =====================
local function setInfJump(state)
    infJump = state
    jumpBtn.Text = "🚀 INF JUMP [G] : " .. (infJump and "ON" or "OFF")
    jumpBtn.BackgroundColor3 = infJump and Color3.fromRGB(0, 230, 100) or Color3.fromRGB(20, 160, 80)
end
jumpBtn.MouseButton1Click:Connect(function() setInfJump(not infJump) end)

UserInputService.JumpRequest:Connect(function()
    if infJump and humanoid then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- =====================
-- 🛡️ GOD MODE
-- =====================
local godConn = nil
local function setGodMode(state)
    godMode = state
    godBtn.Text = "🛡️ GOD MODE [H] : " .. (godMode and "ON" or "OFF")
    godBtn.BackgroundColor3 = godMode and Color3.fromRGB(255, 60, 60) or Color3.fromRGB(200, 30, 30)
    if godMode then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
        godConn = RunService.Heartbeat:Connect(function()
            if humanoid then humanoid.Health = humanoid.MaxHealth end
        end)
    else
        humanoid.MaxHealth = 100
        humanoid.Health = 100
        if godConn then godConn:Disconnect() godConn = nil end
    end
end
godBtn.MouseButton1Click:Connect(function() setGodMode(not godMode) end)

-- =====================
-- 🌟 FULLBRIGHT
-- =====================
local origAmbient = Lighting.Ambient
local origBright = Lighting.Brightness

local function setFullbright(state)
    fullbright = state
    brightBtn.Text = "🌟 FULLBRIGHT [J] : " .. (fullbright and "ON" or "OFF")
    brightBtn.BackgroundColor3 = fullbright and Color3.fromRGB(255, 220, 0) or Color3.fromRGB(170, 140, 0)
    if fullbright then
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
        Lighting.Brightness = 2
        Lighting.GlobalShadows = false
        Lighting.FogEnd = 100000
    else
        Lighting.Ambient = origAmbient
        Lighting.Brightness = origBright
        Lighting.GlobalShadows = true
    end
end
brightBtn.MouseButton1Click:Connect(function() setFullbright(not fullbright) end)

-- =====================
-- 💤 ANTI-AFK
-- =====================
local afkConn = nil
local function setAntiAfk(state)
    antiAfk = state
    afkBtn.Text = "💤 ANTI-AFK [K] : " .. (antiAfk and "ON" or "OFF")
    afkBtn.BackgroundColor3 = antiAfk and Color3.fromRGB(0, 220, 220) or Color3.fromRGB(0, 140, 140)
    if antiAfk then
        afkConn = RunService.Heartbeat:Connect(function()
            pcall(function()
                game:GetService("VirtualInputManager"):SendKeyEvent(true, "W", false, game)
                game:GetService("VirtualInputManager"):SendKeyEvent(false, "W", false, game)
            end)
        end)
    else
        if afkConn then afkConn:Disconnect() afkConn = nil end
    end
end
afkBtn.MouseButton1Click:Connect(function() setAntiAfk(not antiAfk) end)

-- =====================
-- 👁️ ESP
-- =====================
local function setESP(state)
    espOn = state
    espBtn.Text = "👁️ ESP [L] : " .. (espOn and "ON" or "OFF")
    espBtn.BackgroundColor3 = espOn and Color3.fromRGB(0, 255, 140) or Color3.fromRGB(0, 160, 100)
    if not espOn then
        for _, h in pairs(espHighlights) do if h and h.Parent then h:Destroy() end end
        espHighlights = {}
    end
end
espBtn.MouseButton1Click:Connect(function() setESP(not espOn) end)

RunService.Heartbeat:Connect(function()
    if espOn then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= player and p.Character then
                if not p.Character:FindFirstChildOfClass("Highlight") then
                    local h = Instance.new("Highlight")
                    h.FillColor = Color3.fromRGB(255, 0, 0)
                    h.OutlineColor = Color3.fromRGB(255, 255, 255)
                    h.FillTransparency = 0.5
                    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    h.Parent = p.Character
                    table.insert(espHighlights, h)
                end
            end
        end
    end
end)

-- =====================
-- 🌙 SUPER JUMP
-- =====================
local function setSuperJump(state)
    superJump = state
    superJumpBtn.Text = "🌙 SUPER JUMP [Z] : " .. (superJump and "ON" or "OFF")
    superJumpBtn.BackgroundColor3 = superJump and Color3.fromRGB(160, 0, 255) or Color3.fromRGB(80, 0, 200)
    humanoid.JumpPower = superJump and 250 or 50
end
superJumpBtn.MouseButton1Click:Connect(function() setSuperJump(not superJump) end)

-- =====================
-- 🪐 LOW GRAVITY
-- =====================
local function setLowGravity(state)
    lowGravity = state
    gravBtn.Text = "🪐 LOW GRAVITY [X] : " .. (lowGravity and "ON" or "OFF")
    gravBtn.BackgroundColor3 = lowGravity and Color3.fromRGB(60, 160, 255) or Color3.fromRGB(30, 80, 160)
    workspace.Gravity = lowGravity and 10 or 196
end
gravBtn.MouseButton1Click:Connect(function() setLowGravity(not lowGravity) end)

-- =====================
-- 🔎 GIANT MODE
-- =====================
local function setGiant(state)
    giantOn = state
    miniOn = false
    miniBtn.Text = "🐜 MINI [B] : OFF"
    miniBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 80)
    giantBtn.Text = "🔎 GIANT [C] : " .. (giantOn and "ON" or "OFF")
    giantBtn.BackgroundColor3 = giantOn and Color3.fromRGB(255, 120, 0) or Color3.fromRGB(140, 60, 0)
    local scale = giantOn and 5 or 1
    if character and character:FindFirstChild("Humanoid") then
        local desc = humanoid:GetAppliedDescription()
        desc.HeadScale = scale
        desc.BodyHeightScale = scale
        desc.BodyWidthScale = scale
        desc.BodyDepthScale = scale
        humanoid:ApplyDescription(desc)
    end
end
giantBtn.MouseButton1Click:Connect(function() setGiant(not giantOn) end)

-- =====================
-- 🐜 MINI MODE
-- =====================
local function setMini(state)
    miniOn = state
    giantOn = false
    giantBtn.Text = "🔎 GIANT [C] : OFF"
    giantBtn.BackgroundColor3 = Color3.fromRGB(140, 60, 0)
    miniBtn.Text = "🐜 MINI [B] : " .. (miniOn and "ON" or "OFF")
    miniBtn.BackgroundColor3 = miniOn and Color3.fromRGB(0, 200, 140) or Color3.fromRGB(0, 100, 80)
    local scale = miniOn and 0.2 or 1
    if character and humanoid then
        local desc = humanoid:GetAppliedDescription()
        desc.HeadScale = scale
        desc.BodyHeightScale = scale
        desc.BodyWidthScale = scale
        desc.BodyDepthScale = scale
        humanoid:ApplyDescription(desc)
    end
end
miniBtn.MouseButton1Click:Connect(function() setMini(not miniOn) end)

-- =====================
-- 🎭 INVISIBLE
-- =====================
local function setInvisible(state)
    invisible = state
    invisBtn.Text = "🎭 INVISIBLE [V] : " .. (invisible and "ON" or "OFF")
    invisBtn.BackgroundColor3 = invisible and Color3.fromRGB(180, 180, 180) or Color3.fromRGB(50, 50, 80)
    if character then
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") or part:IsA("Decal") then
                part.Transparency = invisible and 1 or 0
            end
        end
    end
end
invisBtn.MouseButton1Click:Connect(function() setInvisible(not invisible) end)

-- =====================
-- ⚡ CLICK TP
-- =====================
local clickConn = nil
local function setClickTP(state)
    clickTP = state
    clickTPBtn.Text = "⚡ CLICK TP [Q] : " .. (clickTP and "ON" or "OFF")
    clickTPBtn.BackgroundColor3 = clickTP and Color3.fromRGB(255, 0, 255) or Color3.fromRGB(180, 0, 180)
    if clickTP then
        clickConn = mouse.Button1Down:Connect(function()
            local target = mouse.Hit
            if target then hrp.CFrame = CFrame.new(target.Position + Vector3.new(0, 3, 0)) end
        end)
    else
        if clickConn then clickConn:Disconnect() clickConn = nil end
    end
end
clickTPBtn.MouseButton1Click:Connect(function() setClickTP(not clickTP) end)

-- =====================
-- 🌀 SLOWMO
-- =====================
local function setSlowmo(state)
    slowmo = state
    slowmoBtn.Text = "🌀 SLOWMO [M] : " .. (slowmo and "ON" or "OFF")
    slowmoBtn.BackgroundColor3 = slowmo and Color3.fromRGB(0, 200, 255) or Color3.fromRGB(0, 120, 180)
    humanoid.WalkSpeed = slowmo and 4 or (speedOn and 150 or 16)
    humanoid.JumpPower = slowmo and 15 or (superJump and 250 or 50)
end
slowmoBtn.MouseButton1Click:Connect(function() setSlowmo(not slowmo) end)

-- =====================
-- 🔥 INF STAMINA
-- =====================
local staminaConn = nil
local function setInfStamina(state)
    infStamina = state
    staminaBtn.Text = "🔥 INF STAMINA [N] : " .. (infStamina and "ON" or "OFF")
    staminaBtn.BackgroundColor3 = infStamina and Color3.fromRGB(255, 100, 0) or Color3.fromRGB(200, 50, 0)
    if infStamina then
        staminaConn = RunService.Heartbeat:Connect(function()
            if humanoid then
                humanoid.WalkSpeed = speedOn and 150 or 60
                humanoid.JumpPower = superJump and 250 or 80
            end
        end)
    else
        if staminaConn then staminaConn:Disconnect() staminaConn = nil end
        humanoid.WalkSpeed = speedOn and 150 or 16
        humanoid.JumpPower = superJump and 250 or 50
    end
end
staminaBtn.MouseButton1Click:Connect(function() setInfStamina(not infStamina) end)

-- =====================
-- 🌈 RAINBOW
-- =====================
local rainbowConn = nil
local hue = 0

local function setRainbow(state)
    rainbow = state
    rainbowBtn.Text = "🌈 RAINBOW [1] : " .. (rainbow and "ON" or "OFF")
    rainbowBtn.BackgroundColor3 = rainbow and Color3.fromRGB(255, 80, 200) or Color3.fromRGB(255, 0, 100)
    if rainbow then
        rainbowConn = RunService.Heartbeat:Connect(function()
            hue = (hue + 0.005) % 1
            local color = Color3.fromHSV(hue, 1, 1)
            if character then
                for _, part in ipairs(character:GetDescendants()) do
                    if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                        part.BrickColor = BrickColor.new(color)
                    end
                end
            end
        end)
    else
        if rainbowConn then rainbowConn:Disconnect() rainbowConn = nil end
    end
end
rainbowBtn.MouseButton1Click:Connect(function() setRainbow(not rainbow) end)

-- =====================
-- 🔄 AUTO SPIN
-- =====================
local spinConn = nil
local function setAutoSpin(state)
    autoSpin = state
    spinBtn.Text = "🔄 AUTO SPIN [2] : " .. (autoSpin and "ON" or "OFF")
    spinBtn.BackgroundColor3 = autoSpin and Color3.fromRGB(200, 100, 255) or Color3.fromRGB(100, 0, 160)
    if autoSpin then
        spinConn = RunService.RenderStepped:Connect(function()
            spinAngle = spinAngle + 5
            hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(5), 0)
        end)
    else
        if spinConn then spinConn:Disconnect() spinConn = nil end
    end
end
spinBtn.MouseButton1Click:Connect(function() setAutoSpin(not autoSpin) end)

-- =====================
-- 🌊 SUPER DASH
-- =====================
local function setSuperDash(state)
    superDash = state
    dashBtn.Text = "🌊 SUPER DASH [3] : " .. (superDash and "ON" or "OFF")
    dashBtn.BackgroundColor3 = superDash and Color3.fromRGB(0, 230, 255) or Color3.fromRGB(0, 160, 200)
end
dashBtn.MouseButton1Click:Connect(function() setSuperDash(not superDash) end)

-- DASH 실행 (더블탭 or Q 연타)
local lastDash = 0
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if superDash and input.KeyCode == Enum.KeyCode.Q then
        local now = tick()
        if now - lastDash < 0.5 then
            local cam = workspace.CurrentCamera
            hrp.CFrame = hrp.CFrame + cam.CFrame.LookVector * 60
        end
        lastDash = now
    end
end)

-- =====================
-- 🎯 AIM LOCK
-- =====================
local function setAimLock(state)
    aimLock = state
    aimBtn.Text = "🎯 AIM LOCK [4] : " .. (aimLock and "ON" or "OFF")
    aimBtn.BackgroundColor3 = aimLock and Color3.fromRGB(255, 50, 50) or Color3.fromRGB(160, 0, 0)
    if not aimLock then aimTarget = nil end
end
aimBtn.MouseButton1Click:Connect(function() setAimLock(not aimLock) end)

-- 가장 가까운 플레이어 자동 조준
RunService.RenderStepped:Connect(function()
    if aimLock then
        local nearest, minDist = nil, math.huge
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local dist = (hrp.Position - p.Character.HumanoidRootPart.Position).Magnitude
                if dist < minDist then
                    minDist = dist
                    nearest = p
                end
            end
        end
        if nearest and nearest.Character then
            local targetHRP = nearest.Character:FindFirstChild("HumanoidRootPart")
            if targetHRP then
                workspace.CurrentCamera.CFrame = CFrame.lookAt(
                    workspace.CurrentCamera.CFrame.Position,
                    targetHRP.Position
                )
            end
        end
    end
end)

-- =====================
-- 📍 TP TO NEAREST
-- =====================
tpPlayerBtn.MouseButton1Click:Connect(function()
    local nearest, minDist = nil, math.huge
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (hrp.Position - p.Character.HumanoidRootPart.Position).Magnitude
            if dist < minDist then minDist = dist nearest = p end
        end
    end
    if nearest then
        hrp.CFrame = nearest.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
    end
end)

-- =====================
-- 💀 RESET
-- =====================
resetBtn.MouseButton1Click:Connect(function() humanoid.Health = 0 end)

-- =====================
-- ⌨️ 키보드 단축키
-- =====================
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    local k = input.KeyCode
    if     k == Enum.KeyCode.E then if flying then stopFlying() else startFlying() end
    elseif k == Enum.KeyCode.R then setNoclip(not noclip)
    elseif k == Enum.KeyCode.F then setSpeed(not speedOn)
    elseif k == Enum.KeyCode.G then setInfJump(not infJump)
    elseif k == Enum.KeyCode.H then setGodMode(not godMode)
    elseif k == Enum.KeyCode.J then setFullbright(not fullbright)
    elseif k == Enum.KeyCode.K then setAntiAfk(not antiAfk)
    elseif k == Enum.KeyCode.L then setESP(not espOn)
    elseif k == Enum.KeyCode.Z then setSuperJump(not superJump)
    elseif k == Enum.KeyCode.X then setLowGravity(not lowGravity)
    elseif k == Enum.KeyCode.C then setGiant(not giantOn)
    elseif k == Enum.KeyCode.B then setMini(not miniOn)
    elseif k == Enum.KeyCode.V then setInvisible(not invisible)
    elseif k == Enum.KeyCode.Q then setClickTP(not clickTP)
    elseif k == Enum.KeyCode.M then setSlowmo(not slowmo)
    elseif k == Enum.KeyCode.N then setInfStamina(not infStamina)
    elseif k == Enum.KeyCode.One then setRainbow(not rainbow)
    elseif k == Enum.KeyCode.Two then setAutoSpin(not autoSpin)
    elseif k == Enum.KeyCode.Three then setSuperDash(not superDash)
    elseif k == Enum.KeyCode.Four then setAimLock(not aimLock)
    end
end)

-- =====================
-- 🎮 비행 이동 처리
-- =====================
RunService.RenderStepped:Connect(function()
    if flying and bodyVelocity and bodyGyro then
        local camera = workspace.CurrentCamera
        local moveDir = Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir += camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir -= camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir -= camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir += camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir += Vector3.new(0,1,0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir -= Vector3.new(0,1,0) end
        bodyVelocity.Velocity = moveDir.Magnitude > 0 and moveDir.Unit * flySpeed or Vector3.zero
        bodyGyro.CFrame = camera.CFrame
    end
end)

-- =====================
-- 🔄 리스폰 처리
-- =====================
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    hrp = newChar:WaitForChild("HumanoidRootPart")
    humanoid = newChar:WaitForChild("Humanoid")
    flying, noclip, speedOn, infJump = false, false, false, false
    godMode, giantOn, miniOn, invisible = false, false, false, false
    rainbow, autoSpin = false, false
    stopFlying()
end)
