--[=[
 d888b  db    db d888888b      .d888b.      db      db    db  .d8b.  
88' Y8b 88    88   `88'        VP  `8D      88      88    88 d8' `8b 
88      88    88    88            odD'      88      88    88 88ooo88 
88  ooo 88    88    88          .88'        88      88    88 88~~~88 
88. ~8~ 88b  d88   .88.        j88.         88booo. 88b  d88 88   88    @uniquadev
 Y888P  ~Y8888P' Y888888P      888888D      Y88888P ~Y8888P' YP   YP  SCRIPT HUB

Biblioteca estilo Rayfield - v2.0
]=]

local ScriptHub = {}
ScriptHub.__index = ScriptHub

-- Serviços
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

-- Configurações de cores
local Theme = {
    Primary = Color3.fromRGB(24, 42, 93),
    Secondary = Color3.fromRGB(0, 0, 0),
    Accent = Color3.fromRGB(80, 124, 157),
    Text = Color3.fromRGB(255, 255, 255),
    Success = Color3.fromRGB(76, 175, 80),
    Error = Color3.fromRGB(244, 67, 54),
    Warning = Color3.fromRGB(255, 193, 7),
    Info = Color3.fromRGB(33, 150, 243),
    ScrollBar = Color3.fromRGB(191, 241, 255)
}

-- Biblioteca Principal
local Library = {}

