--// Biblioteca VixBlib
local VixBlib = {}

-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Criar Janela Principal
function VixBlib:CreateWindow(config)
    local window = {}
    window.Tabs = {}
    local dragging, dragInput, dragStart, startPos

    -- ScreenGui
    local gui = Instance.new("ScreenGui")
    gui.Name = "VixBlibUI"
    gui.ResetOnSpawn = false
    gui.Parent = PlayerGui

    -- Frame principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 500, 0, 300)
    mainFrame.Position = UDim2.new(0.5, -250, 0.5, -150)
    mainFrame.BackgroundColor3 = Color3.fromRGB(15, 20, 40)
    mainFrame.BackgroundTransparency = 0.15 -- 15% transparente
    mainFrame.BorderSizePixel = 0
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    mainFrame.Parent = gui

    -- UICorner para deixar arredondado
    local corner = Instance.new("UICorner", mainFrame)
    corner.CornerRadius = UDim.new(0, 12)

    -- Barra Superior (para mover)
    local topBar = Instance.new("Frame")
    topBar.Size = UDim2.new(1, 0, 0, 30)
    topBar.BackgroundColor3 = Color3.fromRGB(10, 15, 25)
    topBar.BorderSizePixel = 0
    topBar.Parent = mainFrame

    local topCorner = Instance.new("UICorner", topBar)
    topCorner.CornerRadius = UDim.new(0, 12)

    -- Título
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -60, 1, 0)
    title.Position = UDim2.new(0, 10, 0, 0)
    title.Text = config.Nome or "VixBlib Window"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.BackgroundTransparency = 1
    title.Font = Enum.Font.GothamBold
    title.TextSize = 14
    title.Parent = topBar

    -- Botão minimizar/maximizar
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(0, 30, 0, 30)
    toggleBtn.Position = UDim2.new(1, -35, 0, 0)
    toggleBtn.Text = "-"
    toggleBtn.Font = Enum.Font.GothamBold
    toggleBtn.TextSize = 20
    toggleBtn.BackgroundColor3 = Color3.fromRGB(20, 25, 40)
    toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggleBtn.Parent = topBar

    -- Conteúdo das abas
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -100, 1, -30)
    contentFrame.Position = UDim2.new(0, 100, 0, 30)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = mainFrame

    -- Lista de abas (lado esquerdo)
    local tabList = Instance.new("Frame")
    tabList.Size = UDim2.new(0, 100, 1, -30)
    tabList.Position = UDim2.new(0, 0, 0, 30)
    tabList.BackgroundColor3 = Color3.fromRGB(20, 25, 45)
    tabList.BorderSizePixel = 0
    tabList.Parent = mainFrame

    local tabLayout = Instance.new("UIListLayout", tabList)
    tabLayout.SortOrder = Enum.SortOrder.LayoutOrder

    -- Função de arrastar a janela
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

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
                                           startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    -- Minimizar/maximizar
    local minimized = false
    toggleBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            contentFrame.Visible = false
            tabList.Visible = false
            mainFrame.Size = UDim2.new(0, 500, 0, 30)
            toggleBtn.Text = "+"
        else
            contentFrame.Visible = true
            tabList.Visible = true
            mainFrame.Size = UDim2.new(0, 500, 0, 300)
            toggleBtn.Text = "-"
        end
    end)

    -- Criar uma aba
    function window:CreateTab(nome)
        local tab = {}
        local tabBtn = Instance.new("TextButton")
        tabBtn.Size = UDim2.new(1, 0, 0, 30)
        tabBtn.Text = nome
        tabBtn.Font = Enum.Font.GothamBold
        tabBtn.TextSize = 14
        tabBtn.BackgroundColor3 = Color3.fromRGB(25, 30, 55)
        tabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        tabBtn.Parent = tabList

        local tabFrame = Instance.new("Frame")
        tabFrame.Size = UDim2.new(1, 0, 1, 0)
        tabFrame.BackgroundTransparency = 1
        tabFrame.Visible = false
        tabFrame.Parent = contentFrame

        -- Ativar aba
        tabBtn.MouseButton1Click:Connect(function()
            for _, otherTab in pairs(window.Tabs) do
                otherTab.Frame.Visible = false
            end
            tabFrame.Visible = true
        end)

        tab.Frame = tabFrame
        table.insert(window.Tabs, tab)

        -- Botões básicos
        function tab:CreateButton(texto, callback)
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(0, 200, 0, 40)
            btn.Position = UDim2.new(0, 10, 0, #tabFrame:GetChildren() * 45)
            btn.Text = texto
            btn.Font = Enum.Font.Gotham
            btn.TextSize = 14
            btn.BackgroundColor3 = Color3.fromRGB(40, 50, 80)
            btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            btn.Parent = tabFrame

            btn.MouseButton1Click:Connect(function()
                if callback then callback() end
            end)
        end

        return tab
    end

    -- Ativar a primeira aba automaticamente
    game:GetService("RunService").Heartbeat:Wait()
    if window.Tabs[1] then
        window.Tabs[1].Frame.Visible = true
    end

    return window
end

return VixBlib
