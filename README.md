--[=[
 d888b  db    db d888888b      .d888b.      db      db    db  .d8b.  
88' Y8b 88    88   `88'        VP  `8D      88      88    88 d8' `8b 
88      88    88    88            odD'      88      88    88 88ooo88 
88  ooo 88    88    88          .88'        88      88    88 88~~~88 
88. ~8~ 88b  d88   .88.        j88.         88booo. 88b  d88 88   88    @uniquadev
 Y888P  ~Y8888P' Y888888P      888888D      Y88888P ~Y8888P' YP   YP  SCRIPT HUB

Biblioteca Moderna v3.0 - Focada e Limpa
]=]

local Library = {}

-- Serviços
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local HttpService = game:GetService("HttpService")

-- Tema de Cores
local Theme = {
    Background = Color3.fromRGB(20, 20, 20),
    TopBar = Color3.fromRGB(30, 30, 30),
    TabBar = Color3.fromRGB(25, 25, 25),
    Element = Color3.fromRGB(35, 35, 35),
    Accent = Color3.fromRGB(100, 120, 255),
    Text = Color3.fromRGB(255, 255, 255),
    TextDark = Color3.fromRGB(180, 180, 180),
    Success = Color3.fromRGB(76, 175, 80),
    Error = Color3.fromRGB(244, 67, 54),
}

-- Utilitários
local function MakeDraggable(frame, dragFrame)
    local dragging, dragInput, dragStart, startPos
    
    dragFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    dragFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
end

local function CreateCorner(parent, radius)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, radius or 8)
    corner.Parent = parent
    return corner
end

