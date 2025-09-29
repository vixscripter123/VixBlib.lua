--// Biblioteca VixBlib Completa - Com Sistema de Keys Integrado
local VixBlib = {}

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")

-- Banco de dados de keys válidas
local VALID_KEYS = {}

--// SISTEMA DE KEYS
function VixBlib:CreateKeySystem(config)
    local keySystem = {}
    
    -- Configurações
    local hubName = config.HubName or "VixHub"
    local apiUrl = config.ApiUrl or nil -- URL da sua API (opcional)
    local onSuccess = config.OnSuccess or function() end
    local getKeyUrl = config.GetKeyUrl or "https://seu-site-keys.com"
    
    -- Criar ScreenGui
    local gui = Instance.new("ScreenGui")
    gui.Name = "VixKeySystem"
    gui.ResetOnSpawn = false
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    gui.Parent = PlayerGui
    
    -- Overlay
    local overlay = Instance.new("Frame")
    overlay.Size = UDim2.new(1, 0, 1, 0)
    overlay.BackgroundColor3 = Color3.fromRGB(10, 20, 40)
    overlay.BackgroundTransparency = 0.2
    overlay.BorderSizePixel = 0
    overlay.Parent = gui
    
    -- Frame principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 480, 0, 400)
    mainFrame.Position = UDim2.new(0.5, -240, 0.5, -200)
    mainFrame.BackgroundColor3 = Color3.fromRGB(30, 35, 50)
    mainFrame.BackgroundTransparency = 0.05
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = overlay
    
    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 15)
    mainCorner.Parent = mainFrame
    
    -- Sombra
    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 30, 1, 30)
    shadow.Position = UDim2.new(0, -15, 0, -15)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    shadow.BackgroundTransparency = 0.6
    shadow.BorderSizePixel = 0
    shadow.ZIndex = mainFrame.ZIndex - 1
    shadow.Parent = mainFrame
    
    local shadowCorner = Instance.new("UICorner")
    shadowCorner.CornerRadius = UDim.new(0, 20)
    shadowCorner.Parent = shadow
    
    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 80)
    header.BackgroundColor3 = Color3.fromRGB(25, 30, 45)
    header.BackgroundTransparency = 0.3
    header.BorderSizePixel = 0
    header.Parent = mainFrame
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 15)
    headerCorner.Parent = header
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -80, 0, 35)
    title.Position = UDim2.new(0, 20, 0, 10)
    title.Text = hubName
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.BackgroundTransparency = 1
    title.Font = Enum.Font.GothamBold
    title.TextSize = 24
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = header
    
    local titleStroke = Instance.new("UIStroke")
    titleStroke.Color = Color3.fromRGB(100, 200, 255)
    titleStroke.Transparency = 0.7
    titleStroke.Thickness = 2
    titleStroke.Parent = title
    
    -- Subtítulo
    local subtitle = Instance.new("TextLabel")
    subtitle.Size = UDim2.new(1, -80, 0, 25)
    subtitle.Position = UDim2.new(0, 20, 0, 48)
    subtitle.Text = "KEY DO SISTEMA"
    subtitle.TextColor3 = Color3.fromRGB(180, 180, 180)
    subtitle.BackgroundTransparency = 1
    subtitle.Font = Enum.Font.Gotham
    subtitle.TextSize = 13
    subtitle.TextXAlignment = Enum.TextXAlignment.Left
    subtitle.Parent = header
    
    -- Botão fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 40, 0, 40)
    closeBtn.Position = UDim2.new(1, -50, 0, 10)
    closeBtn.Text = "×"
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 24
    closeBtn.BackgroundColor3 = Color3.fromRGB(120, 60, 60)
    closeBtn.BackgroundTransparency = 0.3
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = header
    
    local closeBtnCorner = Instance.new("UICorner")
    closeBtnCorner.CornerRadius = UDim.new(0, 10)
    closeBtnCorner.Parent = closeBtn
    
    -- Página 1: Tela inicial
    local page1 = Instance.new("Frame")
    page1.Size = UDim2.new(1, -40, 1, -180)
    page1.Position = UDim2.new(0, 20, 0, 100)
    page1.BackgroundTransparency = 1
    page1.Parent = mainFrame
    
    local infoFrame = Instance.new("Frame")
    infoFrame.Size = UDim2.new(1, 0, 0, 140)
    infoFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
    infoFrame.BackgroundTransparency = 0.3
    infoFrame.BorderSizePixel = 0
    infoFrame.Parent = page1
    
    local infoCorner = Instance.new("UICorner")
    infoCorner.CornerRadius = UDim.new(0, 12)
    infoCorner.Parent = infoFrame
    
    local infoStroke = Instance.new("UIStroke")
    infoStroke.Color = Color3.fromRGB(100, 200, 255)
    infoStroke.Transparency = 0.7
    infoStroke.Thickness = 2
    infoStroke.Parent = infoFrame
    
    local infoText = Instance.new("TextLabel")
    infoText.Size = UDim2.new(1, -30, 0, 50)
    infoText.Position = UDim2.new(0, 15, 0, 15)
    infoText.Text = "A sua key ta quase pronta só tenha paciência e clike no botão em baixo"
    infoText.TextColor3 = Color3.fromRGB(255, 255, 255)
    infoText.BackgroundTransparency = 1
    infoText.Font = Enum.Font.Gotham
    infoText.TextSize = 13
    infoText.TextWrapped = true
    infoText.Parent = infoFrame
    
    local generateArea = Instance.new("Frame")
    generateArea.Size = UDim2.new(1, -30, 0, 60)
    generateArea.Position = UDim2.new(0, 15, 0, 70)
    generateArea.BackgroundTransparency = 1
    generateArea.Parent = infoFrame
    
    local folderIcon = Instance.new("TextLabel")
    folderIcon.Size = UDim2.new(0, 50, 0, 50)
    folderIcon.Position = UDim2.new(0, 10, 0, 5)
    folderIcon.Text = "📁"
    folderIcon.TextSize = 32
    folderIcon.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
    folderIcon.BackgroundTransparency = 0.3
    folderIcon.BorderSizePixel = 0
    folderIcon.Parent = generateArea
    
    local iconCorner = Instance.new("UICorner")
    iconCorner.CornerRadius = UDim.new(0, 8)
    iconCorner.Parent = folderIcon
    
    local generatingText = Instance.new("TextLabel")
    generatingText.Size = UDim2.new(1, -150, 1, 0)
    generatingText.Position = UDim2.new(0, 70, 0, 0)
    generatingText.Text = "Gerando a sua key só clike no botão"
    generatingText.TextColor3 = Color3.fromRGB(100, 200, 255)
    generatingText.BackgroundTransparency = 1
    generatingText.Font = Enum.Font.GothamBold
    generatingText.TextSize = 15
    generatingText.TextXAlignment = Enum.TextXAlignment.Left
    generatingText.Parent = generateArea
    
    local createBtn = Instance.new("TextButton")
    createBtn.Size = UDim2.new(1, 0, 0, 50)
    createBtn.Position = UDim2.new(0, 0, 0, 160)
    createBtn.Text = "Criar a sua key"
    createBtn.Font = Enum.Font.GothamBold
    createBtn.TextSize = 16
    createBtn.BackgroundColor3 = Color3.fromRGB(60, 100, 180)
    createBtn.BackgroundTransparency = 0.2
    createBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    createBtn.BorderSizePixel = 0
    createBtn.Parent = page1
    
    local createCorner = Instance.new("UICorner")
    createCorner.CornerRadius = UDim.new(0, 10)
    createCorner.Parent = createBtn
    
    -- Página 2: Validação de key
    local page2 = Instance.new("Frame")
    page2.Size = UDim2.new(1, -40, 1, -180)
    page2.Position = UDim2.new(0, 20, 0, 100)
    page2.BackgroundTransparency = 1
    page2.Visible = false
    page2.Parent = mainFrame
    
    local keyInputFrame = Instance.new("Frame")
    keyInputFrame.Size = UDim2.new(1, 0, 0, 60)
    keyInputFrame.BackgroundColor3 = Color3.fromRGB(20, 25, 40)
    keyInputFrame.BackgroundTransparency = 0.3
    keyInputFrame.BorderSizePixel = 0
    keyInputFrame.Parent = page2
    
    local inputCorner = Instance.new("UICorner")
    inputCorner.CornerRadius = UDim.new(0, 10)
    inputCorner.Parent = keyInputFrame
    
    local inputStroke = Instance.new("UIStroke")
    inputStroke.Color = Color3.fromRGB(100, 200, 255)
    inputStroke.Transparency = 0.7
    inputStroke.Thickness = 2
    inputStroke.Parent = keyInputFrame
    
    local keyInput = Instance.new("TextBox")
    keyInput.Size = UDim2.new(1, -30, 1, -20)
    keyInput.Position = UDim2.new(0, 15, 0, 10)
    keyInput.PlaceholderText = "VIXB-XXXX-XXXX-XXXX"
    keyInput.Text = ""
    keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
    keyInput.PlaceholderColor3 = Color3.fromRGB(120, 120, 140)
    keyInput.BackgroundTransparency = 1
    keyInput.Font = Enum.Font.GothamBold
    keyInput.TextSize = 18
    keyInput.Parent = keyInputFrame
    
    local btnContainer = Instance.new("Frame")
    btnContainer.Size = UDim2.new(1, 0, 0, 50)
    btnContainer.Position = UDim2.new(0, 0, 0, 80)
    btnContainer.BackgroundTransparency = 1
    btnContainer.Parent = page2
    
    local validateBtn = Instance.new("TextButton")
    validateBtn.Size = UDim2.new(0.48, 0, 1, 0)
    validateBtn.Text = "ok aceita a key"
    validateBtn.Font = Enum.Font.GothamBold
    validateBtn.TextSize = 14
    validateBtn.BackgroundColor3 = Color3.fromRGB(50, 180, 50)
    validateBtn.BackgroundTransparency = 0.2
    validateBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    validateBtn.BorderSizePixel = 0
    validateBtn.Parent = btnContainer
    
    local validateCorner = Instance.new("UICorner")
    validateCorner.CornerRadius = UDim.new(0, 10)
    validateCorner.Parent = validateBtn
    
    local getKeyBtn = Instance.new("TextButton")
    getKeyBtn.Size = UDim2.new(0.48, 0, 1, 0)
    getKeyBtn.Position = UDim2.new(0.52, 0, 0, 0)
    getKeyBtn.Text = "Pegar a key"
    getKeyBtn.Font = Enum.Font.GothamBold
    getKeyBtn.TextSize = 14
    getKeyBtn.BackgroundColor3 = Color3.fromRGB(60, 100, 180)
    getKeyBtn.BackgroundTransparency = 0.2
    getKeyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    getKeyBtn.BorderSizePixel = 0
    getKeyBtn.Parent = btnContainer
    
    local getKeyCorner = Instance.new("UICorner")
    getKeyCorner.CornerRadius = UDim.new(0, 10)
    getKeyCorner.Parent = getKeyBtn
    
    local statusMsg = Instance.new("TextLabel")
    statusMsg.Size = UDim2.new(1, 0, 0, 80)
    statusMsg.Position = UDim2.new(0, 0, 0, 150)
    statusMsg.Text = ""
    statusMsg.TextColor3 = Color3.fromRGB(255, 255, 255)
    statusMsg.BackgroundTransparency = 1
    statusMsg.Font = Enum.Font.Gotham
    statusMsg.TextSize = 13
    statusMsg.TextWrapped = true
    statusMsg.Visible = false
    statusMsg.Parent = page2
    
    local footer = Instance.new("TextLabel")
    footer.Size = UDim2.new(1, 0, 0, 40)
    footer.Position = UDim2.new(0, 0, 1, -45)
    footer.Text = "Criado pro Vix Scripter"
    footer.TextColor3 = Color3.fromRGB(150, 150, 150)
    footer.BackgroundTransparency = 1
    footer.Font = Enum.Font.Gotham
    footer.TextSize = 12
    footer.Parent = mainFrame
    
    -- Função de validação
    local function validateKey(key)
        key = key:gsub("%s+", "")
        
        if not key:match("^VIXB%-%w%w%w%w%-%w%w%w%w%-%w%w%w%w$") then
            return false, "Formato de key inválido!"
        end
        
        -- Se tiver API configurada
        if apiUrl then
            local success, response = pcall(function()
                return HttpService:RequestAsync({
                    Url = apiUrl .. "/api/keys/validate",
                    Method = "POST",
                    Headers = {["Content-Type"] = "application/json"},
                    Body = HttpService:JSONEncode({
                        key = key,
                        userId = tostring(LocalPlayer.UserId),
                        userName = LocalPlayer.Name
                    })
                })
            end)
            
            if success and response.StatusCode == 200 then
                local data = HttpService:JSONDecode(response.Body)
                return data.success, data.message
            else
                return false, "Erro ao conectar com servidor"
            end
        end
        
        -- Validação local
        local keyData = VALID_KEYS[key]
        if keyData then
            if keyData.used then
                return false, "Key já foi utilizada!"
            end
            if keyData.expiryDate and os.time() > keyData.expiryDate then
                return false, "Key expirada!"
            end
            keyData.used = true
            return true, "Key válida! Bem-vindo " .. (keyData.userName or LocalPlayer.Name)
        end
        
        return false, "Key não encontrada ou inválida!"
    end
    
    -- Eventos
    createBtn.MouseButton1Click:Connect(function()
        page1.Visible = false
        page2.Visible = true
        subtitle.Text = "INSIRA SUA KEY"
    end)
    
    validateBtn.MouseButton1Click:Connect(function()
        local inputKey = keyInput.Text
        
        if inputKey == "" then
            statusMsg.Text = "❌ Por favor, insira uma key!"
            statusMsg.TextColor3 = Color3.fromRGB(255, 100, 100)
            statusMsg.Visible = true
            return
        end
        
        local isValid, message = validateKey(inputKey)
        
        if isValid then
            statusMsg.Text = "✅ " .. message
            statusMsg.TextColor3 = Color3.fromRGB(100, 255, 100)
            statusMsg.Visible = true
            
            wait(1.5)
            gui:Destroy()
            onSuccess()
        else
            statusMsg.Text = "❌ " .. message
            statusMsg.TextColor3 = Color3.fromRGB(255, 100, 100)
            statusMsg.Visible = true
        end
    end)
    
    getKeyBtn.MouseButton1Click:Connect(function()
        setclipboard(getKeyUrl)
        statusMsg.Text = "🔗 Link copiado! Cole no navegador para pegar sua key."
        statusMsg.TextColor3 = Color3.fromRGB(100, 200, 255)
        statusMsg.Visible = true
    end)
    
    closeBtn.MouseButton1Click:Connect(function()
        gui:Destroy()
    end)
    
    return keySystem
