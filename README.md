-- W-Azure de Fãs - Script Educativo Ultimate Mobile
-- Executor Delta / Apenas estudo

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

-- Flags de funções
local flags = {
    autoFarm = false,
    fly = false,
    speed = false,
    godMode = false,
    noclip = false,
    aimbot = false,
    teleportFruit = false
}

-- GUI W-Azure de Fãs
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.Name = "WAzureDeFansGUI"

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,220,0,480)
frame.Position = UDim2.new(0,10,0,50)
frame.BackgroundColor3 = Color3.fromRGB(35,35,35)
frame.BackgroundTransparency = 0.1
frame.Parent = screenGui
frame.Active = true
frame.Draggable = true

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1,0,0,30)
titleLabel.Position = UDim2.new(0,0,0,0)
titleLabel.BackgroundTransparency = 0.5
titleLabel.TextColor3 = Color3.fromRGB(0,255,255)
titleLabel.Text = "W-Azure de Fãs - Ultimate Study"
titleLabel.Font = Enum.Font.SourceSansBold
titleLabel.TextSize = 18
titleLabel.Parent = frame

-- Função criar botão Ultimate com efeitos
local function createButton(name, text, yPos)
    local button = Instance.new("TextButton")
    button.Name = name
    button.Text = text
    button.Size = UDim2.new(0,200,0,40)
    button.Position = UDim2.new(0,10,0,yPos)
    button.BackgroundColor3 = Color3.fromRGB(55,55,55)
    button.TextColor3 = Color3.fromRGB(255,255,255)
    button.Parent = frame

    local status = Instance.new("Frame")
    status.Size = UDim2.new(0,12,1,0)
    status.Position = UDim2.new(1,4,0,0)
    status.BackgroundColor3 = Color3.fromRGB(255,0,0)
    status.Parent = button

    local cooldownBar = Instance.new("Frame")
    cooldownBar.Size = UDim2.new(1,0,0,6)
    cooldownBar.Position = UDim2.new(0,0,1,0)
    cooldownBar.BackgroundColor3 = Color3.fromRGB(0,255,0)
    cooldownBar.Visible = false
    cooldownBar.Parent = button

    return button, status, cooldownBar
end

-- Criar botões e status
local buttons, statusBars, cooldownBars = {}, {}, {}
local yPos = 40
local function addButton(key,text)
    local btn,status,cooldown = createButton(key,text,yPos)
    buttons[key] = btn
    statusBars[key] = status
    cooldownBars[key] = cooldown
    yPos = yPos + 55
end

addButton("autoFarm","AutoFarm")
addButton("fly","Fly")
addButton("speed","Speed")
addButton("godMode","GodMode")
addButton("noclip","Noclip")
addButton("aimbot","Aimbot")
addButton("teleportFruit","Fruits")
addButton("comboMaster","Combo Master")

-- Contadores e indicadores
local enemyCounter = Instance.new("TextLabel")
enemyCounter.Size = UDim2.new(0,200,0,28)
enemyCounter.Position = UDim2.new(0,10,0,yPos)
enemyCounter.BackgroundTransparency = 0.5
enemyCounter.TextColor3 = Color3.fromRGB(255,255,255)
enemyCounter.Text = "Inimigos próximos: 0"
enemyCounter.Parent = frame
yPos = yPos + 32

local fruitCounter = Instance.new("TextLabel")
fruitCounter.Size = UDim2.new(0,200,0,28)
fruitCounter.Position = UDim2.new(0,10,0,yPos)
fruitCounter.BackgroundTransparency = 0.5
fruitCounter.TextColor3 = Color3.fromRGB(255,255,255)
fruitCounter.Text = "Frutas próximas: 0"
fruitCounter.Parent = frame
yPos = yPos + 32

local attacksCounter = Instance.new("TextLabel")
attacksCounter.Size = UDim2.new(0,200,0,28)
attacksCounter.Position = UDim2.new(0,10,0,yPos)
attacksCounter.BackgroundTransparency = 0.5
attacksCounter.TextColor3 = Color3.fromRGB(255,255,255)
attacksCounter.Text = "Ataques realizados: 0"
attacksCounter.Parent = frame