-- Sistema de Key
function Library:CreateKeySystem(config)
    config = config or {}
    config.Title = config.Title or "Key System"
    config.Subtitle = config.Subtitle or "Digite a key para continuar"
    config.Note = config.Note or "Obtenha a key em nosso Discord"
    config.Key = config.Key or "DefaultKey123"
    config.SaveKey = config.SaveKey or true
    config.FileName = config.FileName or "ScriptHubKey"
    
    local KeySystem = {}
    local validKey = false
    
    -- Verificar key salva
    if config.SaveKey then
        local success, savedKey = pcall(function()
            return readfile(config.FileName .. ".txt")
        end)
        
        if success and savedKey == config.Key then
            validKey = true
            if config.Callback then
                config.Callback(true)
            end
            return KeySystem
        end
    end
    
    -- Criar GUI do Key System
    local KeyGui = Instance.new("ScreenGui")
    KeyGui.Name = "KeySystem"
    KeyGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    if syn and syn.protect_gui then
        syn.protect_gui(KeyGui)
        KeyGui.Parent = CoreGui
    elseif gethui then
        KeyGui.Parent = gethui()
    else
        KeyGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    local KeyFrame = Instance.new("Frame")
    KeyFrame.Name = "KeyFrame"
    KeyFrame.BackgroundColor3 = Theme.Background
    KeyFrame.Size = UDim2.new(0, 400, 0, 250)
    KeyFrame.Position = UDim2.new(0.5, -200, 0.5, -125)
    KeyFrame.Parent = KeyGui
    CreateCorner(KeyFrame, 12)
    
    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Theme.Accent
    Stroke.Thickness = 2
    Stroke.Parent = KeyFrame
    
    -- Header
    local Header = Instance.new("Frame")
    Header.BackgroundColor3 = Theme.TopBar
    Header.Size = UDim2.new(1, 0, 0, 50)
    Header.Parent = KeyFrame
    CreateCorner(Header, 12)
    
    local Title = Instance.new("TextLabel")
    Title.Text = config.Title
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.TextColor3 = Theme.Text
    Title.BackgroundTransparency = 1
    Title.Size = UDim2.new(1, 0, 1, 0)
    Title.Parent = Header
    
    -- Subtitle
    local Subtitle = Instance.new("TextLabel")
    Subtitle.Text = config.Subtitle
    Subtitle.Font = Enum.Font.Gotham
    Subtitle.TextSize = 14
    Subtitle.TextColor3 = Theme.TextDark
    Subtitle.BackgroundTransparency = 1
    Subtitle.Position = UDim2.new(0, 0, 0, 60)
    Subtitle.Size = UDim2.new(1, 0, 0, 30)
    Subtitle.Parent = KeyFrame
    
    -- Input Box
    local InputBox = Instance.new("TextBox")
    InputBox.PlaceholderText = "Digite a key aqui..."
    InputBox.Text = ""
    InputBox.Font = Enum.Font.Gotham
    InputBox.TextSize = 14
    InputBox.TextColor3 = Theme.Text
    InputBox.BackgroundColor3 = Theme.Element
    InputBox.Position = UDim2.new(0.1, 0, 0, 100)
    InputBox.Size = UDim2.new(0.8, 0, 0, 40)
    InputBox.Parent = KeyFrame
    CreateCorner(InputBox, 8)
    
    -- Check Button
    local CheckButton = Instance.new("TextButton")
    CheckButton.Text = "Verificar Key"
    CheckButton.Font = Enum.Font.GothamBold
    CheckButton.TextSize = 14
    CheckButton.TextColor3 = Theme.Text
    CheckButton.BackgroundColor3 = Theme.Accent
    CheckButton.Position = UDim2.new(0.1, 0, 0, 150)
    CheckButton.Size = UDim2.new(0.35, 0, 0, 35)
    CheckButton.Parent = KeyFrame
    CreateCorner(CheckButton, 8)
    
    -- Get Key Button
    local GetKeyButton = Instance.new("TextButton")
    GetKeyButton.Text = "Obter Key"
    GetKeyButton.Font = Enum.Font.GothamBold
    GetKeyButton.TextSize = 14
    GetKeyButton.TextColor3 = Theme.Text
    GetKeyButton.BackgroundColor3 = Theme.Element
    GetKeyButton.Position = UDim2.new(0.55, 0, 0, 150)
    GetKeyButton.Size = UDim2.new(0.35, 0, 0, 35)
    GetKeyButton.Parent = KeyFrame
    CreateCorner(GetKeyButton, 8)
    
    -- Note
    local Note = Instance.new("TextLabel")
    Note.Text = config.Note
    Note.Font = Enum.Font.Gotham
    Note.TextSize = 12
    Note.TextColor3 = Theme.TextDark
    Note.BackgroundTransparency = 1
    Note.Position = UDim2.new(0, 0, 0, 200)
    Note.Size = UDim2.new(1, 0, 0, 30)
    Note.Parent = KeyFrame
    
    -- Status Label
    local Status = Instance.new("TextLabel")
    Status.Text = ""
    Status.Font = Enum.Font.GothamBold
    Status.TextSize = 12
    Status.TextColor3 = Theme.Error
    Status.BackgroundTransparency = 1
    Status.Position = UDim2.new(0, 0, 0, 225)
    Status.Size = UDim2.new(1, 0, 0, 20)
    Status.Parent = KeyFrame
    
    MakeDraggable(KeyFrame, Header)
    
    CheckButton.MouseButton1Click:Connect(function()
        local inputKey = InputBox.Text
        
        if inputKey == config.Key then
            Status.Text = "✓ Key Válida!"
            Status.TextColor3 = Theme.Success
            validKey = true
            
            if config.SaveKey then
                pcall(function()
                    writefile(config.FileName .. ".txt", inputKey)
                end)
            end
            
            task.wait(1)
            KeyGui:Destroy()
            
            if config.Callback then
                config.Callback(true)
            end
        else
            Status.Text = "✗ Key Inválida!"
            Status.TextColor3 = Theme.Error
            
            -- Shake animation
            local original = KeyFrame.Position
            for i = 1, 3 do
                KeyFrame.Position = original + UDim2.new(0, 10, 0, 0)
                task.wait(0.05)
                KeyFrame.Position = original - UDim2.new(0, 10, 0, 0)
                task.wait(0.05)
            end
            KeyFrame.Position = original
        end
    end)
    
    GetKeyButton.MouseButton1Click:Connect(function()
        if config.KeyLink then
            setclipboard(config.KeyLink)
            Status.Text = "✓ Link copiado!"
            Status.TextColor3 = Theme.Success
        else
            Status.Text = "Link não disponível"
            Status.TextColor3 = Theme.TextDark
        end
    end)
    
    return KeySystem
end

