-- Cria um ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Cria um Frame circular
local circleButton = Instance.new("ImageButton")
circleButton.Size = UDim2.new(0, 100, 0, 100)
circleButton.Position = UDim2.new(0.5, -50, 0.5, -50)
circleButton.Image = "rbxassetid://3570695787" -- ID de uma imagem circular
circleButton.ImageColor3 = Color3.new(1, 0, 0) -- Cor vermelha
circleButton.BackgroundTransparency = 1
circleButton.Parent = screenGui

-- Função para arrastar o círculo
local dragging = false
local dragInput, mousePos, framePos

circleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        mousePos = input.Position
        framePos = circleButton.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

circleButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - mousePos
        circleButton.Position = UDim2.new(
            framePos.X.Scale,
            framePos.X.Offset + delta.X,
            framePos.Y.Scale,
            framePos.Y.Offset + delta.Y
        )
    end
end)

-- Função para ativar/desativar o script
local scriptEnabled = true

circleButton.MouseButton1Click:Connect(function()
    scriptEnabled = not scriptEnabled
    if scriptEnabled then
        -- Ativar o script
        for _, player in pairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer and player.Character then
                createESP(player)
            end
        end
        game.Players.PlayerAdded:Connect(function(player)
            player.CharacterAdded:Connect(function()
                createESP(player)
            end)
        end)
    else
        -- Desativar o script
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                local highlight = player.Character:FindFirstChildOfClass("Highlight")
                if highlight then
                    highlight:Destroy()
                end
            end
        end
    end
end)
