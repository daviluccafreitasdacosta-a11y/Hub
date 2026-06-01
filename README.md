--!strict
local playersService = game:GetService("Players")
local localPlayer = playersService.LocalPlayer
local tweens = game:GetService("TweenService")
local runService = game:GetService("RunService")
local userInputService = game:GetService("UserInputService")
local lightingService = game:GetService("Lighting")

-- Garante compatibilidade direta na PlayerGui do Delta Mobile
local playerGui = localPlayer:WaitForChild("PlayerGui")
local oldGui = playerGui:FindFirstChild("LukaHubV2")
if oldGui then oldGui:Destroy() end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LukaHubV2"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

-- Nova Paleta de Cores (Design Moderno Dark/Neon)
local MAIN_BG = Color3.fromRGB(11, 11, 14)
local SIDEBAR_BG = Color3.fromRGB(16, 16, 20)
local CARD_BG = Color3.fromRGB(22, 22, 28)
local ACCENT = Color3.fromRGB(0, 240, 255)
local TROLL_NEON = Color3.fromRGB(255, 0, 110)
local TEXT_MAIN = Color3.fromRGB(245, 245, 250)
local TEXT_DARK = Color3.fromRGB(130, 130, 140)

-- Janela Principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 520, 0, 310)
MainFrame.Position = UDim2.new(0.5, -260, 0.4, -155)
MainFrame.BackgroundColor3 = MAIN_BG
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = ACCENT
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- Sidebar (Menu Lateral)
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 130, 1, 0)
Sidebar.BackgroundColor3 = SIDEBAR_BG
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

local SideCorner = Instance.new("UICorner")
SideCorner.CornerRadius = UDim.new(0, 12)
SideCorner.Parent = Sidebar

-- Bloqueia os cantos arredondados do lado direito da barra lateral
local SideFix = Instance.new("Frame")
SideFix.Size = UDim2.new(0, 15, 1, 0)
SideFix.Position = UDim2.new(1, -15, 0, 0)
SideFix.BackgroundColor3 = SIDEBAR_BG
SideFix.BorderSizePixel = 0
SideFix.Parent = Sidebar

local Logo = Instance.new("TextLabel")
Logo.Size = UDim2.new(1, 0, 0, 50)
Logo.BackgroundTransparency = 1
Logo.Text = "⚡ LUKA HUB"
Logo.TextColor3 = ACCENT
Logo.TextSize = 14
Logo.Font = Enum.Font.GothamBold
Logo.Parent = Sidebar

local NavList = Instance.new("UIListLayout")
NavList.Padding = UDim.new(0, 4)
NavList.HorizontalAlignment = Enum.HorizontalAlignment.Center
NavList.Parent = Sidebar

-- Containers de Conteúdo (Lado Direito)
local ContentContainer = Instance.new("Frame")
ContentContainer.Size = UDim2.new(1, -145, 1, -20)
ContentContainer.Position = UDim2.new(0, 140, 0, 10)
ContentContainer.BackgroundTransparency = 1
ContentContainer.Parent = MainFrame

-- Botão Flutuante Móvel (Arredondado e Compacto)
local FloatButton = Instance.new("TextButton")
FloatButton.Size = UDim2.new(0, 52, 0, 52)
FloatButton.Position = UDim2.new(0.05, 0, 0.2, 0)
FloatButton.BackgroundColor3 = SIDEBAR_BG
FloatButton.Text = "LK"
FloatButton.TextColor3 = ACCENT
FloatButton.TextSize = 14
FloatButton.Font = Enum.Font.GothamBold
FloatButton.Active = true
FloatButton.Draggable = true
FloatButton.Parent = ScreenGui

local FloatCorner = Instance.new("UICorner")
FloatCorner.CornerRadius = UDim.new(1, 0)
FloatCorner.Parent = FloatButton

local FloatStroke = Instance.new("UIStroke")
FloatStroke.Color = ACCENT
FloatStroke.Thickness = 2
FloatStroke.Parent = FloatButton

FloatButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
    FloatButton.Visible = false
end)

-- Botão de Fechar dentro do Menu Principal
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Position = UDim2.new(1, -34, 0, 10)
CloseBtn.BackgroundColor3 = CARD_BG
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 70, 70)
CloseBtn.TextSize = 11
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = MainFrame

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = CloseBtn

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    FloatButton.Visible = true
end)

-- Sistema de Criação de Abas Dinâmicas
local tabs = {}
local activeTabButton = nil

