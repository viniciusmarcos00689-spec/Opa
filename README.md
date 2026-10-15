--[[
    EGG HUNTER - ROBLOX STUDIO
    Painel de demonstração para seu próprio jogo.
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local flying = false
local noclip = false
local flySpeed = 50
local walkSpeed = 16

local gui = Instance.new("ScreenGui")
gui.Name = "EggHunter"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Janela principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 320, 0, 470)
frame.Position = UDim2.new(0.5, -160, 0.5, -235)
frame.BackgroundColor3 = Color3.fromRGB(25, 20, 40)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 14)
corner.Parent = frame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 0, 40)
title.Position = UDim2.new(0, 10, 0, 5)
title.BackgroundTransparency = 1
title.Text = "🥚 EGG HUNTER"
title.TextColor3 = Color3.fromRGB(255, 220, 100)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = frame

-- Imagem do ovo
local eggImage = Instance.new("ImageLabel")
eggImage.Size = UDim2.new(0, 90, 0, 90)
eggImage.Position = UDim2.new(0.5, -45, 0, 50)
eggImage.BackgroundTransparency = 1

-- Imagem ilustrativa de ovo
eggImage.Image = "rbxassetid://7072725342"
eggImage.ScaleType = Enum.ScaleType.Fit
eggImage.Parent = frame

-- Estado
local status = Instance.new("TextLabel")
status.Size = UDim2.new(1, -20, 0, 25)
status.Position = UDim2.new(0, 10, 0, 145)
status.BackgroundTransparency = 1
status.Text = "Status: pronto"
status.TextColor3 = Color3.new(1, 1, 1)
status.TextScaled = true
status.Parent = frame

local function createButton(text, y, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -30, 0, 34)
    button.Position = UDim2.new(0, 15, 0, y)
    button.BackgroundColor3 = Color3.fromRGB(90, 55, 145)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Text = text
    button.TextScaled = true
    button.Font = Enum.Font.GothamBold
    button.Parent = frame

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = button

    button.MouseButton1Click:Connect(callback)
    return button
end

-- Procura ovos do seu próprio jogo.
-- Coloque os modelos de ovos na pasta Workspace.Eggs.
local function findEgg(category)
    local folder = workspace:FindFirstChild("Eggs")

    if not folder then
        status.Text = "Crie a pasta Workspace.Eggs"
        return
    end

    local character = player.Character
    local root = character and character:FindFirstChild("HumanoidRootPart")

    if not root then
        status.Text = "Personagem não encontrado"
        return
    end

    local closestEgg
    local shortestDistance = math.huge

    for _, egg in ipairs(folder:GetChildren()) do
        local eggCategory = egg:GetAttribute("Category")
        local reward = egg:GetAttribute("Reward")

        if eggCategory == category and
            typeof(reward) == "number" and reward > 20000000 then

            local part

            if egg:IsA("BasePart") then
                part = egg
            elseif egg:IsA("Model") then
                part = egg.PrimaryPart
                    or egg:FindFirstChildWhichIsA("BasePart")
            end

            if part then
                local distance = (part.Position - root.Position).Magnitude

                if distance < shortestDistance then
                    shortestDistance = distance
                    closestEgg = part
                end
            end
        end
    end

    if closestEgg then
        status.Text = "Ovo encontrado: " .. closestEgg.Name

        -- Apenas identifica o ovo mais próximo.
        -- A movimentação e a coleta devem ser implementadas
        -- com validação no servidor do seu próprio jogo.
    else
        status.Text = "Nenhum ovo elegível encontrado"
    end
end

createButton("🌌 Procurar ovo cósmico", 180, function()
    findEgg("Cosmic")
end)

createButton("🔐 Procurar ovo secreto", 220, function()
    findEgg("Secret")
end)

createButton("👑 Procurar ovo divino", 260, function()
    findEgg("Divine")
end)

createButton("♾️ Procurar ovo eterno", 300, function()
    findEgg("Eternal")
end)

-- Velocidade do personagem
createButton("🏃 Aumentar velocidade", 340, function()
    local character = player.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")

    if humanoid then
        walkSpeed = math.clamp(walkSpeed + 5, 16, 100)
        humanoid.WalkSpeed = walkSpeed
        status.Text = "Velocidade: " .. walkSpeed
    end
end)

createButton("🐢 Diminuir velocidade", 380, function()
    local character = player.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")

    if humanoid then
        walkSpeed = math.clamp(walkSpeed - 5, 16, 100)
        humanoid.WalkSpeed = walkSpeed
        status.Text = "Velocidade: " .. walkSpeed
    end
end)

createButton("❌ Fechar painel", 420, function()
    gui:Destroy()
end)

-- Reinicia o personagem local após 20 minutos.
-- Para reiniciar a experiência inteira, use lógica no servidor.
task.delay(20 * 60, function()
    if gui.Parent and player.Character then
        status.Text = "20 minutos decorridos"
    end
end)
