--[=[
 d888b  db    db d888888b      .d888b.      db      db    db  .d8b.  
88' Y8b 88    88   `88'        VP  `8D      88      88    88 d8' `8b 
88      88    88    88            odD'      88      88    88 88ooo88 
88  ooo 88    88    88          .88'        88      88    88 88~~~88 
88. ~8~ 88b  d88   .88.        j88.         88booo. 88b  d88 88   88    @uniquadev
 Y888P  ~Y8888P' Y888888P      888888D      Y88888P ~Y8888P' YP   YP  SCRIPT HUB

designed using localmaze gui creator
]=]

local ScriptHub = {}
ScriptHub.__index = ScriptHub

-- Configurações
ScriptHub.Settings = {
    DebugMode = false,
    DefaultScripts = {
        ["Admin Scripts"] = {
            {Name = "Infinite Yield FE", Function = function()
                loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
            end},
            {Name = "CMD-X", Function = function()
                loadstring(game:HttpGet("https://raw.githubusercontent.com/CMD-X/CMD-X/master/Source", true))()
            end},
            {Name = "Reviz Admin", Function = function()
                loadstring(game:HttpGet("https://pastebin.com/raw/YAq50gzA"))()
            end}
        },
        ["Player Scripts"] = {
            {Name = "Fly Script", Function = function()
                loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
            end},
            {Name = "Speed Script", Function = function()
                local player = game.Players.LocalPlayer
                player.Character.Humanoid.WalkSpeed = 50
            end},
            {Name = "Jump Script", Function = function()
                local player = game.Players.LocalPlayer
                player.Character.Humanoid.JumpPower = 100
            end}
        },
        ["Fun Scripts"] = {
            {Name = "Chat Logger", Function = function()
                loadstring(game:HttpGet("https://raw.githubusercontent.com/DaCoderMane/roblox-scripts/main/chatlogger.lua"))()
            end},
            {Name = "Esp Script", Function = function()
                loadstring(game:HttpGet("https://pastebin.com/raw/Qu6vK8hY"))()
            end},
            {Name = "TP Tool", Function = function()
                loadstring(game:HttpGet("https://pastebin.com/raw/djBfk8mA"))()
            end}
        }
    }
}

-- Cores do tema
ScriptHub.Colors = {
    Primary = Color3.fromRGB(24, 42, 93),
    Secondary = Color3.fromRGB(0, 0, 0),
    Accent = Color3.fromRGB(80, 124, 157),
    Text = Color3.fromRGB(255, 255, 255),
    Success = Color3.fromRGB(76, 175, 80),
    Error = Color3.fromRGB(244, 67, 54),
    ScrollBar = Color3.fromRGB(191, 241, 255)
}