local attackCount = 0

-- Função toggle com efeitos e cooldown
local cooldownTime = 1.5
local function toggle(flagName)
    if cooldownBars[flagName].Visible then return end
    flags[flagName] = not flags[flagName]
    statusBars[flagName].BackgroundColor3 = flags[flagName] and Color3.fromRGB(0,255,0) or Color3.fromRGB(255,0,0)

    cooldownBars[flagName].Visible = true
    cooldownBars[flagName].Size = UDim2.new(1,0,0,6)
    local tween = TweenService:Create(cooldownBars[flagName],TweenInfo.new(cooldownTime),{Size=UDim2.new(0,0,0,6)})
    tween:Play()
    tween.Completed:Connect(function() cooldownBars[flagName].Visible = false end)

    if flagName ~= "comboMaster" then
        if flags.noclip and flags.fly and flags.godMode then
            for _, s in pairs(statusBars) do
                local blink = TweenService:Create(s,TweenInfo.new(0.4,Enum.EasingStyle.Linear,Enum.EasingDirection.InOut,-1,true),{BackgroundTransparency=0.7})
                blink:Play()
            end
        end
    end
end

-- Conectar botões
for key, btn in pairs(buttons) do
    btn.MouseButton1Click:Connect(function()
        if key == "comboMaster" then
            local comboFlags = {"noclip","fly","godMode","speed","aimbot"}
            for _, f in pairs(comboFlags) do
                if not flags[f] then toggle(f) end
            end
        else
            toggle(key)
        end
    end)
end

-- Função educativa para achar NPC mais próximo
local function closestNPC()
    local closestDist = math.huge
    local target = nil
    for _, npc in pairs(workspace.Enemies:GetChildren()) do
        if npc:FindFirstChild("HumanoidRootPart") then
            local dist = (player.Character.HumanoidRootPart.Position - npc.HumanoidRootPart.Position).Magnitude
            if dist < closestDist then
                closestDist = dist
                target = npc
            end
        end
    end
    return target
end

-- Loop Stepped W-Azure de Fãs
RunService.Stepped:Connect(function()
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("Humanoid") then
        local hrp = character.HumanoidRootPart
        local humanoid = character.Humanoid

        -- Atualizar contadores
        enemyCounter.Text = "Inimigos próximos: "..#workspace.Enemies:GetChildren()
        fruitCounter.Text = "Frutas próximas: "..#workspace.Fruits:GetChildren()
        attacksCounter.Text = "Ataques realizados: "..attackCount

        -- Noclip
        if flags.noclip then
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then part.CanCollide = false end
            end
            hrp.Velocity = Vector3.new(0,0,0)
        end

        -- Fly
        if flags.fly then
            hrp.Velocity = Vector3.new(0,0,0)
        end

        -- Speed
        humanoid.WalkSpeed = flags.speed and 100 or 16

        -- GodMode
        if flags.godMode then
            humanoid.Health = humanoid.MaxHealth
        end

        -- AutoFarm educativo
        if flags.autoFarm then
            local npc = closestNPC()
            if npc then
                hrp.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0,0,3)
                local remote = ReplicatedStorage:FindFirstChild("Remotes") and ReplicatedStorage.Remotes:FindFirstChild("HitEnemy")
                if remote then 
                    remote:FireServer(npc.HumanoidRootPart.Position)
                    attackCount = attackCount + 1
                end
            end
        end

        -- Aimbot educativo
        if flags.aimbot then
            local npc = closestNPC()
            if npc then
                hrp.CFrame = CFrame.new(hrp.Position, npc.HumanoidRootPart.Position)
            end
        end

        -- Teleporte para frutas
        if flags.teleportFruit then
            for _, fruit in pairs(workspace.Fruits:GetChildren()) do
                if fruit:IsA("BasePart") then
                    hrp.CFrame = fruit.CFrame + Vector3.new(0,3,0)
                    break
                end
            end
        end
    end
end)
