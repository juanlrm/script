--[[
  PURGE Hub para "Simulador de Estrelas de Ginásio" (Gym Stars Simulator)
  Funções: Auto Click (3x por segundo no mouse), Modo AFK, Minimizar Hub (bolinha P), Tecla G para abrir/minimizar
  Para uso educacional.
]]

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PURGE_Hub"
ScreenGui.Parent = game.CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 300, 0, 200)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -100)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = true
MainFrame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "PURGE - Gym Stars Simulator"
Title.TextColor3 = Color3.fromRGB(85, 170, 255)
Title.TextSize = 18
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

local AutoClickBtn = Instance.new("TextButton")
AutoClickBtn.Size = UDim2.new(0.8, 0, 0, 40)
AutoClickBtn.Position = UDim2.new(0.1, 0, 0, 60)
AutoClickBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
AutoClickBtn.Text = "Auto Click [OFF]"
AutoClickBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoClickBtn.TextSize = 16
AutoClickBtn.Font = Enum.Font.Gotham
AutoClickBtn.Parent = MainFrame

local AFKBtn = Instance.new("TextButton")
AFKBtn.Size = UDim2.new(0.8, 0, 0, 40)
AFKBtn.Position = UDim2.new(0.1, 0, 0, 110)
AFKBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
AFKBtn.Text = "Modo AFK [OFF]"
AFKBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AFKBtn.TextSize = 16
AFKBtn.Font = Enum.Font.Gotham
AFKBtn.Parent = MainFrame

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0.2, 0, 0, 25)
CloseBtn.Position = UDim2.new(0.8, 0, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 16
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = MainFrame

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Size = UDim2.new(0.2, 0, 0, 25)
MinimizeBtn.Position = UDim2.new(0.6, 0, 0, 5)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 200)
MinimizeBtn.Text = "_"
MinimizeBtn.TextColor3 = Color3.fromRGB(255,255,255)
MinimizeBtn.TextSize = 18
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Parent = MainFrame

local BallBtn = Instance.new("TextButton")
BallBtn.Name = "P_BallBtn"
BallBtn.Size = UDim2.new(0, 50, 0, 50)
BallBtn.Position = UDim2.new(0, 20, 0.5, -25)
BallBtn.BackgroundColor3 = Color3.fromRGB(25,25,25)
BallBtn.BackgroundTransparency = 0
BallBtn.BorderSizePixel = 0
BallBtn.Visible = false
BallBtn.Parent = ScreenGui
BallBtn.AutoButtonColor = true
BallBtn.Text = ""
local BallUICorner = Instance.new("UICorner")
BallUICorner.CornerRadius = UDim.new(1, 0)
BallUICorner.Parent = BallBtn

local PLabel = Instance.new("TextLabel")
PLabel.Size = UDim2.new(1, 0, 1, 0)
PLabel.BackgroundTransparency = 1
PLabel.Text = "P"
PLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
PLabel.TextSize = 36
PLabel.Font = Enum.Font.GothamBlack
PLabel.Parent = BallBtn

local autoClick = false
local afkMode = false
local autoClickThread
local afkThread

local function startAutoClick()
    local vim = game:GetService("VirtualInputManager")
    autoClickThread = task.spawn(function()
        while autoClick do
            local mouse = game.Players.LocalPlayer:GetMouse()
            local x, y = mouse.X, mouse.Y
            vim:SendMouseButtonEvent(x, y, 0, true, game, 0)
            vim:SendMouseButtonEvent(x, y, 0, false, game, 0)
            task.wait(1/3)
        end
    end)
end

local function stopAutoClick()
    autoClick = false
    if autoClickThread then
        task.cancel(autoClickThread)
        autoClickThread = nil
    end
end

local function startAFK()
    afkThread = task.spawn(function()
        while afkMode do
            local player = game.Players.LocalPlayer
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = player.Character.HumanoidRootPart
                local pos = hrp.Position
                hrp.CFrame = CFrame.new(pos + Vector3.new(0,0,1))
                task.wait(0.2)
                hrp.CFrame = CFrame.new(pos)
            end
            task.wait(45)
        end
    end)
end
local function stopAFK()
    afkMode = false
    if afkThread then
        task.cancel(afkThread)
        afkThread = nil
    end
end

AutoClickBtn.MouseButton1Click:Connect(function()
    autoClick = not autoClick
    AutoClickBtn.Text = "Auto Click ["..(autoClick and "ON" or "OFF").."]"
    if autoClick then
        startAutoClick()
    else
        stopAutoClick()
    end
end)

AFKBtn.MouseButton1Click:Connect(function()
    afkMode = not afkMode
    AFKBtn.Text = "Modo AFK ["..(afkMode and "ON" or "OFF").."]"
    if afkMode then
        startAFK()
    else
        stopAFK()
    end
end)

CloseBtn.MouseButton1Click:Connect(function()
    stopAutoClick()
    stopAFK()
    ScreenGui:Destroy()
end)

MinimizeBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    BallBtn.Visible = true
end)
BallBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
    BallBtn.Visible = false
end)

-- Tecla G para abrir/minimizar
local UIS = game:GetService("UserInputService")
UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.G then
        if MainFrame.Visible then
            MainFrame.Visible = false
            BallBtn.Visible = true
        else
            MainFrame.Visible = true
            BallBtn.Visible = false
        end
    end
end)

-- Arrastar a janela principal
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)
MainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)
UIS.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Arrastar a bolinha flutuante
local ballDragging = false
local ballDragInput, ballDragStart, ballStartPos
BallBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        ballDragging = true
        ballDragStart = input.Position
        ballStartPos = BallBtn.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                ballDragging = false
            end
        end)
    end
end)
BallBtn.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        ballDragInput = input
    end
end)
UIS.InputChanged:Connect(function(input)
    if input == ballDragInput and ballDragging then
        local delta = input.Position - ballDragStart
        BallBtn.Position = UDim2.new(ballStartPos.X.Scale, ballStartPos.X.Offset + delta.X, ballStartPos.Y.Scale, ballStartPos.Y.Offset + delta.Y)
    end
end)