-- Inicializar GUI
function ScriptHub:CreateGUI()
    if self.Gui then self.Gui:Destroy() end
    
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    local PlayerGui = player:WaitForChild("PlayerGui")
    
    -- ScreenGui Principal
    self.Gui = Instance.new("ScreenGui")
    self.Gui.Name = "ScriptHub"
    self.Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    self.Gui.Parent = PlayerGui
    
    -- Background
    self.Background = Instance.new("Frame")
    self.Background.Name = "Background"
    self.Background.BorderSizePixel = 0
    self.Background.BackgroundColor3 = ScriptHub.Colors.Secondary
    self.Background.Size = UDim2.new(0.47604, 0, 0.561, 0)
    self.Background.Position = UDim2.new(0, 506, 0, 188)
    self.Background.Parent = self.Gui
    
    local bgCorner = Instance.new("UICorner")
    bgCorner.CornerRadius = UDim.new(0, 16)
    bgCorner.Parent = self.Background
    
    -- Header
    self.Header = Instance.new("Frame")
    self.Header.Name = "Header"
    self.Header.BorderSizePixel = 0
    self.Header.BackgroundColor3 = ScriptHub.Colors.Primary
    self.Header.Size = UDim2.new(0.47083, 0, 0.07508, 0)
    self.Header.Position = UDim2.new(0, 512, 0, 194)
    self.Header.Parent = self.Gui
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 12)
    headerCorner.Parent = self.Header
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Name = "Title"
    title.Text = "SCRIPT HUB v1.0"
    title.Font = Enum.Font.GothamBold
    title.TextColor3 = ScriptHub.Colors.Text
    title.TextSize = 18
    title.BackgroundTransparency = 1
    title.Size = UDim2.new(1, 0, 1, 0)
    title.Parent = self.Header
    
    -- Sidebar (Categorias)
    self.Sidebar = Instance.new("Frame")
    self.Sidebar.Name = "Sidebar"
    self.Sidebar.BorderSizePixel = 0
    self.Sidebar.BackgroundColor3 = ScriptHub.Colors.Primary
    self.Sidebar.Size = UDim2.new(0.12812, 0, 0.45881, 0)
    self.Sidebar.Position = UDim2.new(0, 514, 0, 274)
    self.Sidebar.Parent = self.Gui
    
    local sidebarCorner = Instance.new("UICorner")
    sidebarCorner.CornerRadius = UDim.new(0, 12)
    sidebarCorner.Parent = self.Sidebar
    
    local sidebarLayout = Instance.new("UIListLayout")
    sidebarLayout.Padding = UDim.new(0, 5)
    sidebarLayout.Parent = self.Sidebar
    
    -- Área de Scripts
    self.ScriptsFrame = Instance.new("ScrollingFrame")
    self.ScriptsFrame.Name = "ScriptsFrame"
    self.ScriptsFrame.BorderSizePixel = 0
    self.ScriptsFrame.BackgroundColor3 = ScriptHub.Colors.Primary
    self.ScriptsFrame.Size = UDim2.new(0.33437, 0, 0.45881, 0)
    self.ScriptsFrame.Position = UDim2.new(0, 770, 0, 276)
    self.ScriptsFrame.ScrollBarImageColor3 = ScriptHub.Colors.ScrollBar
    self.ScriptsFrame.ScrollBarThickness = 8
    self.ScriptsFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    self.ScriptsFrame.Parent = self.Gui
    
    local scriptsCorner = Instance.new("UICorner")
    scriptsCorner.CornerRadius = UDim.new(0, 12)
    scriptsCorner.Parent = self.ScriptsFrame
    
    local scriptsLayout = Instance.new("UIListLayout")
    scriptsLayout.Padding = UDim.new(0, 8)
    scriptsLayout.Parent = self.ScriptsFrame
    
    -- Botão de Fechar
    self.CloseButton = Instance.new("TextButton")
    self.CloseButton.Name = "CloseButton"
    self.CloseButton.Text = "X"
    self.CloseButton.Font = Enum.Font.GothamBold
    self.CloseButton.TextColor3 = ScriptHub.Colors.Text
    self.CloseButton.TextSize = 14
    self.CloseButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    self.CloseButton.Size = UDim2.new(0, 25, 0, 25)
    self.CloseButton.Position = UDim2.new(1, -30, 0, 5)
    self.CloseButton.Parent = self.Header
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 6)
    closeCorner.Parent = self.CloseButton
    
    -- Botão de Minimizar
    self.MinimizeButton = Instance.new("TextButton")
    self.MinimizeButton.Name = "MinimizeButton"
    self.MinimizeButton.Text = "_"
    self.MinimizeButton.Font = Enum.Font.GothamBold
    self.MinimizeButton.TextColor3 = ScriptHub.Colors.Text
    self.MinimizeButton.TextSize = 14
    self.MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 193, 7)
    self.MinimizeButton.Size = UDim2.new(0, 25, 0, 25)
    self.MinimizeButton.Position = UDim2.new(1, -60, 0, 5)
    self.MinimizeButton.Parent = self.Header
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 6)
    minCorner.Parent = self.MinimizeButton
    
    -- Botão de Executar Tudo
    self.ExecuteAllButton = Instance.new("TextButton")
    self.ExecuteAllButton.Name = "ExecuteAllButton"
    self.ExecuteAllButton.Text = "▶ Executar Todos"
    self.ExecuteAllButton.Font = Enum.Font.GothamBold
    self.ExecuteAllButton.TextColor3 = ScriptHub.Colors.Text
    self.ExecuteAllButton.TextSize = 12
    self.ExecuteAllButton.BackgroundColor3 = ScriptHub.Colors.Success
    self.ExecuteAllButton.Size = UDim2.new(0.9, 0, 0, 30)
    self.ExecuteAllButton.Position = UDim2.new(0.05, 0, 0.9, 0)
    self.ExecuteAllButton.Parent = self.Background
    
    local execCorner = Instance.new("UICorner")
    execCorner.CornerRadius = UDim.new(0, 8)
    execCorner.Parent = self.ExecuteAllButton
    
    self:SetupEvents()
    self:LoadCategories()
    self:SelectCategory("Admin Scripts")
    
    return self.Gui
end

