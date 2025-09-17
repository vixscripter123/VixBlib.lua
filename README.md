--// Biblioteca VixBlib Corrigida
local VixBlib = {}

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

function VixBlib:CreateWindow(config)
    local window = {}
    window.Abas = {}
    local dragging, dragInput, dragStart, startPos
    local primeiraAba = true

    local gui = Instance.new("ScreenGui")
    gui.Name = "VixBlibUI"
    gui.ResetOnSpawn = false
    gui.Parent = PlayerGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 600, 0, 350)
    mainFrame.Position = UDim2.new(0.5, -300, 0.5, -175)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    mainFrame.BorderSizePixel = 0
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    mainFrame.Parent = gui

    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 8)
    mainCorner.Parent = mainFrame

    local topBar = Instance.new("Frame")
    topBar.Size = UDim2.new(1, 0, 0, 30)
    topBar.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
    topBar.BorderSizePixel = 0
    topBar.Parent = mainFrame

    local topCorner = Instance.new("UICorner")
    topCorner.CornerRadius = UDim.new(0, 8)
    topCorner.Parent = topBar

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -60, 1, 0)
    title.Position = UDim2.new(0, 10, 0, 0)
    title.Text = config.Nome or "VixBlib"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.BackgroundTransparency = 1
    title.Font = Enum.Font.GothamBold
    title.TextSize = 14
    title.Parent = topBar

    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -30, 0, 0)
    closeBtn.Text = "×"
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 16
    closeBtn.BackgroundColor3 = Color3.fromRGB(60, 20, 20)
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = topBar

    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeBtn

    closeBtn.MouseButton1Click:Connect(function()
        gui:Destroy()
    end)

    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 30, 0, 30)
    minBtn.Position = UDim2.new(1, -60, 0, 0)
    minBtn.Text = "−"
    minBtn.Font = Enum.Font.GothamBold
    minBtn.TextSize = 16
    minBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 50)
    minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minBtn.BorderSizePixel = 0
    minBtn.Parent = topBar

    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 8)
    minCorner.Parent = minBtn

    local minimized = false
    minBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        local targetSize = minimized and UDim2.new(0, 600, 0, 30) or UDim2.new(0, 600, 0, 350)
        
        local tween = TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = targetSize})
        tween:Play()
        
        for _, child in pairs(mainFrame:GetChildren()) do
            if child ~= topBar then 
                child.Visible = not minimized 
            end
        end
    end)

    -- Frame lateral (abas)
    local abaList = Instance.new("Frame")
    abaList.Size = UDim2.new(0, 120, 1, -30)
    abaList.Position = UDim2.new(0, 0, 0, 30)
    abaList.BackgroundColor3 = Color3.fromRGB(25, 25, 40)
    abaList.BorderSizePixel = 0
    abaList.Parent = mainFrame

    local abaLayout = Instance.new("UIListLayout")
    abaLayout.SortOrder = Enum.SortOrder.LayoutOrder
    abaLayout.Padding = UDim.new(0, 2)
    abaLayout.Parent = abaList

    -- Conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -120, 1, -30)
    contentFrame.Position = UDim2.new(0, 120, 0, 30)
    contentFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
    contentFrame.BorderSizePixel = 0
    contentFrame.Parent = mainFrame

    -- Label do nome da aba selecionada
    local abaTitle = Instance.new("TextLabel")
    abaTitle.Size = UDim2.new(1, -20, 0, 30)
    abaTitle.Position = UDim2.new(0, 10, 0, 0)
    abaTitle.Text = "SELECIONE UMA ABA"
    abaTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    abaTitle.BackgroundTransparency = 1
    abaTitle.Font = Enum.Font.GothamBold
    abaTitle.TextSize = 14
    abaTitle.TextXAlignment = Enum.TextXAlignment.Left
    abaTitle.Parent = contentFrame

    -- Foto do jogador
    local playerImage = Instance.new("ImageLabel")
    playerImage.Size = UDim2.new(0, 50, 0, 50)
    playerImage.Position = UDim2.new(0, 10, 1, -60)
    playerImage.BackgroundTransparency = 1
    playerImage.Image = "https://www.roblox.com/headshot-thumbnail/image?userId="..LocalPlayer.UserId.."&width=100&height=100&format=png"
    playerImage.Parent = abaList

    local imgCorner = Instance.new("UICorner")
    imgCorner.CornerRadius = UDim.new(0, 8)
    imgCorner.Parent = playerImage

    local playerName = Instance.new("TextLabel")
    playerName.Size = UDim2.new(1, -70, 0, 50)
    playerName.Position = UDim2.new(0, 65, 1, -60)
    playerName.Text = LocalPlayer.DisplayName
    playerName.TextColor3 = Color3.fromRGB(200, 200, 200)
    playerName.BackgroundTransparency = 1
    playerName.Font = Enum.Font.Gotham
    playerName.TextSize = 10
    playerName.TextXAlignment = Enum.TextXAlignment.Left
    playerName.TextYAlignment = Enum.TextYAlignment.Center
    playerName.Parent = abaList

    -- Sistema de arrastar
    topBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then 
                    dragging = false 
                end
            end)
        end
    end)

    topBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then 
            dragInput = input 
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    function window:CreateAba(nome)
        local aba = {}
        local elementCount = 0

        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -4, 0, 30)
        btn.Position = UDim2.new(0, 2, 0, 0)
        btn.Text = nome
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 12
        btn.BackgroundColor3 = Color3.fromRGB(35, 35, 55)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.BorderSizePixel = 0
        btn.Parent = abaList

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 4)
        btnCorner.Parent = btn

        local abaFrame = Instance.new("ScrollingFrame")
        abaFrame.Size = UDim2.new(1, 0, 1, -30)
        abaFrame.Position = UDim2.new(0, 0, 0, 30)
        abaFrame.BackgroundTransparency = 1
        abaFrame.BorderSizePixel = 0
        abaFrame.ScrollBarThickness = 4
        abaFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
        abaFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
        abaFrame.Visible = false
        abaFrame.Parent = contentFrame

        local layout = Instance.new("UIListLayout")
        layout.SortOrder = Enum.SortOrder.LayoutOrder
        layout.Padding = UDim.new(0, 8)
        layout.Parent = abaFrame

        layout.Changed:Connect(function()
            abaFrame.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10)
        end)

        btn.MouseButton1Click:Connect(function()
            -- Resetar cor de todas as abas
            for _, other in pairs(window.Abas) do
                other.Button.BackgroundColor3 = Color3.fromRGB(35, 35, 55)
                other.Frame.Visible = false
            end
            -- Ativar aba atual
            btn.BackgroundColor3 = Color3.fromRGB(60, 60, 100)
            abaFrame.Visible = true
            abaTitle.Text = nome
        end)

        -- Se é a primeira aba, ativá-la automaticamente
        if primeiraAba then
            primeiraAba = false
            btn.BackgroundColor3 = Color3.fromRGB(60, 60, 100)
            abaFrame.Visible = true
            abaTitle.Text = nome
        end

        aba.Button = btn
        aba.Frame = abaFrame
        table.insert(window.Abas, aba)

        function aba:CreateButton(txt, callback)
            elementCount = elementCount + 1
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, -20, 0, 35)
            b.Position = UDim2.new(0, 10, 0, 0)
            b.Text = txt
            b.Font = Enum.Font.Gotham
            b.TextSize = 13
            b.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            b.TextColor3 = Color3.fromRGB(255, 255, 255)
            b.BorderSizePixel = 0
            b.Parent = abaFrame
            b.LayoutOrder = elementCount

            local bCorner = Instance.new("UICorner")
            bCorner.CornerRadius = UDim.new(0, 6)
            bCorner.Parent = b
            
            b.MouseButton1Click:Connect(function()
                local tween1 = TweenService:Create(b, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(70, 70, 120)})
                tween1:Play()
                
                spawn(function()
                    wait(0.1)
                    local tween2 = TweenService:Create(b, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(50, 50, 80)})
                    tween2:Play()
                end)
                
                if callback then callback() end
            end)
        end

        function aba:CreateToggle(txt, default, callback)
            elementCount = elementCount + 1
            local toggleFrame = Instance.new("Frame")
            toggleFrame.Size = UDim2.new(1, -20, 0, 35)
            toggleFrame.Position = UDim2.new(0, 10, 0, 0)
            toggleFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 60)
            toggleFrame.BorderSizePixel = 0
            toggleFrame.Parent = abaFrame
            toggleFrame.LayoutOrder = elementCount

            local toggleCorner = Instance.new("UICorner")
            toggleCorner.CornerRadius = UDim.new(0, 6)
            toggleCorner.Parent = toggleFrame

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, -60, 1, 0)
            label.Position = UDim2.new(0, 10, 0, 0)
            label.Text = txt
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.BackgroundTransparency = 1
            label.Font = Enum.Font.Gotham
            label.TextSize = 13
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = toggleFrame

            local toggle = Instance.new("TextButton")
            toggle.Size = UDim2.new(0, 40, 0, 20)
            toggle.Position = UDim2.new(1, -50, 0.5, -10)
            toggle.Text = ""
            toggle.BackgroundColor3 = default and Color3.fromRGB(50, 150, 50) or Color3.fromRGB(80, 80, 80)
            toggle.BorderSizePixel = 0
            toggle.Parent = toggleFrame

            local toggleBtnCorner = Instance.new("UICorner")
            toggleBtnCorner.CornerRadius = UDim.new(0, 10)
            toggleBtnCorner.Parent = toggle

            local circle = Instance.new("Frame")
            circle.Size = UDim2.new(0, 16, 0, 16)
            circle.Position = default and UDim2.new(0, 22, 0, 2) or UDim2.new(0, 2, 0, 2)
            circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            circle.BorderSizePixel = 0
            circle.Parent = toggle

            local circleCorner = Instance.new("UICorner")
            circleCorner.CornerRadius = UDim.new(0, 8)
            circleCorner.Parent = circle

            local toggled = default or false

            toggle.MouseButton1Click:Connect(function()
                toggled = not toggled
                local newPos = toggled and UDim2.new(0, 22, 0, 2) or UDim2.new(0, 2, 0, 2)
                local newColor = toggled and Color3.fromRGB(50, 150, 50) or Color3.fromRGB(80, 80, 80)
                
                local tween1 = TweenService:Create(circle, TweenInfo.new(0.2), {Position = newPos})
                local tween2 = TweenService:Create(toggle, TweenInfo.new(0.2), {BackgroundColor3 = newColor})
                tween1:Play()
                tween2:Play()
                
                if callback then callback(toggled) end
            end)

            return {
                SetValue = function(self, value)
                    toggled = value
                    local newPos = toggled and UDim2.new(0, 22, 0, 2) or UDim2.new(0, 2, 0, 2)
                    local newColor = toggled and Color3.fromRGB(50, 150, 50) or Color3.fromRGB(80, 80, 80)
                    local tween1 = TweenService:Create(circle, TweenInfo.new(0.2), {Position = newPos})
                    local tween2 = TweenService:Create(toggle, TweenInfo.new(0.2), {BackgroundColor3 = newColor})
                    tween1:Play()
                    tween2:Play()
                end
            }
        end

        function aba:CreateSlider(txt, min, max, default, callback)
            elementCount = elementCount + 1
            local sliderFrame = Instance.new("Frame")
            sliderFrame.Size = UDim2.new(1, -20, 0, 50)
            sliderFrame.Position = UDim2.new(0, 10, 0, 0)
            sliderFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 60)
            sliderFrame.BorderSizePixel = 0
            sliderFrame.Parent = abaFrame
            sliderFrame.LayoutOrder = elementCount

            local sliderCorner = Instance.new("UICorner")
            sliderCorner.CornerRadius = UDim.new(0, 6)
            sliderCorner.Parent = sliderFrame

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, -100, 0, 25)
            label.Position = UDim2.new(0, 10, 0, 0)
            label.Text = txt
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.BackgroundTransparency = 1
            label.Font = Enum.Font.Gotham
            label.TextSize = 13
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = sliderFrame

            local valueLabel = Instance.new("TextLabel")
            valueLabel.Size = UDim2.new(0, 80, 0, 25)
            valueLabel.Position = UDim2.new(1, -90, 0, 0)
            valueLabel.Text = tostring(default)
            valueLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
            valueLabel.BackgroundTransparency = 1
            valueLabel.Font = Enum.Font.Gotham
            valueLabel.TextSize = 12
            valueLabel.TextXAlignment = Enum.TextXAlignment.Right
            valueLabel.Parent = sliderFrame

            local sliderBar = Instance.new("Frame")
            sliderBar.Size = UDim2.new(1, -20, 0, 4)
            sliderBar.Position = UDim2.new(0, 10, 1, -15)
            sliderBar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            sliderBar.BorderSizePixel = 0
            sliderBar.Parent = sliderFrame

            local barCorner = Instance.new("UICorner")
            barCorner.CornerRadius = UDim.new(0, 2)
            barCorner.Parent = sliderBar

            local fillBar = Instance.new("Frame")
            fillBar.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
            fillBar.Position = UDim2.new(0, 0, 0, 0)
            fillBar.BackgroundColor3 = Color3.fromRGB(100, 150, 255)
            fillBar.BorderSizePixel = 0
            fillBar.Parent = sliderBar

            local fillCorner = Instance.new("UICorner")
            fillCorner.CornerRadius = UDim.new(0, 2)
            fillCorner.Parent = fillBar

            local slider = Instance.new("TextButton")
            slider.Size = UDim2.new(0, 16, 0, 16)
            slider.Position = UDim2.new((default - min) / (max - min), -8, 0, -6)
            slider.Text = ""
            slider.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            slider.BorderSizePixel = 0
            slider.Parent = sliderBar

            local sliderBtnCorner = Instance.new("UICorner")
            sliderBtnCorner.CornerRadius = UDim.new(0, 8)
            sliderBtnCorner.Parent = slider

            local currentValue = default
            local dragging = false

            slider.InputBegan:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = true
                end
            end)

            UserInputService.InputChanged:Connect(function(input)
                if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                    local mouse = UserInputService:GetMouseLocation()
                    local relativePos = math.clamp((mouse.X - sliderBar.AbsolutePosition.X) / sliderBar.AbsoluteSize.X, 0, 1)
                    currentValue = math.floor(min + (max - min) * relativePos)
                    
                    valueLabel.Text = tostring(currentValue)
                    fillBar.Size = UDim2.new(relativePos, 0, 1, 0)
                    slider.Position = UDim2.new(relativePos, -8, 0, -6)
                    
                    if callback then callback(currentValue) end
                end
            end)

            UserInputService.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = false
                end
            end)

            return {
                SetValue = function(self, value)
                    currentValue = math.clamp(value, min, max)
                    local relativePos = (currentValue - min) / (max - min)
                    valueLabel.Text = tostring(currentValue)
                    fillBar.Size = UDim2.new(relativePos, 0, 1, 0)
                    slider.Position = UDim2.new(relativePos, -8, 0, -6)
                end
            }
        end

        return aba
    end

    return window
end

return VixBlib
