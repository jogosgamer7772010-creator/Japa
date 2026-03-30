# Japa
Script 
local player = game.Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local root = character:WaitForChild("HumanoidRootPart")

-- GUI
local gui = Instance.new("ScreenGui", player.PlayerGui)

-- Botão abrir/fechar
local toggleBtn = Instance.new("TextButton", gui)
toggleBtn.Size = UDim2.new(0, 50, 0, 50)
toggleBtn.Position = UDim2.new(0, 20, 0.5, -25)
toggleBtn.Text = "☰"
toggleBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
toggleBtn.TextColor3 = Color3.new(1,1,1)

Instance.new("UICorner", toggleBtn)

-- Frame principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 260, 0, 220)
frame.Position = UDim2.new(0.5, -130, 0.5, -110)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Visible = false

Instance.new("UICorner", frame)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,40)
title.Text = "MENU"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextScaled = true

-- Caixa
local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.8,0,0,35)
box.Position = UDim2.new(0.1,0,0.3,0)
box.PlaceholderText = "Velocidade"
box.BackgroundColor3 = Color3.fromRGB(40,40,40)
box.TextColor3 = Color3.new(1,1,1)

Instance.new("UICorner", box)

-- Botão speed
local speedBtn = Instance.new("TextButton", frame)
speedBtn.Size = UDim2.new(0.8,0,0,35)
speedBtn.Position = UDim2.new(0.1,0,0.55,0)
speedBtn.Text = "Aplicar Speed"

Instance.new("UICorner", speedBtn)

-- Botão fly
local flyBtn = Instance.new("TextButton", frame)
flyBtn.Size = UDim2.new(0.8,0,0,35)
flyBtn.Position = UDim2.new(0.1,0,0.75,0)
flyBtn.Text = "Ativar Voo"

Instance.new("UICorner", flyBtn)

-- ANIMAÇÃO ABRIR/FECHAR
toggleBtn.MouseButton1Click:Connect(function()
    frame.Visible = true
    
    frame.Size = UDim2.new(0,0,0,0)
    local tween = TweenService:Create(frame, TweenInfo.new(0.3), {
        Size = UDim2.new(0,260,0,220)
    })
    tween:Play()
end)

-- FECHAR ao clicar fora
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        if frame.Visible then
            frame.Visible = false
        end
    end
end)

-- ARRASTAR MENU
local dragging = false
local dragStart, startPos

frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
    end
end)

frame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.Touch then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- RGB ANIMADO
task.spawn(function()
    while true do
        for i = 0, 1, 0.01 do
            frame.BackgroundColor3 = Color3.fromHSV(i, 0.6, 0.3)
            task.wait()
        end
    end
end)

-- SPEED
speedBtn.MouseButton1Click:Connect(function()
    local speed = tonumber(box.Text)
    if speed then
        humanoid.WalkSpeed = speed
    else
        box.Text = "Inválido"
    end
end)

-- FLY
local flying = false
local bv

flyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    
    if flying then
        flyBtn.Text = "Desativar Voo"
        
        bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1,1,1) * 100000
        bv.Parent = root
        
        humanoid.PlatformStand = true

        game:GetService("RunService").RenderStepped:Connect(function()
            if flying and bv then
                local cam = workspace.CurrentCamera
                bv.Velocity = cam.CFrame.LookVector * 60
            end
        end)
        
    else
        flyBtn.Text = "Ativar Voo"
        
        if bv then
            bv:Destroy()
        end
        
        humanoid.PlatformStand = false
    end
end)