local function createTab(name: string)
    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, 0, 1, 0)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 3
    scroll.ScrollBarImageColor3 = ACCENT
    scroll.Visible = false
    scroll.Parent = ContentContainer

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 6)
    layout.Parent = scroll

    runService.RenderStepped:Connect(function()
        scroll.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10)
    end)

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
    btn.BackgroundTransparency = 1
    btn.Text = name
    btn.TextColor3 = TEXT_DARK
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.Parent = Sidebar

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = btn

    btn.MouseButton1Click:Connect(function()
        for _, t in pairs(tabs) do
            t.Scroll.Visible = false
            t.Btn.BackgroundTransparency = 1
            t.Btn.TextColor3 = TEXT_DARK
        end
        scroll.Visible = true
        btn.BackgroundTransparency = 0
        btn.BackgroundColor3 = CARD_BG
        btn.TextColor3 = ACCENT
    end)

    tabs[name] = {Scroll = scroll, Btn = btn}
end

-- Inicializando as 3 Novas Abas Solicitadas
createTab("💪 Físico")
createTab("😈 Troll")
createTab("🎯 Teleporte")

-- Forçar a primeira aba a abrir por padrão
tabs["💪 Físico"].Scroll.Visible = true
tabs["💪 Físico"].Btn.BackgroundTransparency = 0
tabs["💪 Físico"].Btn.BackgroundColor3 = CARD_BG
tabs["💪 Físico"].Btn.TextColor3 = ACCENT

-- Elementos de Interface Customizados (Toggles e Inputs)
local function addToggle(tabName: string, text: string, callback: (boolean) -> ())
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -6, 0, 40)
    frame.BackgroundColor3 = CARD_BG
    frame.BorderSizePixel = 0
    frame.Parent = tabs[tabName].Scroll

    local fCorner = Instance.new("UICorner")
    fCorner.CornerRadius = UDim.new(0, 6)
    fCorner.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = TEXT_MAIN
    label.TextSize = 11
    label.Font = Enum.Font.GothamBold
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local switch = Instance.new("TextButton")
    switch.Size = UDim2.new(0, 45, 0, 22)
    switch.Position = UDim2.new(1, -57, 0.5, -11)
    switch.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    switch.Text = ""
    switch.Parent = frame

    local sCorner = Instance.new("UICorner")
    sCorner.CornerRadius = UDim.new(1, 0)
    sCorner.Parent = switch

    local indicator = Instance.new("Frame")
    indicator.Size = UDim2.new(0, 16, 0, 16)
    indicator.Position = UDim2.new(0, 3, 0.5, -8)
    indicator.BackgroundColor3 = Color3.fromRGB(180, 180, 190)
    indicator.Parent = switch

    local indCorner = Instance.new("UICorner")
    indCorner.CornerRadius = UDim.new(1, 0)
    indCorner.Parent = indicator

    local enabled = false
    switch.MouseButton1Click:Connect(function()
        enabled = not enabled
        local targetPos = if enabled then UDim2.new(1, -19, 0.5, -8) else UDim2.new(0, 3, 0.5, -8)
        local targetColor = if enabled then (if tabName == "😈 Troll" then TROLL_NEON else ACCENT) else Color3.fromRGB(45, 45, 55)
        local indColor = if enabled then Color3.fromRGB(255, 255, 255) else Color3.fromRGB(180, 180, 190)
        
        tweens:Create(switch, TweenInfo.new(0.15), {BackgroundColor3 = targetColor}):Play()
        tweens:Create(indicator, TweenInfo.new(0.15), {Position = targetPos, BackgroundColor3 = indColor}):Play()
        callback(enabled)
    end)
end

local function addSliderInput(tabName: string, text: string, default: string, callback: (string) -> ())
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -6, 0, 40)
    frame.BackgroundColor3 = CARD_BG
    frame.BorderSizePixel = 0
    frame.Parent = tabs[tabName].Scroll

    local fCorner = Instance.new("UICorner")
    fCorner.CornerRadius = UDim.new(0, 6)
    fCorner.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.6, 0, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = TEXT_MAIN
    label.TextSize = 11
    label.Font = Enum.Font.GothamBold
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local box = Instance.new("TextBox")
    box.Size = UDim2.new(0, 65, 0, 24)
    box.Position = UDim2.new(1, -77, 0.5, -12)
    box.BackgroundColor3 = MAIN_BG
    box.Text = default
    box.TextColor3 = ACCENT
    box.Font = Enum.Font.GothamBold
    box.TextSize = 11
    box.Parent = frame

    local bCorner = Instance.new("UICorner")
    bCorner.CornerRadius = UDim.new(0, 6)
    bCorner.Parent = box

    local bStroke = Instance.new("UIStroke")
    bStroke.Color = Color3.fromRGB(45, 45, 55)
    bStroke.Thickness = 1
    bStroke.Parent = box

    box.FocusLost:Connect(function()
        callback(box.Text)
    end)