end

-- Adicionar key manualmente
function VixBlib:AddKey(key, data)
    VALID_KEYS[key] = {
        userName = data.UserName,
        hubName = data.HubName,
        createdAt = os.time(),
        expiryDate = data.ExpiryDate,
        used = false
    }
end

--// SISTEMA DE BOLINHA TOGGLE
function VixBlib:CreateBolinha(config)
    local bolinha = {}
    
    local cor = config.Cor or Color3.fromRGB(100, 200, 255)
    local tamanho = config.Tamanho or 60
    local posicao = config.Posicao or {X = 100, Y = 100}
    
    local gui = Instance.new("ScreenGui")
    gui.Name = "VixBolinhaToggle"
    gui.ResetOnSpawn = false
    gui.Parent = PlayerGui
    
    local frame = Instance.new("TextButton")
    frame.Size = UDim2.new(0, tamanho, 0, tamanho)
    frame.Position = UDim2.new(0, posicao.X, 0, posicao.Y)
    frame.BackgroundColor3 = cor
    frame.BorderSizePixel = 0
    frame.Text = "≡"
    frame.TextColor3 = Color3.fromRGB(255, 255, 255)
    frame.TextScaled = true
    frame.Font = Enum.Font.GothamBold
    frame.Parent = gui
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0.5, 0)
    corner.Parent = frame
    
    local dragging = false
    local dragStart, startPos
    
    frame.MouseButton1Down:Connect(function()
        dragging = true
        local mouse = Players.LocalPlayer:GetMouse()
        dragStart = Vector2.new(mouse.X, mouse.Y)
        startPos = frame.Position
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mouse = Players.LocalPlayer:GetMouse()
            local delta = Vector2.new(mouse.X, mouse.Y) - dragStart
            frame.Position = UDim2.new(0, startPos.X.Offset + delta.X, 0, startPos.Y.Offset + delta.Y)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    local windowConnected
    
    function bolinha:ConectarJanela(window)
        windowConnected = window
    end
    
    frame.MouseButton1Click:Connect(function()
        if windowConnected and windowConnected.mainFrame then
            windowConnected.mainFrame.Visible = not windowConnected.mainFrame.Visible
        end
    end)
    
    function bolinha:MudarCor(novaCor)
        frame.BackgroundColor3 = novaCor
    end
    
    function bolinha:Destroy()
        gui:Destroy()
    end
    
    return bolinha
end

--// SISTEMA DE JANELA
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
    mainFrame.Size = UDim2.new(0, 850, 0, 520)
    mainFrame.Position = UDim2.new(0.5, -425, 0.5, -260)
    mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    mainFrame.BackgroundTransparency = 0.1
    mainFrame.BorderSizePixel = 0
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = gui

    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 12)
    mainCorner.Parent = mainFrame

    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 20, 1, 20)
    shadow.Position = UDim2.new(0, -10, 0, -10)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    shadow.BackgroundTransparency = 0.7
    shadow.BorderSizePixel = 0
    shadow.ZIndex = mainFrame.ZIndex - 1
    shadow.Parent = mainFrame

    local shadowCorner = Instance.new("UICorner")
    shadowCorner.CornerRadius = UDim.new(0, 15)
    shadowCorner.Parent = shadow

    local topBar = Instance.new("Frame")
    topBar.Size = UDim2.new(1, 0, 0, 45)
    topBar.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    topBar.BackgroundTransparency = 0.2
    topBar.BorderSizePixel = 0
    topBar.Parent = mainFrame

    local topCorner = Instance.new("UICorner")
    topCorner.CornerRadius = UDim.new(0, 12)
    topCorner.Parent = topBar

    local topGradient = Instance.new("UIGradient")
    topGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(45, 45, 65)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(35, 35, 50))
    }
    topGradient.Rotation = 90
    topGradient.Parent = topBar

    local menuBtn = Instance.new("TextButton")
    menuBtn.Size = UDim2.new(0, 45, 0, 45)
    menuBtn.Position = UDim2.new(0, 0, 0, 0)
    menuBtn.Text = "≡"
    menuBtn.Font = Enum.Font.GothamBold
    menuBtn.TextSize = 22
    menuBtn.BackgroundTransparency = 1
    menuBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    menuBtn.Parent = topBar

    menuBtn.MouseEnter:Connect(function()
        TweenService:Create(menuBtn, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(100, 200, 255)}):Play()
    end)

    menuBtn.MouseLeave:Connect(function()
        TweenService:Create(menuBtn, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(255, 255, 255)}):Play()
    end)

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -130, 1, 0)
    title.Position = UDim2.new(0, 55, 0, 0)
    title.Text = config.Nome or "VixBlib"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.BackgroundTransparency = 1
    title.Font = Enum.Font.GothamBold
    title.TextSize = 18
    title.Parent = topBar

    local titleStroke = Instance.new("UIStroke")
    titleStroke.Color = Color3.fromRGB(100, 200, 255)
    titleStroke.Transparency = 0.8
    titleStroke.Thickness = 1
    titleStroke.Parent = title

    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 38, 0, 38)
    minBtn.Position = UDim2.new(1, -80, 0.5, -19)
    minBtn.Text = "−"
    minBtn.Font = Enum.Font.GothamBold
    minBtn.TextSize = 18
    minBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    minBtn.BackgroundTransparency = 0.3
    minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minBtn.BorderSizePixel = 0
    minBtn.Parent = topBar

    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 8)
    minCorner.Parent = minBtn

    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 38, 0, 38)
    closeBtn.Position = UDim2.new(1, -38, 0.5, -19)
    closeBtn.Text = "×"
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 18
    closeBtn.BackgroundColor3 = Color3.fromRGB(120, 60, 60)
    closeBtn.BackgroundTransparency = 0.3
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = topBar

    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeBtn

    minBtn.MouseEnter:Connect(function()
        TweenService:Create(minBtn, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
    end)
    minBtn.MouseLeave:Connect(function()
        TweenService:Create(minBtn, TweenInfo.new(0.2), {BackgroundTransparency = 0.3}):Play()
    end)

    closeBtn.MouseEnter:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
    end)
    closeBtn.MouseLeave:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.2), {BackgroundTransparency = 0.3}):Play()
    end)

    closeBtn.MouseButton1Click:Connect(function()
        local closeTween = TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back), {
            Size = UDim2.new(0, 0, 0, 0),
            BackgroundTransparency = 1
        })
        closeTween:Play()
        closeTween.Completed:Connect(function()
            gui:Destroy()
        end)
    end)

    local minimized = false
    minBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        
        if minimized then
            mainFrame.Size = UDim2.new(0, 300, 0, 45)
            for _, child in pairs(mainFrame:GetChildren()) do
                if child ~= topBar and child:IsA("GuiObject") then
                    child.Visible = false
                end
            end
        else
            mainFrame.Size = UDim2.new(0, 850, 0, 520)
            wait(0.1)
            for _, child in pairs(mainFrame:GetChildren()) do
                if child ~= topBar and child:IsA("GuiObject") then
                    child.Visible = true
                end
            end
            for _, aba in pairs(window.Abas) do
                if aba.Button.BackgroundColor3 == Color3.fromRGB(70, 70, 100) then
                    aba.Frame.Visible = true
                else
                    aba.Frame.Visible = false
                end
            end
        end
    end)

    local sidePanel = Instance.new("Frame")
    sidePanel.Size = UDim2.new(0, 90, 1, -45)
    sidePanel.Position = UDim2.new(0, 0, 0, 45)
    sidePanel.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    sidePanel.BackgroundTransparency = 0.15
    sidePanel.BorderSizePixel = 0
    sidePanel.ClipsDescendants = true
    sidePanel.Parent = mainFrame

    local sideGradient = Instance.new("UIGradient")
    sideGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 40)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 15, 25))
    }
    sideGradient.Rotation = 180
    sideGradient.Parent = sidePanel

    local divider = Instance.new("Frame")
    divider.Size = UDim2.new(0, 1, 1, 0)
    divider.Position = UDim2.new(1, 0, 0, 0)
    divider.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
    divider.BackgroundTransparency = 0.7
    divider.BorderSizePixel = 0
    divider.Parent = sidePanel

    local abasTitle = Instance.new("TextLabel")
    abasTitle.Size = UDim2.new(1, 0, 0, 35)
    abasTitle.Position = UDim2.new(0, 0, 0, 15)
    abasTitle.Text = "ABAS"
    abasTitle.TextColor3 = Color3.fromRGB(220, 220, 220)
    abasTitle.BackgroundTransparency = 1
    abasTitle.Font = Enum.Font.GothamBold
    abasTitle.TextSize = 14
    abasTitle.TextXAlignment = Enum.TextXAlignment.Center
    abasTitle.Parent = sidePanel

    local abasStroke = Instance.new("UIStroke")
    abasStroke.Color = Color3.fromRGB(100, 200, 255)
    abasStroke.Transparency = 0.6
    abasStroke.Thickness = 1
    abasStroke.Parent = abasTitle

    local abaContainer = Instance.new("ScrollingFrame")
    abaContainer.Size = UDim2.new(1, 0, 1, -120)
    abaContainer.Position = UDim2.new(0, 0, 0, 50)
    abaContainer.BackgroundTransparency = 1
    abaContainer.BorderSizePixel = 0
    abaContainer.ScrollBarThickness = 3
    abaContainer.ScrollBarImageColor3 = Color3.fromRGB(100, 200, 255)
    abaContainer.ScrollBarImageTransparency = 0.5
    abaContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
    abaContainer.Parent = sidePanel

    local abaLayout = Instance.new("UIListLayout")
    abaLayout.SortOrder = Enum.SortOrder.LayoutOrder
    abaLayout.Padding = UDim.new(0, 8)
    abaLayout.Parent = abaContainer

    abaLayout.Changed:Connect(function()
        abaContainer.CanvasSize = UDim2.new(0, 0, 0, abaLayout.AbsoluteContentSize.Y + 20)
    end)

    local playerImage = Instance.new("ImageLabel")
    playerImage.Size = UDim2.new(0, 55, 0, 55)
    playerImage.Position = UDim2.new(0.5, -27.5, 1, -70)
    playerImage.BackgroundTransparency = 1
    playerImage.Image = "https://www.roblox.com/headshot-thumbnail/image?userId="..LocalPlayer.UserId.."&width=150&height=150&format=png"
    playerImage.Parent = sidePanel

    local imgCorner = Instance.new("UICorner")
    imgCorner.CornerRadius = UDim.new(0, 12)
    imgCorner.Parent = playerImage

    local imgStroke = Instance.new("UIStroke")
    imgStroke.Color = Color3.fromRGB(100, 200, 255)
    imgStroke.Transparency = 0.5
    imgStroke.Thickness = 2
    imgStroke.Parent = playerImage

    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -90, 1, -45)
    contentFrame.Position = UDim2.new(0, 90, 0, 45)
    contentFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    contentFrame.BackgroundTransparency = 0.2
    contentFrame.BorderSizePixel = 0
    contentFrame.ClipsDescendants = true
    contentFrame.Parent = mainFrame

    local contentGradient = Instance.new("UIGradient")
    contentGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(35, 35, 50)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 40))
    }
    contentGradient.Rotation = 45
    contentGradient.Parent = contentFrame

    local abaTitle = Instance.new("TextLabel")
    abaTitle.Size = UDim2.new(1, -50, 0, 70)
    abaTitle.Position = UDim2.new(0, 25, 0, 25)
    abaTitle.Text = "NOME DA ABA SELECIONADA"
    abaTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    abaTitle.BackgroundTransparency = 1
    abaTitle.Font = Enum.Font.GothamBold
    abaTitle.TextSize = 26
    abaTitle.TextXAlignment = Enum.TextXAlignment.Left
    abaTitle.TextYAlignment = Enum.TextYAlignment.Center
    abaTitle.Parent = contentFrame

    local titleMainStroke = Instance.new("UIStroke")
    titleMainStroke.Color = Color3.fromRGB(100, 200, 255)
    titleMainStroke.Transparency = 0.7
    titleMainStroke.Thickness = 1
    titleMainStroke.Parent = abaTitle

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
        btn.Size = UDim2.new(1, -10, 0, 65)
        btn.Position = UDim2.new(0, 5, 0, 0)
        btn.Text = nome
        btn.Font = Enum.Font.GothamBold
        btn.TextSize = 11
        btn.BackgroundColor3 = Color3.fromRGB(40, 40, 60)
        btn.BackgroundTransparency = 0.2
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.BorderSizePixel = 0
        btn.TextWrapped = true
        btn.Parent = abaContainer

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = btn

        local btnStroke = Instance.new("UIStroke")
        btnStroke.Color = Color3.fromRGB(100, 200, 255)
        btnStroke.Transparency = 0.8
        btnStroke.Thickness = 1
        btnStroke.Parent = btn

        local btnGradient = Instance.new("UIGradient")
        btnGradient.Color = ColorSequence.new{
            ColorSequenceKeypoint.new(0, Color3.fromRGB(50, 50, 70)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(35, 35, 55))
        }
        btnGradient.Rotation = 90
        btnGradient.Parent = btn

        btn.MouseEnter:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
            TweenService:Create(btnStroke, TweenInfo.new(0.2), {Transparency = 0.4}):Play()
        end)

        btn.MouseLeave:Connect(function()
            if btn.BackgroundColor3 ~= Color3.fromRGB(70, 70, 100) then
                TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.2}):Play()
                TweenService:Create(btnStroke, TweenInfo.new(0.2), {Transparency = 0.8}):Play()
            end
        end)

        local abaFrame = Instance.new("ScrollingFrame")
        abaFrame.Size = UDim2.new(1, -50, 1, -110)
        abaFrame.Position = UDim2.new(0, 25, 0, 85)
        abaFrame.BackgroundTransparency = 1
        abaFrame.BorderSizePixel = 0
        abaFrame.ScrollBarThickness = 5
        abaFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 200, 255)
        abaFrame.ScrollBarImageTransparency = 0.4
        abaFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
        abaFrame.Visible = false
        abaFrame.Parent = contentFrame

        local layout = Instance.new("UIListLayout")
        layout.SortOrder = Enum.SortOrder.LayoutOrder
        layout.Padding = UDim.new(0, 10)
        layout.Parent = abaFrame

        layout.Changed:Connect(function()
            abaFrame.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 20)
        end)

        btn.MouseButton1Click:Connect(function()
            for _, other in pairs(window.Abas) do
                TweenService:Create(other.Button, TweenInfo.new(0.3), {
                    BackgroundColor3 = Color3.fromRGB(40, 40, 60),
                    BackgroundTransparency = 0.2
                }):Play()
                TweenService:Create(other.Button:FindFirstChild("UIStroke"), TweenInfo.new(0.3), {Transparency = 0.8}):Play()
                other.Frame.Visible = false
            end
            TweenService:Create(btn, TweenInfo.new(0.3), {
                BackgroundColor3 = Color3.fromRGB(70, 70, 100),
                BackgroundTransparency = 0.1
            }):Play()
            TweenService:Create(btnStroke, TweenInfo.new(0.3), {Transparency = 0.3}):Play()
            abaFrame.Visible = true
            
            abaTitle.TextTransparency = 1
            abaTitle.Text = nome:upper()
            TweenService:Create(abaTitle, TweenInfo.new(0.5), {TextTransparency = 0}):Play()
        end)

        if primeiraAba then
            primeiraAba = false
            btn.BackgroundColor3 = Color3.fromRGB(70, 70, 100)
            btn.BackgroundTransparency = 0.1
            btnStroke.Transparency = 0.3
            abaFrame.Visible = true
            abaTitle.Text = nome:upper()
        end

        aba.Button = btn
        aba.Frame = abaFrame
        table.insert(window.Abas, aba)

        function aba:CreateButton(txt, callback)
            elementCount = elementCount + 1
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, -20, 0, 40)
            b.Position = UDim2.new(0, 10, 0, 0)
            b.Text = txt
            b.Font = Enum.Font.GothamBold
            b.TextSize = 14
            b.BackgroundColor3 = Color3.fromRGB(60, 60, 90)
            b.BackgroundTransparency = 0.2
            b.TextColor3 = Color3.fromRGB(255, 255, 255)
            b.BorderSizePixel = 0
            b.Parent = abaFrame
            b.LayoutOrder = elementCount

            local bCorner = Instance.new("UICorner")
            bCorner.CornerRadius = UDim.new(0, 8)
            bCorner.Parent = b

            local bStroke = Instance.new("UIStroke")
            bStroke.Color = Color3.fromRGB(100, 200, 255)
            bStroke.Transparency = 0.7
            bStroke.Thickness = 1
            bStroke.Parent = b

            b.MouseEnter:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
            end)

            b.MouseLeave:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.2}):Play()
            end)
            
            b.MouseButton1Click:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.1), {Size = UDim2.new(1, -18, 0, 38)}):Play()
                wait(0.1)
                TweenService:Create(b, TweenInfo.new(0.2), {Size = UDim2.new(1, -20, 0, 40)}):Play()
                if callback then callback() end
            end)
        end

        function aba:CreateToggle(txt, default, callback)
            elementCount = elementCount + 1
            local toggleFrame = Instance.new("Frame")
            toggleFrame.Size = UDim2.new(1, -20, 0, 40)
            toggleFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            toggleFrame.BackgroundTransparency = 0.3
            toggleFrame.BorderSizePixel = 0
            toggleFrame.Parent = abaFrame
            toggleFrame.LayoutOrder = elementCount

            local toggleCorner = Instance.new("UICorner")
            toggleCorner.CornerRadius = UDim.new(0, 8)
            toggleCorner.Parent = toggleFrame

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, -70, 1, 0)
            label.Position = UDim2.new(0, 15, 0, 0)
            label.Text = txt
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.BackgroundTransparency = 1
            label.Font = Enum.Font.GothamBold
            label.TextSize = 14
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = toggleFrame

            local toggle = Instance.new("TextButton")
            toggle.Size = UDim2.new(0, 45, 0, 22)
            toggle.Position = UDim2.new(1, -55, 0.5, -11)
            toggle.Text = ""
            toggle.BackgroundColor3 = default and Color3.fromRGB(50, 180, 50) or Color3.fromRGB(80, 80, 80)
            toggle.BorderSizePixel = 0
            toggle.Parent = toggleFrame

            local toggleCorner2 = Instance.new("UICorner")
            toggleCorner2.CornerRadius = UDim.new(0, 11)
            toggleCorner2.Parent = toggle

            local circle = Instance.new("Frame")
            circle.Size = UDim2.new(0, 18, 0, 18)
            circle.Position = default and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
            circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            circle.BorderSizePixel = 0
            circle.Parent = toggle

            local circleCorner = Instance.new("UICorner")
            circleCorner.CornerRadius = UDim.new(0, 9)
            circleCorner.Parent = circle

            local toggled = default or false

            toggle.MouseButton1Click:Connect(function()
                toggled = not toggled
                local newPos = toggled and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
                local newColor = toggled and Color3.fromRGB(50, 180, 50) or Color3.fromRGB(80, 80, 80)
                
                TweenService:Create(circle, TweenInfo.new(0.3, Enum.EasingStyle.Back), {Position = newPos}):Play()
                TweenService:Create(toggle, TweenInfo.new(0.3), {BackgroundColor3 = newColor}):Play()
                
                if callback then callback(toggled) end
            end)

            return {
                SetValue = function(self, value)
                    toggled = value
                    circle.Position = toggled and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
                    toggle.BackgroundColor3 = toggled and Color3.fromRGB(50, 180, 50) or Color3.fromRGB(80, 80, 80)
                end
            }
        end

        function aba:CreateSlider(txt, min, max, default, callback)
            elementCount = elementCount + 1
            local sliderFrame = Instance.new("Frame")
            sliderFrame.Size = UDim2.new(1, -20, 0, 55)
            sliderFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            sliderFrame.BackgroundTransparency = 0.3
            sliderFrame.BorderSizePixel = 0
            sliderFrame.Parent = abaFrame
            sliderFrame.LayoutOrder = elementCount

            local sliderCorner = Instance.new("UICorner")
            sliderCorner.CornerRadius = UDim.new(0, 8)
            sliderCorner.Parent = sliderFrame

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, -120, 0, 28)
            label.Position = UDim2.new(0, 15, 0, 0)
            label.Text = txt
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.BackgroundTransparency = 1
            label.Font = Enum.Font.GothamBold
            label.TextSize = 14
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = sliderFrame

            local valueLabel = Instance.new("TextLabel")
            valueLabel.Size = UDim2.new(0, 90, 0, 28)
            valueLabel.Position = UDim2.new(1, -105, 0, 0)
            valueLabel.Text = tostring(default)
            valueLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
            valueLabel.BackgroundTransparency = 1
            valueLabel.Font = Enum.Font.GothamBold
            valueLabel.TextSize = 13
            valueLabel.TextXAlignment = Enum.TextXAlignment.Right
            valueLabel.Parent = sliderFrame

            local sliderBar = Instance.new("Frame")
            sliderBar.Size = UDim2.new(1, -30, 0, 6)
            sliderBar.Position = UDim2.new(0, 15, 1, -20)
            sliderBar.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
            sliderBar.BorderSizePixel = 0
            sliderBar.Parent = sliderFrame

            local barCorner = Instance.new("UICorner")
            barCorner.CornerRadius = UDim.new(0, 3)
            barCorner.Parent = sliderBar

            local fillBar = Instance.new("Frame")
            fillBar.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
            fillBar.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
            fillBar.BorderSizePixel = 0
            fillBar.Parent = sliderBar

            local fillCorner = Instance.new("UICorner")
            fillCorner.CornerRadius = UDim.new(0, 3)
            fillCorner.Parent = fillBar

            local slider = Instance.new("TextButton")
            slider.Size = UDim2.new(0, 20, 0, 20)
            slider.Position = UDim2.new((default - min) / (max - min), -10, 0, -7)
            slider.Text = ""
            slider.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            slider.BorderSizePixel = 0
            slider.Parent = sliderBar

            local sliderCorner2 = Instance.new("UICorner")
            sliderCorner2.CornerRadius = UDim.new(0, 10)
            sliderCorner2.Parent = slider

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
                    slider.Position = UDim2.new(relativePos, -10, 0, -7)
                    
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
                    slider.Position = UDim2.new(relativePos, -10, 0, -7)
                end
            }
        end

        return aba
    end

    window.mainFrame = mainFrame
    return window
end

return VixBlib
