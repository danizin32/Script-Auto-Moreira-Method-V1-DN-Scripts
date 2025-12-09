-- LocalScript — GUI com blur de fundo + TextBox + botão; ao clicar, a GUI some
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Blur = Instance.new("BlurEffect")
Blur.Name = "BackgroundBlur"

-- Cria ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PrivateServerUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Cria o efeito de blur e o aplica
Blur.Parent = game:GetService("Lighting")
Blur.Size = 0

-- Função pra ativar blur com transição
local TweenService = game:GetService("TweenService")
local function fadeInBlur()
    Blur.Size = 0
    TweenService:Create(Blur, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = 24}):Play()
end

local function fadeOutBlurAndRemoveGui()
    TweenService:Create(Blur, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {Size = 0}):Play()
    -- Opcional: esperar a transição antes de remover
    delay(0.32, function()
        screenGui:Destroy()
        Blur:Destroy()
    end)
end

-- Frame principal
local main = Instance.new("Frame")
main.Size = UDim2.new(0, 380, 0, 160)
main.Position = UDim2.new(0.5, -190, 0.5, -80)
main.AnchorPoint = Vector2.new(0.5, 0.5)
main.BackgroundColor3 = Color3.fromRGB(10, 25, 50)
main.BorderSizePixel = 0
main.Parent = screenGui
main.Active = true

local uicorner = Instance.new("UICorner", main)
uicorner.CornerRadius = UDim.new(0, 14)

-- “Glow” ou borda luminosa
local glow = Instance.new("Frame")
glow.Size = UDim2.new(1, 10, 1, 10)
glow.Position = UDim2.new(0, -5, 0, -5)
glow.BackgroundColor3 = Color3.fromRGB(50, 140, 255)
glow.BackgroundTransparency = 0.85
glow.BorderSizePixel = 0
glow.ZIndex = 0
glow.Parent = main

local glowCorner = Instance.new("UICorner", glow)
glowCorner.CornerRadius = UDim.new(0, 18)

-- Inner panel com gradiente
local inner = Instance.new("Frame", main)
inner.Size = UDim2.new(1, -12, 1, -12)
inner.Position = UDim2.new(0, 6, 0, 6)
inner.BackgroundColor3 = Color3.fromRGB(5, 15, 35)
inner.BorderSizePixel = 0

local innerCorner = Instance.new("UICorner", inner)
innerCorner.CornerRadius = UDim.new(0, 10)

local grad = Instance.new("UIGradient", inner)
grad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(15, 45, 95)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(5, 15, 35))
}
grad.Rotation = 330

-- Título
local title = Instance.new("TextLabel", inner)
title.Size = UDim2.new(1, -20, 0, 32)
title.Position = UDim2.new(0, 10, 0, 10)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextXAlignment = Enum.TextXAlignment.Left
title.Text = "Paste Your Private Server Link"
title.TextColor3 = Color3.fromRGB(170, 210, 255)

-- Linha azul
local line = Instance.new("Frame", inner)
line.Size = UDim2.new(0, 140, 0, 3)
line.Position = UDim2.new(0, 10, 0, 42)
line.BackgroundColor3 = Color3.fromRGB(60, 160, 255)
line.BorderSizePixel = 0
local lineCorner = Instance.new("UICorner", line)
lineCorner.CornerRadius = UDim.new(0, 3)

-- TextBox
local box = Instance.new("TextBox", inner)
box.Size = UDim2.new(1, -20, 0, 40)
box.Position = UDim2.new(0, 10, 0, 60)
box.PlaceholderText = "Paste here..."
box.ClearTextOnFocus = false
box.Text = ""
box.Font = Enum.Font.Gotham
box.TextSize = 15
box.TextColor3 = Color3.fromRGB(200, 230, 255)
box.BackgroundColor3 = Color3.fromRGB(10, 25, 50)
box.BorderSizePixel = 0

local boxCorner = Instance.new("UICorner", box)
boxCorner.CornerRadius = UDim.new(0, 8)

-- Botão
local button = Instance.new("TextButton", inner)
button.Size = UDim2.new(0, 170, 0, 36)
button.Position = UDim2.new(1, -180, 1, -46)
button.Text = "Enter Private Server"
button.Font = Enum.Font.GothamSemibold
button.TextSize = 15
button.TextColor3 = Color3.fromRGB(10, 20, 35)
button.BackgroundColor3 = Color3.fromRGB(120, 200, 255)
button.BorderSizePixel = 0

local btnCorner = Instance.new("UICorner", button)
btnCorner.CornerRadius = UDim.new(0, 8)

-- Função do botão: remover a GUI + blur
button.MouseButton1Click:Connect(function()
    fadeOutBlurAndRemoveGui()
end)

-- Permite arrastar a janela
local function dragify(frame)
    local dragging = false
    local dragInput, mousePos, framePos

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            mousePos = Vector2.new(input.Position.X, input.Position.Y)
            framePos = Vector2.new(frame.Position.X.Offset, frame.Position.Y.Offset)
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = Vector2.new(input.Position.X, input.Position.Y) - mousePos
            local newX = framePos.X + delta.X
            local newY = framePos.Y + delta.Y
            frame.Position = UDim2.new(frame.Position.X.Scale, newX, frame.Position.Y.Scale, newY)
        end
    end)
end

dragify(main)

-- Ativa blur com transição
fadeInBlur()