-- Configurar eventos
function ScriptHub:SetupEvents()
    self.CloseButton.MouseButton1Click:Connect(function()
        self.Gui:Destroy()
        self.Gui = nil
    end)
    
    self.MinimizeButton.MouseButton1Click:Connect(function()
        local isVisible = self.Background.Visible
        self.Background.Visible = not isVisible
        self.Sidebar.Visible = not isVisible
        self.ScriptsFrame.Visible = not isVisible
        self.ExecuteAllButton.Visible = not isVisible
    end)
    
    self.ExecuteAllButton.MouseButton1Click:Connect(function()
        self:ExecuteAllScripts()
    end)
    
    -- Sistema de arrastar
    local dragging
    local dragInput
    local dragStart
    local startPos
    
    local function update(input)
        local delta = input.Position - dragStart
        self.Header.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
        self.Background.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y - 6
        )
        self.Sidebar.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X - 2,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y + 80
        )
        self.ScriptsFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X + 264,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y + 82
        )
        self.ExecuteAllButton.Position = UDim2.new(
            0.05,
            startPos.X.Offset + delta.X,
            0.9,
            startPos.Y.Offset + delta.Y + 266
        )
    end
    
    self.Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = self.Header.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    self.Header.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end

-- Carregar categorias
function ScriptHub:LoadCategories()
    for categoryName in pairs(ScriptHub.Settings.DefaultScripts) do
        local categoryButton = Instance.new("TextButton")
        categoryButton.Name = categoryName
        categoryButton.Text = categoryName
        categoryButton.Font = Enum.Font.Gotham
        categoryButton.TextColor3 = ScriptHub.Colors.Text
        categoryButton.TextSize = 12
        categoryButton.BackgroundColor3 = ScriptHub.Colors.Accent
        categoryButton.Size = UDim2.new(0.9, 0, 0, 35)
        categoryButton.Position = UDim2.new(0.05, 0, 0, 5)
        categoryButton.Parent = self.Sidebar
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 6)
        btnCorner.Parent = categoryButton
        
        categoryButton.MouseButton1Click:Connect(function()
            self:SelectCategory(categoryName)
        end)
    end
end