-- Criar Window
function Library:CreateWindow(config)
    config = config or {}
    config.Name = config.Name or "Script Hub"
    config.HidePremium = config.HidePremium or false
    
    local Window = {
        Tabs = {},
        CurrentTab = nil
    }
    
    -- ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "ScriptHub_" .. math.random(1000, 9999)
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.ResetOnSpawn = false
    
    if syn and syn.protect_gui then
        syn.protect_gui(ScreenGui)
        ScreenGui.Parent = CoreGui
    elseif gethui then
        ScreenGui.Parent = gethui()
    else
        ScreenGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    Window.ScreenGui = ScreenGui
    
    -- Main Frame
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.BackgroundColor3 = Theme.Background
    MainFrame.Size = UDim2.new(0, 600, 0, 450)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -225)
    MainFrame.Parent = ScreenGui
    CreateCorner(MainFrame, 12)
    
    local MainStroke = Instance.new("UIStroke")
    MainStroke.Color = Theme.Accent
    MainStroke.Thickness = 2
    MainStroke.Parent = MainFrame
    
    -- Top Bar
    local TopBar = Instance.new("Frame")
    TopBar.Name = "TopBar"
    TopBar.BackgroundColor3 = Theme.TopBar
    TopBar.Size = UDim2.new(1, 0, 0, 45)
    TopBar.Parent = MainFrame
    CreateCorner(TopBar, 12)
    
    local Title = Instance.new("TextLabel")
    Title.Text = config.Name
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 16
    Title.TextColor3 = Theme.Text
    Title.BackgroundTransparency = 1
    Title.Position = UDim2.new(0, 15, 0, 0)
    Title.Size = UDim2.new(0.6, 0, 1, 0)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = TopBar
    
    -- Minimize Button
    local MinimizeBtn = Instance.new("TextButton")
    MinimizeBtn.Text = "−"
    MinimizeBtn.Font = Enum.Font.GothamBold
    MinimizeBtn.TextSize = 20
    MinimizeBtn.TextColor3 = Theme.Text
    MinimizeBtn.BackgroundColor3 = Theme.Element
    MinimizeBtn.Size = UDim2.new(0, 35, 0, 30)
    MinimizeBtn.Position = UDim2.new(1, -80, 0.5, -15)
    MinimizeBtn.Parent = TopBar
    CreateCorner(MinimizeBtn, 6)
    
    -- Close Button
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Text = "✕"
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.TextSize = 16
    CloseBtn.TextColor3 = Theme.Text
    CloseBtn.BackgroundColor3 = Theme.Error
    CloseBtn.Size = UDim2.new(0, 35, 0, 30)
    CloseBtn.Position = UDim2.new(1, -40, 0.5, -15)
    CloseBtn.Parent = TopBar
    CreateCorner(CloseBtn, 6)
    
    -- Tab Bar
    local TabBar = Instance.new("Frame")
    TabBar.Name = "TabBar"
    TabBar.BackgroundColor3 = Theme.TabBar
    TabBar.Size = UDim2.new(0, 150, 1, -55)
    TabBar.Position = UDim2.new(0, 5, 0, 50)
    TabBar.Parent = MainFrame
    CreateCorner(TabBar, 8)
    
    local TabLayout = Instance.new("UIListLayout")
    TabLayout.Padding = UDim.new(0, 5)
    TabLayout.Parent = TabBar
    
    local TabPadding = Instance.new("UIPadding")
    TabPadding.PaddingTop = UDim.new(0, 5)
    TabPadding.PaddingLeft = UDim.new(0, 5)
    TabPadding.PaddingRight = UDim.new(0, 5)
    TabPadding.Parent = TabBar
    
    -- Content Container
    local ContentContainer = Instance.new("Frame")
    ContentContainer.Name = "Content"
    ContentContainer.BackgroundTransparency = 1
    ContentContainer.Size = UDim2.new(1, -165, 1, -55)
    ContentContainer.Position = UDim2.new(0, 160, 0, 50)
    ContentContainer.Parent = MainFrame
    
    MakeDraggable(MainFrame, TopBar)
    
    -- Events
    local minimized = false
    MinimizeBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        local targetSize = minimized and UDim2.new(0, 600, 0, 45) or UDim2.new(0, 600, 0, 450)
        
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {
            Size = targetSize
        }):Play()
        
        TabBar.Visible = not minimized
        ContentContainer.Visible = not minimized
    end)
    
    CloseBtn.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)
    
    -- Create Tab
    function Window:CreateTab(config)
        config = config or {}
        config.Name = config.Name or "Tab"
        
        local Tab = {
            Elements = {}
        }
        
        -- Tab Button
        local TabButton = Instance.new("TextButton")
        TabButton.Name = config.Name
        TabButton.Text = config.Name
        TabButton.Font = Enum.Font.GothamBold
        TabButton.TextSize = 13
        TabButton.TextColor3 = Theme.Text
        TabButton.BackgroundColor3 = Theme.Element
        TabButton.Size = UDim2.new(1, 0, 0, 35)
        TabButton.TextXAlignment = Enum.TextXAlignment.Center
        TabButton.Parent = TabBar
        CreateCorner(TabButton, 6)
        
        -- Tab Content
        local TabContent = Instance.new("ScrollingFrame")
        TabContent.Name = config.Name .. "_Content"
        TabContent.BackgroundTransparency = 1
        TabContent.BorderSizePixel = 0
        TabContent.Size = UDim2.new(1, 0, 1, 0)
        TabContent.ScrollBarThickness = 4
        TabContent.ScrollBarImageColor3 = Theme.Accent
        TabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        TabContent.Visible = false
        TabContent.Parent = ContentContainer
        
        local ContentLayout = Instance.new("UIListLayout")
        ContentLayout.Padding = UDim.new(0, 8)
        ContentLayout.Parent = TabContent
        
        local ContentPadding = Instance.new("UIPadding")
        ContentPadding.PaddingTop = UDim.new(0, 5)
        ContentPadding.PaddingLeft = UDim.new(0, 5)
        ContentPadding.PaddingRight = UDim.new(0, 5)
        ContentPadding.Parent = TabContent
        
        ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            TabContent.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 10)
        end)
        
        TabButton.MouseButton1Click:Connect(function()
            for _, tab in pairs(Window.Tabs) do
                tab.Button.BackgroundColor3 = Theme.Element
                tab.Content.Visible = false
            end
            
            TabButton.BackgroundColor3 = Theme.Accent
            TabContent.Visible = true
            Window.CurrentTab = Tab
        end)
        
        Tab.Button = TabButton
        Tab.Content = TabContent
        table.insert(Window.Tabs, Tab)
        
        if #Window.Tabs == 1 then
            TabButton.BackgroundColor3 = Theme.Accent
            TabContent.Visible = true
            Window.CurrentTab = Tab
        end
        
        -- Create Button
        function Tab:CreateButton(config)
            config = config or {}
            config.Name = config.Name or "Button"
            config.Callback = config.Callback or function() end
            
            local ButtonFrame = Instance.new("Frame")
            ButtonFrame.BackgroundColor3 = Theme.Element
            ButtonFrame.Size = UDim2.new(1, -10, 0, 40)
            ButtonFrame.Parent = TabContent
            CreateCorner(ButtonFrame, 8)
            
            local Button = Instance.new("TextButton")
            Button.Text = config.Name
            Button.Font = Enum.Font.GothamBold
            Button.TextSize = 13
            Button.TextColor3 = Theme.Text
            Button.BackgroundColor3 = Theme.Accent
            Button.Size = UDim2.new(1, -10, 1, -10)
            Button.Position = UDim2.new(0, 5, 0, 5)
            Button.Parent = ButtonFrame
            CreateCorner(Button, 6)
            
            Button.MouseButton1Click:Connect(function()
                pcall(config.Callback)
            end)
            
            return Button
        end
        
        -- Create Toggle
        function Tab:CreateToggle(config)
            config = config or {}
            config.Name = config.Name or "Toggle"
            config.CurrentValue = config.CurrentValue or false
            config.Callback = config.Callback or function(v) end
            
            local ToggleFrame = Instance.new("Frame")
            ToggleFrame.BackgroundColor3 = Theme.Element
            ToggleFrame.Size = UDim2.new(1, -10, 0, 40)
            ToggleFrame.Parent = TabContent
            CreateCorner(ToggleFrame, 8)
            
            local Label = Instance.new("TextLabel")
            Label.Text = config.Name
            Label.Font = Enum.Font.Gotham
            Label.TextSize = 13
            Label.TextColor3 = Theme.Text
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 10, 0, 0)
            Label.Size = UDim2.new(0.7, 0, 1, 0)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.Parent = ToggleFrame
            
            local ToggleButton = Instance.new("TextButton")
            ToggleButton.Text = ""
            ToggleButton.BackgroundColor3 = config.CurrentValue and Theme.Success or Theme.Error
            ToggleButton.Size = UDim2.new(0, 50, 0, 24)
            ToggleButton.Position = UDim2.new(1, -60, 0.5, -12)
            ToggleButton.Parent = ToggleFrame
            CreateCorner(ToggleButton, 12)
            
            local Indicator = Instance.new("Frame")
            Indicator.BackgroundColor3 = Theme.Text
            Indicator.Size = UDim2.new(0, 20, 0, 20)
            Indicator.Position = config.CurrentValue and UDim2.new(1, -22, 0.5, -10) or UDim2.new(0, 2, 0.5, -10)
            Indicator.Parent = ToggleButton
            CreateCorner(Indicator, 10)
            
            local toggled = config.CurrentValue
            
            ToggleButton.MouseButton1Click:Connect(function()
                toggled = not toggled
                
                TweenService:Create(Indicator, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
                    Position = toggled and UDim2.new(1, -22, 0.5, -10) or UDim2.new(0, 2, 0.5, -10)
                }):Play()
                
                TweenService:Create(ToggleButton, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
                    BackgroundColor3 = toggled and Theme.Success or Theme.Error
                }):Play()
                
                pcall(config.Callback, toggled)
            end)
            
            return {
                Set = function(value)
                    toggled = value
                    Indicator.Position = toggled and UDim2.new(1, -22, 0.5, -10) or UDim2.new(0, 2, 0.5, -10)
                    ToggleButton.BackgroundColor3 = toggled and Theme.Success or Theme.Error
                end
            }
        end
        
        -- Create Slider
        function Tab:CreateSlider(config)
            config = config or {}
            config.Name = config.Name or "Slider"
            config.Range = config.Range or {0, 100}
            config.Increment = config.Increment or 1
            config.CurrentValue = config.CurrentValue or config.Range[1]
            config.Callback = config.Callback or function(v) end
            
            local SliderFrame = Instance.new("Frame")
            SliderFrame.BackgroundColor3 = Theme.Element
            SliderFrame.Size = UDim2.new(1, -10, 0, 55)
            SliderFrame.Parent = TabContent
            CreateCorner(SliderFrame, 8)
            
            local Label = Instance.new("TextLabel")
            Label.Text = config.Name
            Label.Font = Enum.Font.Gotham
            Label.TextSize = 13
            Label.TextColor3 = Theme.Text
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 10, 0, 5)
            Label.Size = UDim2.new(0.6, 0, 0, 20)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.Parent = SliderFrame
            
            local ValueLabel = Instance.new("TextLabel")
            ValueLabel.Text = tostring(config.CurrentValue)
            ValueLabel.Font = Enum.Font.GothamBold
            ValueLabel.TextSize = 13
            ValueLabel.TextColor3 = Theme.Accent
            ValueLabel.BackgroundTransparency = 1
            ValueLabel.Position = UDim2.new(0.7, 0, 0, 5)
            ValueLabel.Size = UDim2.new(0.25, 0, 0, 20)
            ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
            ValueLabel.Parent = SliderFrame
            
            local SliderBack = Instance.new("Frame")
            SliderBack.BackgroundColor3 = Theme.Background
            SliderBack.Position = UDim2.new(0, 10, 1, -18)
            SliderBack.Size = UDim2.new(1, -20, 0, 6)
            SliderBack.Parent = SliderFrame
            CreateCorner(SliderBack, 3)
            
            local SliderFill = Instance.new("Frame")
            SliderFill.BackgroundColor3 = Theme.Accent
            SliderFill.Size = UDim2.new(0, 0, 1, 0)
            SliderFill.Parent = SliderBack
            CreateCorner(SliderFill, 3)
            
            local function UpdateSlider(input)
                local sizeX = math.clamp((input.Position.X - SliderBack.AbsolutePosition.X) / SliderBack.AbsoluteSize.X, 0, 1)
                local value = math.floor(config.Range[1] + (config.Range[2] - config.Range[1]) * sizeX)
                value = math.floor(value / config.Increment + 0.5) * config.Increment
                value = math.clamp(value, config.Range[1], config.Range[2])
                
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
            
            local initialPercent = (config.CurrentValue - config.Range[1]) / (config.Range[2] - config.Range[1])
            SliderFill.Size = UDim2.new(initialPercent, 0, 1, 0)
            
            return {
                Set = function(value)
                    ValueLabel.Text = tostring(value)
                    local percent = (value - config.Range[1]) / (config.Range[2] - config.Range[1])
                    SliderFill.Size = UDim2.new(percent, 0, 1, 0)
                end
            }
        end
        
        -- Create Input
        function Tab:CreateInput(config)
            config = config or {}
            config.Name = config.Name or "Input"
            config.PlaceholderText = config.PlaceholderText or "Digite aqui..."
            config.RemoveTextAfterFocusLost = config.RemoveTextAfterFocusLost or false
            config.Callback = config.Callback or function(v) end
            
            local InputFrame = Instance.new("Frame")
            InputFrame.BackgroundColor3 = Theme.Element
            InputFrame.Size = UDim2.new(1, -10, 0, 65)
            InputFrame.Parent = TabContent
            CreateCorner(InputFrame, 8)
            
            local Label = Instance.new("TextLabel")
            Label.Text = config.Name
            Label.Font = Enum.Font.Gotham
            Label.TextSize = 13
            Label.TextColor3 = Theme.Text
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 10, 0, 5)
            Label.Size = UDim2.new(1, -20, 0, 20)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.Parent = InputFrame
            
            local Input = Instance.new("TextBox")
            Input.PlaceholderText = config.PlaceholderText
            Input.Text = ""
            Input.Font = Enum.Font.Gotham
            Input.TextSize = 13
            Input.TextColor3 = Theme.Text
            Input.BackgroundColor3 = Theme.Background
            Input.Position = UDim2.new(0, 10, 0, 30)
            Input.Size = UDim2.new(1, -20, 0, 30)
            Input.TextXAlignment = Enum.TextXAlignment.Left
            Input.Parent = InputFrame
            CreateCorner(Input, 6)
            
            local InputPadding = Instance.new("UIPadding")
            InputPadding.PaddingLeft = UDim.new(0, 8)
            InputPadding.Parent = Input
            
            Input.FocusLost:Connect(function(enterPressed)
                if enterPressed then
                    pcall(config.Callback, Input.Text)
                    if config.RemoveTextAfterFocusLost then
                        Input.Text = ""
                    end
                end
            end)
            
            return {
                Set = function(text)
                    Input.Text = text
                end
            }
        end
        
        -- Create Dropdown
        function Tab:CreateDropdown(config)
            config = config or {}
            config.Name = config.Name or "Dropdown"
            config.Options = config.Options or {"Opção 1", "Opção 2"}
            config.CurrentOption = config.CurrentOption or config.Options[1]
            config.Callback = config.Callback or function(v) end
            
            local DropdownFrame = Instance.new("Frame")
            DropdownFrame.BackgroundColor3 = Theme.Element
            DropdownFrame.Size = UDim2.new(1, -10, 0, 40)
            DropdownFrame.Parent = TabContent
            CreateCorner(DropdownFrame, 8)
            
            local Label = Instance.new("TextLabel")
            Label.Text = config.Name
            Label.Font = Enum.Font.Gotham
            Label.TextSize = 13
            Label.TextColor3 = Theme.Text
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 10, 0, 0)
            Label.Size = UDim2.new(0.5, 0, 1, 0)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.Parent = DropdownFrame
            
            local DropButton = Instance.new("TextButton")
            DropButton.Text = config.CurrentOption .. " ▼"
            DropButton.Font = Enum.Font.Gotham
            DropButton.TextSize = 12
            DropButton.TextColor3 = Theme.Text
            DropButton.BackgroundColor3 = Theme.Background
            DropButton.Position = UDim2.new(0.5, 5, 0.5, -15)
            DropButton.Size = UDim2.new(0.45, -10, 0, 30)
            DropButton.Parent = DropdownFrame
            CreateCorner(DropButton, 6)
            
            local OptionsList = Instance.new("Frame")
            OptionsList.BackgroundColor3 = Theme.Element
            OptionsList.Position = UDim2.new(0, 0, 1, 5)
            OptionsList.Size = UDim2.new(1, 0, 0, 0)
            OptionsList.Visible = false
            OptionsList.Parent = DropdownFrame
            CreateCorner(OptionsList, 8)
            
            local OptionsLayout = Instance.new("UIListLayout")
            OptionsLayout.Padding = UDim.new(0, 2)
            OptionsLayout.Parent = OptionsList
            
            local OptionsPadding = Instance.new("UIPadding")
            OptionsPadding.PaddingTop = UDim.new(0, 3)
            OptionsPadding.PaddingBottom = UDim.new(0, 3)
            OptionsPadding.Parent = OptionsList
            
            local currentOption = config.CurrentOption
            local expanded = false
            
            for _, option in ipairs(config.Options) do
                local OptionButton = Instance.new("TextButton")
                OptionButton.Text = option
                OptionButton.Font = Enum.Font.Gotham
                OptionButton.TextSize = 12
                OptionButton.TextColor3 = Theme.Text
                OptionButton.BackgroundColor3 = Theme.Background
                OptionButton.Size = UDim2.new(1, 0, 0, 30)
                OptionButton.Parent = OptionsList
                CreateCorner(OptionButton, 6)
                
                OptionButton.MouseButton1Click:Connect(function()
                    currentOption = option
                    DropButton.Text = option .. " ▼"
                    expanded = false
                    OptionsList.Visible = false
                    DropdownFrame.Size = UDim2.new(1, -10, 0, 40)
                    pcall(config.Callback, option)
                end)
            end
            
            OptionsLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
                OptionsList.Size = UDim2.new(1, 0, 0, OptionsLayout.AbsoluteContentSize.Y + 6)
            end)
            
            DropButton.MouseButton1Click:Connect(function()
                expanded = not expanded
                OptionsList.Visible = expanded
                
                if expanded then
                    DropdownFrame.Size = UDim2.new(1, -10, 0, 40 + OptionsList.AbsoluteSize.Y + 5)
                else
                    DropdownFrame.Size = UDim2.new(1, -10, 0, 40)
                end
            end)
            
            return {
                Set = function(option)
                    currentOption = option
                    DropButton.Text = option .. " ▼"
                end
            }
        end
        
        -- Create Label
        function Tab:CreateLabel(text)
            local LabelFrame = Instance.new("Frame")
            LabelFrame.BackgroundColor3 = Theme.Element
            LabelFrame.Size = UDim2.new(1, -10, 0, 35)
            LabelFrame.Parent = TabContent
            CreateCorner(LabelFrame, 8)
            
            local Label = Instance.new("TextLabel")
            Label.Text = text or "Label"
            Label.Font = Enum.Font.Gotham
            Label.TextSize = 13
            Label.TextColor3 = Theme.Text
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 10, 0, 0)
            Label.Size = UDim2.new(1, -20, 1, 0)
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.TextWrapped = true
            Label.Parent = LabelFrame
            
            return {
                Set = function(newText)
                    Label.Text = newText
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
    config.Content = config.Content or "Conteúdo"
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
    NotifFrame.BackgroundColor3 = Theme.Element
    NotifFrame.Size = UDim2.new(0, 300, 0, 80)
    NotifFrame.Position = UDim2.new(1, 10, 1, -90)
    NotifFrame.Parent = NotifGui
    CreateCorner(NotifFrame, 10)
    
    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Theme.Accent
    Stroke.Thickness = 2
    Stroke.Parent = NotifFrame
    
    local Title = Instance.new("TextLabel")
    Title.Text = config.Title
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 14
    Title.TextColor3 = Theme.Text
    Title.BackgroundTransparency = 1
    Title.Position = UDim2.new(0, 10, 0, 5)
    Title.Size = UDim2.new(1, -20, 0, 25)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = NotifFrame
    
    local Content = Instance.new("TextLabel")
    Content.Text = config.Content
    Content.Font = Enum.Font.Gotham
    Content.TextSize = 12
    Content.TextColor3 = Theme.TextDark
    Content.BackgroundTransparency = 1
    Content.Position = UDim2.new(0, 10, 0, 30)
    Content.Size = UDim2.new(1, -20, 1, -35)
    Content.TextXAlignment = Enum.TextXAlignment.Left
    Content.TextYAlignment = Enum.TextYAlignment.Top
    Content.TextWrapped = true
    Content.Parent = NotifFrame
    
    TweenService:Create(NotifFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -310, 1, -90)
    }):Play()
    
    task.wait(config.Duration)
    
    TweenService:Create(NotifFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, 10, 1, -90)
    }):Play()
    
    task.wait(0.5)
    NotifGui:Destroy()
end

return Library
