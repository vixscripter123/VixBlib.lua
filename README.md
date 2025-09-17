--// Biblioteca VixBlib
local VixBlib = {}

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

function VixBlib:CreateWindow(config)
    local window = {}
    window.Abas = {}
    local dragging, dragInput, dragStart, startPos

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

    Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 8)

    local topBar = Instance.new("Frame")
    topBar.Size = UDim2.new(1, 0, 0, 30)
    topBar.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
    topBar.BorderSizePixel = 0
    topBar.Parent = mainFrame

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
    closeBtn.Text = "X"
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 14
    closeBtn.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.Parent = topBar
    closeBtn.MouseButton1Click:Connect(function() gui:Destroy() end)

    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 30, 0, 30)
    minBtn.Position = UDim2.new(1, -60, 0, 0)
    minBtn.Text = "-"
    minBtn.Font = Enum.Font.GothamBold
    minBtn.TextSize = 14
    minBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 40)
    minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minBtn.Parent = topBar

    local minimized = false
    minBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            for _, child in pairs(mainFrame:GetChildren()) do
                if child ~= topBar then child.Visible = false end
            end
            mainFrame.Size = UDim2.new(0, 600, 0, 30)
        else
            for _, child in pairs(mainFrame:GetChildren()) do
                child.Visible = true
            end
            mainFrame.Size = UDim2.new(0, 600, 0, 350)
        end
    end)

    -- Frame lateral (abas)
    local abaList = Instance.new("Frame")
    abaList.Size = UDim2.new(0, 120, 1, -30)
    abaList.Position = UDim2.new(0, 0, 0, 30)
    abaList.BackgroundColor3 = Color3.fromRGB(25, 25, 40)
    abaList.BorderSizePixel = 0
    abaList.Parent = mainFrame
    Instance.new("UIListLayout", abaList).SortOrder = Enum.SortOrder.LayoutOrder

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
    abaTitle.Text = ""
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

    -- Arrastar
    local UserInputService = game:GetService("UserInputService")
    topBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    topBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
                                           startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    function window:CreateAba(nome)
        local aba = {}

        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, 0, 0, 30)
        btn.Text = nome
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 14
        btn.BackgroundColor3 = Color3.fromRGB(35, 35, 55)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Parent = abaList

        local abaFrame = Instance.new("Frame")
        abaFrame.Size = UDim2.new(1, 0, 1, -30)
        abaFrame.Position = UDim2.new(0, 0, 0, 30)
        abaFrame.BackgroundTransparency = 1
        abaFrame.Visible = false
        abaFrame.Parent = contentFrame

        btn.MouseButton1Click:Connect(function()
            for _, other in pairs(window.Abas) do
                other.Frame.Visible = false
            end
            abaFrame.Visible = true
            abaTitle.Text = nome
        end)

        aba.Frame = abaFrame
        table.insert(window.Abas, aba)

        function aba:CreateButton(txt, callback)
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(0, 200, 0, 30)
            b.Position = UDim2.new(0, 10, 0, #abaFrame:GetChildren() * 35)
            b.Text = txt
            b.Font = Enum.Font.Gotham
            b.TextSize = 14
            b.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            b.TextColor3 = Color3.fromRGB(255, 255, 255)
            b.Parent = abaFrame
            b.MouseButton1Click:Connect(function()
                if callback then callback() end
            end)
        end

        return aba
    end

    return window
end

return VixBlib