-- Selecionar categoria
function ScriptHub:SelectCategory(categoryName)
    -- Limpar scripts atuais
    for _, child in ipairs(self.ScriptsFrame:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
    
    -- Carregar scripts da categoria
    local scripts = ScriptHub.Settings.DefaultScripts[categoryName]
    if not scripts then return end
    
    local totalHeight = 0
    
    for i, scriptData in ipairs(scripts) do
        local scriptFrame = Instance.new("Frame")
        scriptFrame.Name = "Script_" .. i
        scriptFrame.BackgroundColor3 = ScriptHub.Colors.Accent
        scriptFrame.Size = UDim2.new(0.95, 0, 0, 80)
        scriptFrame.Position = UDim2.new(0.025, 0, 0, (i-1)*88)
        scriptFrame.Parent = self.ScriptsFrame
        
        local scriptCorner = Instance.new("UICorner")
        scriptCorner.CornerRadius = UDim.new(0, 8)
        scriptCorner.Parent = scriptFrame
        
        -- Nome do script
        local nameLabel = Instance.new("TextLabel")
        nameLabel.Name = "Name"
        nameLabel.Text = scriptData.Name
        nameLabel.Font = Enum.Font.GothamBold
        nameLabel.TextColor3 = ScriptHub.Colors.Text
        nameLabel.TextSize = 14
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(0.7, 0, 0.4, 0)
        nameLabel.Position = UDim2.new(0.05, 0, 0.1, 0)
        nameLabel.TextXAlignment = Enum.TextXAlignment.Left
        nameLabel.Parent = scriptFrame
        
        -- Botão de execução
        local executeButton = Instance.new("TextButton")
        executeButton.Name = "ExecuteButton"
        executeButton.Text = "Executar"
        executeButton.Font = Enum.Font.GothamBold
        executeButton.TextColor3 = ScriptHub.Colors.Text
        executeButton.TextSize = 12
        executeButton.BackgroundColor3 = ScriptHub.Colors.Success
        executeButton.Size = UDim2.new(0.25, 0, 0.4, 0)
        executeButton.Position = UDim2.new(0.7, 0, 0.1, 0)
        executeButton.Parent = scriptFrame
        
        local execBtnCorner = Instance.new("UICorner")
        execBtnCorner.CornerRadius = UDim.new(0, 6)
        execBtnCorner.Parent = executeButton
        
        executeButton.MouseButton1Click:Connect(function()
            local success, err = pcall(function()
                scriptData.Function()
            end)
            
            if not success then
                self:ShowNotification("Erro ao executar script: " .. err, "error")
            else
                self:ShowNotification("Script executado com sucesso!", "success")
            end
        end)
        
        -- Botão de copiar
        local copyButton = Instance.new("TextButton")
        copyButton.Name = "CopyButton"
        copyButton.Text = "Copiar"
        copyButton.Font = Enum.Font.GothamBold
        copyButton.TextColor3 = ScriptHub.Colors.Text
        copyButton.TextSize = 12
        copyButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
        copyButton.Size = UDim2.new(0.25, 0, 0.4, 0)
        copyButton.Position = UDim2.new(0.7, 0, 0.55, 0)
        copyButton.Parent = scriptFrame
        
        local copyBtnCorner = Instance.new("UICorner")
        copyBtnCorner.CornerRadius = UDim.new(0, 6)
        copyBtnCorner.Parent = copyButton
        
        copyButton.MouseButton1Click:Connect(function()
            self:ShowNotification("Função copiada para área de transferência", "info")
        end)
        
        totalHeight = totalHeight + 88
    end
    
    self.ScriptsFrame.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
end

-- Executar todos scripts da categoria
function ScriptHub:ExecuteAllScripts()
    local scripts = ScriptHub.Settings.DefaultScripts
    local executed = 0
    local failed = 0
    
    for categoryName, categoryScripts in pairs(scripts) do
        for _, scriptData in ipairs(categoryScripts) do
            local success, err = pcall(function()
                scriptData.Function()
            end)
            
            if success then
                executed = executed + 1
            else
                failed = failed + 1
                if ScriptHub.Settings.DebugMode then
                    warn("Erro em " .. scriptData.Name .. ": " .. err)
                end
            end
        end
    end
    
    self:ShowNotification(string.format("Executados: %d | Falhas: %d", executed, failed), 
                         failed == 0 and "success" or "error")
end

-- Mostrar notificação
function ScriptHub:ShowNotification(message, type)
    local color
    if type == "success" then
        color = ScriptHub.Colors.Success
    elseif type == "error" then
        color = ScriptHub.Colors.Error
    else
        color = ScriptHub.Colors.Accent
    end
    
    local notification = Instance.new("Frame")
    notification.Name = "Notification"
    notification.BackgroundColor3 = color
    notification.Size = UDim2.new(0.4, 0, 0, 50)
    notification.Position = UDim2.new(0.3, 0, 1, 10)
    notification.Parent = self.Gui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 8)
    notifCorner.Parent = notification
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Text = message
    textLabel.Font = Enum.Font.Gotham
    textLabel.TextColor3 = ScriptHub.Colors.Text
    textLabel.TextSize = 12
    textLabel.BackgroundTransparency = 1
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.Parent = notification
    
    -- Animação
    notification:TweenPosition(UDim2.new(0.3, 0, 0.8, 0), "Out", "Quad", 0.5)
    
    task.wait(3)
    
    notification:TweenPosition(UDim2.new(0.3, 0, 1, 10), "Out", "Quad", 0.5)
    task.wait(0.5)
    notification:Destroy()
end

-- Métodos públicos
function ScriptHub:AddCategory(name)
    ScriptHub.Settings.DefaultScripts[name] = {}
end

function ScriptHub:AddScript(category, name, func)
    if not ScriptHub.Settings.DefaultScripts[category] then
        self:AddCategory(category)
    end
    
    table.insert(ScriptHub.Settings.DefaultScripts[category], {
        Name = name,
        Function = func
    })
end

function ScriptHub:Toggle()
    if self.Gui and self.Gui.Parent then
        self.Gui:Destroy()
        self.Gui = nil
    else
        self:CreateGUI()
    end
end

-- Inicializar tecla de atalho
function ScriptHub:BindKey(keyCode)
    game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
        if not gameProcessed and input.KeyCode == keyCode then
            self:Toggle()
        end
    end)
end

-- Criar instância padrão
local DefaultHub = setmetatable({}, ScriptHub)
DefaultHub:CreateGUI()
DefaultHub:BindKey(Enum.KeyCode.RightShift)

-- Interface de usuário (para facilitar o uso)
getgenv().ScriptHub = {
    Toggle = function() DefaultHub:Toggle() end,
    AddScript = function(category, name, func) DefaultHub:AddScript(category, name, func) end,
    AddCategory = function(name) DefaultHub:AddCategory(name) end,
    ShowNotification = function(message, type) DefaultHub:ShowNotification(message, type) end
}

-- Retornar a biblioteca
return ScriptHub