end

-- =========================================================
-- 📌 ABA 1: FÍSICO (Customização do Personagem)
-- =========================================================
local speedOn, jumpOn, infJumpOn, flyOn, noclipOn = false, false, false, false, false
local speedValue, jumpValue, flySpeed = 16, 50, 45

addToggle("💪 Físico", "Mudar Velocidade Nativa", function(state) speedOn = state end)
addSliderInput("💪 Físico", "Ajustar Velocidade", "16", function(txt)
    speedValue = tonumber(txt) or 16
end)

addToggle("💪 Físico", "Mudar Força do Pulo", function(state) jumpOn = state end)
addSliderInput("💪 Físico", "Ajustar Pulo", "50", function(txt)
    jumpValue = tonumber(txt) or 50
end)

addToggle("💪 Físico", "Pulo Infinito", function(state) infJumpOn = state end)
addToggle("💪 Físico", "Ativar Voo (Fly)", function(state) flyOn = state end)
addToggle("💪 Físico", "NoClip (Atravessar Paredes)", function(state) noclipOn = state end)

-- Loops de Controle do Personagem (Físico)
runService.RenderStepped:Connect(function()
    local char = localPlayer.Character
    local hum = if char then char:FindFirstChildOfClass("Humanoid") else nil
    if hum then
        if speedOn then hum.WalkSpeed = speedValue end
        if jumpOn then
            hum.UseJumpPower = true
            hum.JumpPower = jumpValue
        end
    end
end)