function Library:CreateWindow(config)
    local Window = {
        Tabs = {},
        CurrentTab = nil,
        Minimized = false
    }
    
    config = config or {}
    config.Name = config.Name or "Script Hub"
    config.LoadingTitle = config.LoadingTitle or "Script Hub"
    config.LoadingSubtitle = config.LoadingSubtitle or "by @uniquadev"
    config.ConfigurationSaving = config.ConfigurationSaving or {Enabled = false}
    config.Discord = config.Discord or {Enabled = false}
    config.KeySystem = config.KeySystem or false
    
    -- Criar ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "ScriptHub_" .. math.random(1000, 9999)
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.ResetOnSpawn = false
    
    -- Proteção contra exclusão
    if syn and syn.protect_gui then
        syn.protect_gui(ScreenGui)
        ScreenGui.Parent = CoreGui
    elseif gethui then
        ScreenGui.Parent = gethui()
    else
        ScreenGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    Window.ScreenGui = ScreenGui
    
    -- Background principal
    local Background = Instance.new("Frame")
    Background.Name = "Background"
    Background.BorderSizePixel = 0
    Background.BackgroundColor3 = Theme.Secondary
    Background.Size = UDim2.new(0, 550, 0, 400)
    Background.Position = UDim2.new(0.5, -275, 0.5, -200)
    Background.Parent = ScreenGui
    
    local bgCorner = Instance.new("UICorner")
    bgCorner.CornerRadius = UDim.new(0, 16)
    bgCorner.Parent = Background
    
    local bgStroke = Instance.new("UIStroke")
    bgStroke.Color = Theme.Primary
    bgStroke.Thickness = 2
    bgStroke.Parent = Background
    
    Window.Background = Background
    
    -- Header
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.BorderSizePixel = 0
    Header.BackgroundColor3 = Theme.Primary
    Header.Size = UDim2.new(1, 0, 0, 40)
    Header.Parent = Background
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 16)
    headerCorner.Parent = Header
    
    -- Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Text = config.Name
    Title.Font = Enum.Font.GothamBold
    Title.TextColor3 = Theme.Text
    Title.TextSize = 16
    Title.BackgroundTransparency = 1
    Title.Size = UDim2.new(1, -100, 1, 0)
    Title.Position = UDim2.new(0, 15, 0, 0)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    -- Botões de controle
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "Close"
    CloseButton.Text = "✕"
    CloseButton.Font = Enum.Font.GothamBold
    CloseButton.TextColor3 = Theme.Text
    CloseButton.TextSize = 14
    CloseButton.BackgroundColor3 = Theme.Error
    CloseButton.Size = UDim2.new(0, 30, 0, 30)
    CloseButton.Position = UDim2.new(1, -35, 0.5, -15)
    CloseButton.Parent = Header
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = CloseButton
    
    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Name = "Minimize"
    MinimizeButton.Text = "−"
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.TextColor3 = Theme.Text
    MinimizeButton.TextSize = 14
    MinimizeButton.BackgroundColor3 = Theme.Warning
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -70, 0.5, -15)
    MinimizeButton.Parent = Header
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 8)
    minCorner.Parent = MinimizeButton
    
    -- Container de Tabs
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.BackgroundColor3 = Theme.Primary
    TabContainer.BorderSizePixel = 0
    TabContainer.Size = UDim2.new(0, 140, 1, -50)
    TabContainer.Position = UDim2.new(0, 5, 0, 45)
    TabContainer.Parent = Background
    
    local tabCorner = Instance.new("UICorner")
    tabCorner.CornerRadius = UDim.new(0, 12)
    tabCorner.Parent = TabContainer
    
    local TabLayout = Instance.new("UIListLayout")
    TabLayout.Padding = UDim.new(0, 5)
    TabLayout.SortOrder = Enum.SortOrder.LayoutOrder
    TabLayout.Parent = TabContainer
    
    local TabPadding = Instance.new("UIPadding")
    TabPadding.PaddingTop = UDim.new(0, 5)
    TabPadding.PaddingLeft = UDim.new(0, 5)
    TabPadding.PaddingRight = UDim.new(0, 5)
    TabPadding.Parent = TabContainer
    
    Window.TabContainer = TabContainer
    
    -- Container de conteúdo
    local ContentContainer = Instance.new("Frame")
    ContentContainer.Name = "ContentContainer"
    ContentContainer.BackgroundTransparency = 1
    ContentContainer.Size = UDim2.new(1, -155, 1, -50)
    ContentContainer.Position = UDim2.new(0, 150, 0, 45)
    ContentContainer.Parent = Background
    
    Window.ContentContainer = ContentContainer
    
    -- Sistema de arrastar
    local dragging = false
    local dragInput
    local dragStart
    local startPos
    
    local function update(input)
        local delta = input.Position - dragStart
        Background.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
    
    Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = Background.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    Header.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
    
    -- Eventos dos botões
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)
    
    MinimizeButton.MouseButton1Click:Connect(function()
        Window.Minimized = not Window.Minimized
        local targetSize = Window.Minimized and UDim2.new(0, 550, 0, 40) or UDim2.new(0, 550, 0, 400)
        
        TweenService:Create(Background, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
            Size = targetSize
        }):Play()
        
        TabContainer.Visible = not Window.Minimized
        ContentContainer.Visible = not Window.Minimized
    end)
    
    -- Função para criar Tab
    function Window:CreateTab(config)
        config = config or {}
        config.Name = config.Name or "Tab"
        config.Icon = config.Icon or "rbxassetid://4483345998"
        
        local Tab = {
            Elements = {},
            Button = nil,
            Content = nil,
            Active = false
        }
        
        -- Botão da Tab
        local TabButton = Instance.new("TextButton")
        TabButton.Name = config.Name
        TabButton.Text = "  " .. config.Name
        TabButton.Font = Enum.Font.Gotham
        TabButton.TextColor3 = Theme.Text
        TabButton.TextSize = 13
        TabButton.TextXAlignment = Enum.TextXAlignment.Left
        TabButton.BackgroundColor3 = Theme.Accent
        TabButton.Size = UDim2.new(1, 0, 0, 35)
        TabButton.Parent = TabContainer
        
        local tabBtnCorner = Instance.new("UICorner")
        tabBtnCorner.CornerRadius = UDim.new(0, 8)
        tabBtnCorner.Parent = TabButton
        
        Tab.Button = TabButton
        
        -- Conteúdo da Tab
        local TabContent = Instance.new("ScrollingFrame")
        TabContent.Name = config.Name .. "_Content"
        TabContent.BackgroundTransparency = 1
        TabContent.BorderSizePixel = 0
        TabContent.Size = UDim2.new(1, 0, 1, 0)
        TabContent.ScrollBarThickness = 4
        TabContent.ScrollBarImageColor3 = Theme.ScrollBar
        TabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        TabContent.Visible = false
        TabContent.Parent = ContentContainer
        
        local ContentLayout = Instance.new("UIListLayout")
        ContentLayout.Padding = UDim.new(0, 8)
        ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ContentLayout.Parent = TabContent
        
        local ContentPadding = Instance.new("UIPadding")
        ContentPadding.PaddingTop = UDim.new(0, 5)
        ContentPadding.PaddingLeft = UDim.new(0, 5)
        ContentPadding.PaddingRight = UDim.new(0, 5)
        ContentPadding.Parent = TabContent
        
        Tab.Content = TabContent
        
        -- Atualizar tamanho do canvas
        ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            TabContent.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 10)
        end)
        
        -- Evento de clique
        TabButton.MouseButton1Click:Connect(function()
            for _, tab in pairs(Window.Tabs) do
                tab.Content.Visible = false
                tab.Button.BackgroundColor3 = Theme.Accent
                tab.Active = false
            end
            
            TabContent.Visible = true
            TabButton.BackgroundColor3 = Theme.Primary
            Tab.Active = true
            Window.CurrentTab = Tab
        end)
        
        -- Adicionar à lista
        table.insert(Window.Tabs, Tab)
        
        -- Ativar primeira tab
        if #Window.Tabs == 1 then
            TabButton.BackgroundColor3 = Theme.Primary
            TabContent.Visible = true
            Tab.Active = true
            Window.CurrentTab = Tab
        end
        
        -- Função para criar Button
        function Tab:CreateButton(config)
            config = config or {}
            config.Name = config.Name or "Button"
            config.Callback = config.Callback or function() end
            
            local ButtonFrame = Instance.new("Frame")
            ButtonFrame.BackgroundColor3 = Theme.Primary
            ButtonFrame.Size = UDim2.new(1, -10, 0, 40)
            ButtonFrame.Parent = TabContent
            
            local btnCorner = Instance.new("UICorner")
            btnCorner.CornerRadius = UDim.new(0, 10)
            btnCorner.Parent = ButtonFrame
            
            local Button = Instance.new("TextButton")
            Button.Name = config.Name
            Button.Text = config.Name
            Button.Font = Enum.Font.GothamBold
            Button.TextColor3 = Theme.Text
            Button.TextSize = 13
            Button.BackgroundColor3 = Theme.Success
            Button.Size = UDim2.new(1, -10, 1, -10)
            Button.Position = UDim2.new(0, 5, 0, 5)
            Button.Parent = ButtonFrame
            
            local btnInnerCorner = Instance.new("UICorner")
            btnInnerCorner.CornerRadius = UDim.new(0, 8)
            btnInnerCorner.Parent = Button
            
            Button.MouseButton1Click:Connect(function()
                pcall(config.Callback)
            end)
            
            return Button
        end
        
        -- Função para criar Toggle
        function Tab:CreateToggle(config)
            config = config or {}
            config.Name = config.Name or "Toggle"
            config.CurrentValue = config.CurrentValue or false
            config.Callback = config.Callback or function(value) end
            
            local ToggleFrame = Instance.new("Frame")
            ToggleFrame.BackgroundColor3 = Theme.Primary
            ToggleFrame.Size = UDim2.new(1, -10, 0, 40)
            ToggleFrame.Parent = TabContent
            
            local toggleCorner = Instance.new("UICorner")
            toggleCorner.CornerRadius = UDim.new(0, 10)
            toggleCorner.Parent = ToggleFrame
            
            local ToggleLabel = Instance.new("TextLabel")
            ToggleLabel.Text = config.Name
            ToggleLabel.Font = Enum.Font.Gotham
            ToggleLabel.TextColor3 = Theme.Text
            ToggleLabel.TextSize = 13
            ToggleLabel.BackgroundTransparency = 1
            ToggleLabel.Size = UDim2.new(0.7, 0, 1, 0)
            ToggleLabel.Position = UDim2.new(0, 10, 0, 0)
            ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
            ToggleLabel.Parent = ToggleFrame
            
            local ToggleButton = Instance.new("TextButton")
            ToggleButton.Text = ""
            ToggleButton.BackgroundColor3 = config.CurrentValue and Theme.Success or Theme.Error
            ToggleButton.Size = UDim2.new(0, 50, 0, 25)
            ToggleButton.Position = UDim2.new(1, -60, 0.5, -12.5)
            ToggleButton.Parent = ToggleFrame
            
            local toggleBtnCorner = Instance.new("UICorner")
            toggleBtnCorner.CornerRadius = UDim.new(1, 0)
            toggleBtnCorner.Parent = ToggleButton
            
            local ToggleIndicator = Instance.new("Frame")
            ToggleIndicator.BackgroundColor3 = Theme.Text
            ToggleIndicator.Size = UDim2.new(0, 21, 0, 21)
            ToggleIndicator.Position = config.CurrentValue and UDim2.new(1, -23, 0.5, -10.5) or UDim2.new(0, 2, 0.5, -10.5)
            ToggleIndicator.Parent = ToggleButton
            
            local indicatorCorner = Instance.new("UICorner")
            indicatorCorner.CornerRadius = UDim.new(1, 0)
            indicatorCorner.Parent = ToggleIndicator
            
            local toggled = config.CurrentValue
            
            ToggleButton.MouseButton1Click:Connect(function()
                toggled = not toggled
                
                TweenService:Create(ToggleIndicator, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
                    Position = toggled and UDim2.new(1, -23, 0.5, -10.5) or UDim2.new(0, 2, 0.5, -10.5)
                }):Play()
                
                TweenService:Create(ToggleButton, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
                    BackgroundColor3 = toggled and Theme.Success or Theme.Error
                }):Play()
                
                pcall(config.Callback, toggled)
            end)
            
            return {
                Set = function(value)
                    toggled = value
                    ToggleIndicator.Position = toggled and UDim2.new(1, -23, 0.5, -10.5) or UDim2.new(0, 2, 0.5, -10.5)
                    ToggleButton.BackgroundColor3 = toggled and Theme.Success or Theme.Error
                end
            }
        end
        
        -- Função para criar Slider
        function Tab:CreateSlider(config)
            config = config or {}
            config.Name = config.Name or "Slider"
            config.Range = config.Range or {0, 100}
            config.Increment = config.Increment or 1
            config.CurrentValue = config.CurrentValue or config.Range[1]
            config.Callback = config.Callback or function(value) end
            
            local SliderFrame = Instance.new("Frame")
            SliderFrame.BackgroundColor3 = Theme.Primary
            SliderFrame.Size = UDim2.new(1, -10, 0, 55)
            SliderFrame.Parent = TabContent
            
            local sliderCorner = Instance.new("UICorner")
            sliderCorner.CornerRadius = UDim.new(0, 10)
            sliderCorner.Parent = SliderFrame
            
            local SliderLabel = Instance.new("TextLabel")
            SliderLabel.Text = config.Name
            SliderLabel.Font = Enum.Font.Gotham
            SliderLabel.TextColor3 = Theme.Text
            SliderLabel.TextSize = 13
            SliderLabel.BackgroundTransparency = 1
            SliderLabel.Size = UDim2.new(0.6, 0, 0, 20)
            SliderLabel.Position = UDim2.new(0, 10, 0, 5)
            SliderLabel.TextXAlignment = Enum.TextXAlignment.Left
            SliderLabel.Parent = SliderFrame
            
            local ValueLabel = Instance.new("TextLabel")
            ValueLabel.Text = tostring(config.CurrentValue)
            ValueLabel.Font = Enum.Font.GothamBold
            ValueLabel.TextColor3 = Theme.Text
            ValueLabel.TextSize = 13
            ValueLabel.BackgroundTransparency = 1
            ValueLabel.Size = UDim2.new(0.3, 0, 0, 20)
            ValueLabel.Position = UDim2.new(0.7, 0, 0, 5)
            ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
            ValueLabel.Parent = SliderFrame
            
            local SliderBack = Instance.new("Frame")
            SliderBack.BackgroundColor3 = Theme.Secondary
            SliderBack.Size = UDim2.new(1, -20, 0, 6)
            SliderBack.Position = UDim2.new(0, 10, 1, -15)
            SliderBack.Parent = SliderFrame
            
            local sliderBackCorner = Instance.new("UICorner")
            sliderBackCorner.CornerRadius = UDim.new(1, 0)
            sliderBackCorner.Parent = SliderBack
            
            local SliderFill = Instance.new("Frame")
            SliderFill.BackgroundColor3 = Theme.Success
            SliderFill.Size = UDim2.new(0, 0, 1, 0)
            SliderFill.Parent = SliderBack
            
            local sliderFillCorner = Instance.new("UICorner")
            sliderFillCorner.CornerRadius = UDim.new(1, 0)
            sliderFillCorner.Parent = SliderFill
            
            local currentValue = config.CurrentValue
            
            local function UpdateSlider(input)
                local sizeX = math.clamp((input.Position.X - SliderBack.AbsolutePosition.X) / SliderBack.AbsoluteSize.X, 0, 1)
                local value = math.floor(config.Range[1] + (config.Range[2] - config.Range[1]) * sizeX)
                value = math.floor(value / config.Increment + 0.5) * config.Increment
                
                currentValue = value
                ValueLabel.Text = tostring(value)
                SliderFill.Size = UDim2.new(sizeX, 0, 1, 0)
                
                pcall(config.Callback, value)
            end
            
            local dragging = false
            
            SliderBack.InputBegan:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = true
                    UpdateSlider(input)
                end
            end)
            
            SliderBack.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = false
                end
            end)
            
            UserInputService.InputChanged:Connect(function(input)
                if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                    UpdateSlider(input)
                end
            end)
            
            -- Inicializar valor
            local initialPercent = (config.CurrentValue - config.Range[1]) / (config.Range[2] - config.Range[1])
            SliderFill.Size = UDim2.new(initialPercent, 0, 1, 0)
            
            return {
                Set = function(value)
                    currentValue = value
                    ValueLabel.Text = tostring(value)
                    local percent = (value - config.Range[1]) / (config.Range[2] - config.Range[1])
                    SliderFill.Size = UDim2.new(percent, 0, 1, 0)
                end
            }
        end
        
        -- Função para criar Label
        function Tab:CreateLabel(text)
            local LabelFrame = Instance.new("Frame")
            LabelFrame.BackgroundColor3 = Theme.Primary
            LabelFrame.Size = UDim2.new(1, -10, 0, 35)
            LabelFrame.Parent = TabContent
            
            local labelCorner = Instance.new("UICorner")
            labelCorner.CornerRadius = UDim.new(0, 10)
            labelCorner.Parent = LabelFrame
            
            local Label = Instance.new("TextLabel")
            Label.Text = text or "Label"
            Label.Font = Enum.Font.Gotham
            Label.TextColor3 = Theme.Text
            Label.TextSize = 13
            Label.BackgroundTransparency = 1
            Label.Size = UDim2.new(1, -10, 1, 0)
            Label.Position = UDim2.new(0, 10, 0, 0)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.TextWrapped = true
            Label.Parent = LabelFrame
            
            return {
                Set = function(newText)
                    Label.Text = newText
                end
            }
        end
        
        -- Função para criar Paragraph
        function Tab:CreateParagraph(config)
            config = config or {}
            config.Title = config.Title or "Paragraph"
            config.Content = config.Content or "Content"
            
            local ParagraphFrame = Instance.new("Frame")
            ParagraphFrame.BackgroundColor3 = Theme.Primary
            ParagraphFrame.Size = UDim2.new(1, -10, 0, 70)
            ParagraphFrame.Parent = TabContent
            
            local paraCorner = Instance.new("UICorner")
            paraCorner.CornerRadius = UDim.new(0, 10)
            paraCorner.Parent = ParagraphFrame
            
            local TitleLabel = Instance.new("TextLabel")
            TitleLabel.Text = config.Title
            TitleLabel.Font = Enum.Font.GothamBold
            TitleLabel.TextColor3 = Theme.Text
            TitleLabel.TextSize = 14
            TitleLabel.BackgroundTransparency = 1
            TitleLabel.Size = UDim2.new(1, -10, 0, 20)
            TitleLabel.Position = UDim2.new(0, 10, 0, 5)
            TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
            TitleLabel.Parent = ParagraphFrame
            
            local ContentLabel = Instance.new("TextLabel")
            ContentLabel.Text = config.Content
            ContentLabel.Font = Enum.Font.Gotham
            ContentLabel.TextColor3 = Theme.Text
            ContentLabel.TextSize = 12
            ContentLabel.BackgroundTransparency = 1
            ContentLabel.Size = UDim2.new(1, -10, 1, -30)
            ContentLabel.Position = UDim2.new(0, 10, 0, 25)
            ContentLabel.TextXAlignment = Enum.TextXAlignment.Left
            ContentLabel.TextYAlignment = Enum.TextYAlignment.Top
            ContentLabel.TextWrapped = true
            ContentLabel.Parent = ParagraphFrame
            
            return {
                Set = function(newConfig)
                    TitleLabel.Text = newConfig.Title or config.Title
                    ContentLabel.Text = newConfig.Content or config.Content
                end
            }
        end
        
        return Tab
    end
    
    return Window
end

-- Notificações
function Library:Notify(config)
    config = config or {}
    config.Title = config.Title or "Notificação"
    config.Content = config.Content or "Conteúdo da notificação"
    config.Duration = config.Duration or 3
    config.Image = config.Image or nil
    
    local NotifGui = Instance.new("ScreenGui")
    NotifGui.Name = "Notification"
    NotifGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    if syn and syn.protect_gui then
        syn.protect_gui(NotifGui)
        NotifGui.Parent = CoreGui
    elseif gethui then
        NotifGui.Parent = gethui()
    else
        NotifGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    local NotifFrame = Instance.new("Frame")
    NotifFrame.BackgroundColor3 = Theme.Primary
    NotifFrame.Size = UDim2.new(0, 300, 0, 80)
    NotifFrame.Position = UDim2.new(1, 10, 1, -90)
    NotifFrame.Parent = NotifGui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 12)
    notifCorner.Parent = NotifFrame
    
    local notifStroke = Instance.new("UIStroke")
    notifStroke.Color = Theme.Accent
    notifStroke.Thickness = 2
    notifStroke.Parent = NotifFrame
    
    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Text = config.Title
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.TextColor3 = Theme.Text
    TitleLabel.TextSize = 14
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Size = UDim2.new(1, -10, 0, 25)
    TitleLabel.Position = UDim2.new(0, 10, 0, 5)
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.Parent = NotifFrame
    
    local ContentLabel = Instance.new("TextLabel")
    ContentLabel.Text = config.Content
    ContentLabel.Font = Enum.Font.Gotham
    ContentLabel.TextColor3 = Theme.Text
    ContentLabel.TextSize = 12
    ContentLabel.BackgroundTransparency = 1
    ContentLabel.Size = UDim2.new(1, -10, 1, -30)
    ContentLabel.Position = UDim2.new(0, 10, 0, 25)
    ContentLabel.TextXAlignment = Enum.TextXAlignment.Left
    ContentLabel.TextYAlignment = Enum.TextYAlignment.Top
    ContentLabel.TextWrapped = true
    ContentLabel.Parent = NotifFrame
    
    -- Animação de entrada
    TweenService:Create(NotifFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -310, 1, -90)
    }):Play()
    
    -- Animação de saída
    task.wait(config.Duration)
    
    TweenService:Create(NotifFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, 10, 1, -90)
    }):Play()
    
    task.wait(0.5)
    NotifGui:Destroy()
end

return Library