runService.Stepped:Connect(function()
    if noclipOn and localPlayer.Character then
        for _, part in localPlayer.Character:GetDescendants() do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

userInputService.JumpRequest:Connect(function()
    if infJumpOn then
        local char = localPlayer.Character
        local hum = if char then char:FindFirstChildOfClass("Humanoid") else nil
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

-- Mecânica de Voo Estável
local flyGyro, flyVelocity
runService.RenderStepped:Connect(function()
    local char = localPlayer.Character
    local hrp = if char then char:FindFirstChild("HumanoidRootPart") else nil
    if flyOn and hrp then
        if not flyGyro or flyGyro.Parent ~= hrp then
            if flyGyro then flyGyro:Destroy() end
            if flyVelocity then flyVelocity:Destroy() end
            
            flyGyro = Instance.new("BodyGyro")
            flyGyro.P = 9e4
            flyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            flyGyro.Parent = hrp
            
            flyVelocity = Instance.new("BodyVelocity")
            flyVelocity.maxForce = Vector3.new(9e9, 9e9, 9e9)
            flyVelocity.Parent = hrp
        end
        local cam = workspace.CurrentCamera
        local hum = char:FindFirstChildOfClass("Humanoid")
        local move = if hum then hum.MoveDirection else Vector3.zero
        flyVelocity.velocity = if move.Magnitude > 0 then cam.CFrame.LookVector * flySpeed * move.Magnitude else Vector3.new(0, 0.1, 0)
        flyGyro.cframe = cam.CFrame
    else
        if flyGyro then flyGyro:Destroy(); flyGyro = nil end
        if flyVelocity then flyVelocity:Destroy(); flyVelocity = nil end
    end
end)


-- =========================================================
-- 📌 ABA 2: TROLL (Sistemas Maliciosos)
-- =========================================================
local flingOn, pullOn, bringOn = false, false, false
local flingBv = nil

addToggle("😈 Troll", "Fling (Matar ao Encostar)", function(state)
    flingOn = state
    if not state and flingBv then flingBv:Destroy(); flingBv = nil end
end)

addToggle("😈 Troll", "Puxar Blocos do Mapa (Imã)", function(state) pullOn = state end)
addToggle("😈 Troll", "Trazer Todos os Players", function(state) bringOn = state end)

runService.Heartbeat:Connect(function()
    local char = localPlayer.Character
    local hrp = if char then char:FindFirstChild("HumanoidRootPart") else nil
    if flingOn and hrp then
        if not flingBv or flingBv.Parent ~= hrp then
            if flingBv then flingBv:Destroy() end
            flingBv = Instance.new("BodyAngularVelocity")
            flingBv.MaxTorque = Vector3.new(1, 1, 1) * math.huge
            flingBv.P = math.huge
            flingBv.Parent = hrp
        end
        flingBv.AngularVelocity = if flingBv.AngularVelocity.Y == 95000 then Vector3.new(0, 95160, 0) else Vector3.new(0, 95000, 0)
        for _, part in char:GetDescendants() do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.3)
        local char = localPlayer.Character
        local hrp = if char then char:FindFirstChild("HumanoidRootPart") else nil
        if hrp then
            if pullOn then
                for _, obj in workspace:GetDescendants() do
                    if obj:IsA("BasePart") and not obj.Anchored and not obj:IsDescendantOf(char) and not obj.Parent:FindFirstChildOfClass("Humanoid") then
                        if (obj.Position - hrp.Position).Magnitude < 160 then
                            obj.AssemblyLinearVelocity = (hrp.Position - obj.Position).Unit * 65
                        end
                    end
                end
            end
            if bringOn then
                for _, p in playersService:GetPlayers() do
                    if p ~= localPlayer and p.Character then
                        local tHrp = p.Character:FindFirstChild("HumanoidRootPart")
                        if tHrp then hrp.CFrame = tHrp.CFrame * CFrame.new(0, 0, 1) end
                    end
                end
            end
        end
    end
end)


-- =========================================================
-- 📌 ABA 3: TELEPORTE (Lista Inteligente de Alvos)
-- =========================================================
local targetSpectate = nil

local function rebuildTeleportList()
    local scroll = tabs["🎯 Teleporte"].Scroll
    for _, child in scroll:GetChildren() do
        if child:IsA("Frame") then child:Destroy() end
    end

    for _, p in playersService:GetPlayers() do
        if p ~= localPlayer then
            local pFrame = Instance.new("Frame")
            pFrame.Size = UDim2.new(1, -6, 0, 45)
            pFrame.BackgroundColor3 = CARD_BG
            pFrame.Parent = scroll

            local pfCorner = Instance.new("UICorner")
            pfCorner.CornerRadius = UDim.new(0, 6)
            pfCorner.Parent = pFrame

            local pName = Instance.new("TextLabel")
            pName.Size = UDim2.new(0.5, 0, 1, 0)
            pName.Position = UDim2.new(0, 12, 0, 0)
            pName.BackgroundTransparency = 1
            pName.Text = p.DisplayName or p.Name
            pName.TextColor3 = TEXT_MAIN
            pName.TextSize = 11
            pName.Font = Enum.Font.GothamBold
            pName.TextXAlignment = Enum.TextXAlignment.Left
            pName.Parent = pFrame

            -- Botão ir até o Alvo
            local tpBtn = Instance.new("TextButton")
            tpBtn.Size = UDim2.new(0, 55, 0, 26)
            tpBtn.Position = UDim2.new(1, -125, 0.5, -13)
            tpBtn.BackgroundColor3 = MAIN_BG
            pName.TextSize = 10
            tpBtn.Text = "Teleport"
            tpBtn.TextColor3 = ACCENT
            tpBtn.Font = Enum.Font.GothamBold
            tpBtn.Parent = pFrame

            local c1 = Instance.new("UICorner")
            c1.CornerRadius = UDim.new(0, 6)
            c1.Parent = tpBtn

            -- Botão Assistir Alvo (Câmera)
            local viewBtn = Instance.new("TextButton")
            viewBtn.Size = UDim2.new(0, 55, 0, 26)
            viewBtn.Position = UDim2.new(1, -65, 0.5, -13)
            viewBtn.BackgroundColor3 = MAIN_BG
            viewBtn.Text = "Olhar"
            viewBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
            viewBtn.Font = Enum.Font.GothamBold
            viewBtn.Parent = pFrame

            local c2 = Instance.new("UICorner")
            c2.CornerRadius = UDim.new(0, 6)
            c2.Parent = viewBtn

            tpBtn.MouseButton1Click:Connect(function()
                local tChar, mChar = p.Character, localPlayer.Character
                if tChar and mChar then
                    local tHrp = tChar:FindFirstChild("HumanoidRootPart")
                    local mHrp = mChar:FindFirstChild("HumanoidRootPart")
                    if tHrp and mHrp then mHrp.CFrame = tHrp.CFrame * CFrame.new(0, 3, 0) end
                end
            end)

            viewBtn.MouseButton1Click:Connect(function()
                local cam = workspace.CurrentCamera
                if targetSpectate == p then
                    targetSpectate = nil
                    viewBtn.Text = "Olhar"
                    viewBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
                    if localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid") then
                        cam.CameraSubject = localPlayer.Character:FindFirstChildOfClass("Humanoid")
                    end
                else
                    targetSpectate = p
                    viewBtn.Text = "Parar"
                    viewBtn.TextColor3 = TROLL_NEON
                    if p.Character and p.Character:FindFirstChildOfClass("Humanoid") then
                        cam.CameraSubject = p.Character:FindFirstChildOfClass("Humanoid")
                    end
                end
            end)
        end
    end
end

playersService.PlayerAdded:Connect(rebuildTeleportList)
playersService.PlayerRemoving:Connect(rebuildTeleportList)
rebuildTeleportList()
