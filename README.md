--// Biblioteca VixBlib - Layout Melhorado com Transparência
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
    mainFrame.Size = UDim2.new(0, 850, 0, 520)
    mainFrame.Position = UDim2.new(0.5, -425, 0.5, -260)
    mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    mainFrame.BackgroundTransparency = 0.1
    mainFrame.BorderSizePixel = 0
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    mainFrame.ClipsDescendants = true  -- IMPORTANTE: Corta qualquer coisa que saia dos limites
    mainFrame.Parent = gui

    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 12)
    mainCorner.Parent = mainFrame

    -- Efeito de blur/sombra
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

    -- Barra superior com gradiente
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

    -- Menu hamburguer com efeito hover
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

    -- Título com efeito
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

    -- Botões com efeitos hover melhorados
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

    -- Efeitos hover nos botões
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
        local targetSize = minimized and UDim2.new(0, 300, 0, 45) or UDim2.new(0, 850, 0, 520)
        
        if minimized then
            -- Esconder TODOS os elementos exceto topBar
            for _, child in pairs(mainFrame:GetChildren()) do
                if child ~= topBar and child ~= shadow and child:IsA("GuiObject") then
                    child.Visible = false
                end
            end
            -- Também esconder todos os filhos profundos
            sidePanel.Visible = false
            contentFrame.Visible = false
            abaContainer.Visible = false
            for _, aba in pairs(window.Abas) do
                if aba.Frame then
                    aba.Frame.Visible = false
                end
            end
            
            local tween = TweenService:Create(mainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Quart), {Size = targetSize})
            tween:Play()
        else
            -- Aumentar tamanho primeiro, depois mostrar elementos
            local tween = TweenService:Create(mainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Quart), {Size = targetSize})
            tween:Play()
            tween.Completed:Connect(function()
                -- Restaurar todos os elementos
                for _, child in pairs(mainFrame:GetChildren()) do
                    if child ~= topBar and child ~= shadow and child:IsA("GuiObject") then
                        child.Visible = true
                    end
                end
                sidePanel.Visible = true
                contentFrame.Visible = true
                abaContainer.Visible = true
                -- Restaurar aba ativa
                for _, aba in pairs(window.Abas) do
                    if aba.Button.BackgroundColor3 == Color3.fromRGB(70, 70, 100) then
                        aba.Frame.Visible = true
                    end
                end
            end)
        end
    end)

    -- Painel lateral com transparência e gradiente
    local sidePanel = Instance.new("Frame")
    sidePanel.Size = UDim2.new(0, 90, 1, -45)
    sidePanel.Position = UDim2.new(0, 0, 0, 45)
    sidePanel.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    sidePanel.BackgroundTransparency = 0.15
    sidePanel.BorderSizePixel = 0
    sidePanel.ClipsDescendants = true  -- Importante: impede vazamento
    sidePanel.Parent = mainFrame

    local sideGradient = Instance.new("UIGradient")
    sideGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 40)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 15, 25))
    }
    sideGradient.Rotation = 180
    sideGradient.Parent = sidePanel

    -- Linha divisória sutil
    local divider = Instance.new("Frame")
    divider.Size = UDim2.new(0, 1, 1, 0)
    divider.Position = UDim2.new(1, 0, 0, 0)
    divider.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
    divider.BackgroundTransparency = 0.7
    divider.BorderSizePixel = 0
    divider.Parent = sidePanel

    -- Título "ABAS" com efeito glow
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

    -- Container para as abas com scroll
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

    -- Foto do jogador com efeito
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

    local playerLabel = Instance.new("TextLabel")
    playerLabel.Size = UDim2.new(1, -10, 0, 15)
    playerLabel.Position = UDim2.new(0, 5, 1, -20)
    playerLabel.Text = "FOTO DO JOGADOR"
    playerLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
    playerLabel.BackgroundTransparency = 1
    playerLabel.Font = Enum.Font.Gotham
    playerLabel.TextSize = 9
    playerLabel.TextXAlignment = Enum.TextXAlignment.Center
    playerLabel.Parent = sidePanel

    -- Área de conteúdo com transparência
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -90, 1, -45)
    contentFrame.Position = UDim2.new(0, 90, 0, 45)
    contentFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    contentFrame.BackgroundTransparency = 0.2
    contentFrame.BorderSizePixel = 0
    contentFrame.ClipsDescendants = true  -- Importante: impede vazamento
    contentFrame.Parent = mainFrame

    local contentGradient = Instance.new("UIGradient")
    contentGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(35, 35, 50)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 40))
    }
    contentGradient.Rotation = 45
    contentGradient.Parent = contentFrame

    -- Título da aba selecionada com efeito
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

        -- Botão da aba com efeitos melhorados
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

        -- Efeitos hover no botão da aba
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

        -- Frame de conteúdo da aba
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
            -- Resetar cor de todas as abas
            for _, other in pairs(window.Abas) do
                TweenService:Create(other.Button, TweenInfo.new(0.3), {
                    BackgroundColor3 = Color3.fromRGB(40, 40, 60),
                    BackgroundTransparency = 0.2
                }):Play()
                TweenService:Create(other.Button:FindFirstChild("UIStroke"), TweenInfo.new(0.3), {Transparency = 0.8}):Play()
                other.Frame.Visible = false
            end
            -- Ativar aba atual
            TweenService:Create(btn, TweenInfo.new(0.3), {
                BackgroundColor3 = Color3.fromRGB(70, 70, 100),
                BackgroundTransparency = 0.1
            }):Play()
            TweenService:Create(btnStroke, TweenInfo.new(0.3), {Transparency = 0.3}):Play()
            abaFrame.Visible = true
            
            -- Efeito de fade no título
            abaTitle.TextTransparency = 1
            abaTitle.Text = nome:upper()
            TweenService:Create(abaTitle, TweenInfo.new(0.5), {TextTransparency = 0}):Play()
        end)

        -- Se é a primeira aba, ativá-la automaticamente
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

            local bGradient = Instance.new("UIGradient")
            bGradient.Color = ColorSequence.new{
                ColorSequenceKeypoint.new(0, Color3.fromRGB(70, 70, 100)),
                ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 50, 80))
            }
            bGradient.Rotation = 90
            bGradient.Parent = b

            b.MouseEnter:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
                TweenService:Create(bStroke, TweenInfo.new(0.2), {Transparency = 0.4}):Play()
            end)

            b.MouseLeave:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.2}):Play()
                TweenService:Create(bStroke, TweenInfo.new(0.2), {Transparency = 0.7}):Play()
            end)
            
            b.MouseButton1Click:Connect(function()
                local tween1 = TweenService:Create(b, TweenInfo.new(0.1), {
                    BackgroundTransparency = 0.05,
                    Size = UDim2.new(1, -18, 0, 38)
                })
                tween1:Play()
                
                spawn(function()
                    wait(0.1)
                    local tween2 = TweenService:Create(b, TweenInfo.new(0.2), {
                        BackgroundTransparency = 0.2,
                        Size = UDim2.new(1, -20, 0, 40)
                    })
                    tween2:Play()
                end)
                
                if callback then callback() end
            end)
        end

        function aba:CreateToggle(txt, default, callback)
            elementCount = elementCount + 1
            local toggleFrame = Instance.new("Frame")
            toggleFrame.Size = UDim2.new(1, -20, 0, 40)
            toggleFrame.Position = UDim2.new(0, 10, 0, 0)
            toggleFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            toggleFrame.BackgroundTransparency = 0.3
            toggleFrame.BorderSizePixel = 0
            toggleFrame.Parent = abaFrame
            toggleFrame.LayoutOrder = elementCount

            local toggleCorner = Instance.new("UICorner")
            toggleCorner.CornerRadius = UDim.new(0, 8)
            toggleCorner.Parent = toggleFrame

            local toggleStroke = Instance.new("UIStroke")
            toggleStroke.Color = Color3.fromRGB(100, 200, 255)
            toggleStroke.Transparency = 0.8
            toggleStroke.Thickness = 1
            toggleStroke.Parent = toggleFrame

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
            toggle.BackgroundTransparency = 0.2
            toggle.BorderSizePixel = 0
            toggle.Parent = toggleFrame

            local toggleBtnCorner = Instance.new("UICorner")
            toggleBtnCorner.CornerRadius = UDim.new(0, 11)
            toggleBtnCorner.Parent = toggle

            local toggleBtnStroke = Instance.new("UIStroke")
            toggleBtnStroke.Color = Color3.fromRGB(255, 255, 255)
            toggleBtnStroke.Transparency = 0.6
            toggleBtnStroke.Thickness = 1
            toggleBtnStroke.Parent = toggle

            local circle = Instance.new("Frame")
            circle.Size = UDim2.new(0, 18, 0, 18)
            circle.Position = default and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
            circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            circle.BorderSizePixel = 0
            circle.Parent = toggle

            local circleCorner = Instance.new("UICorner")
            circleCorner.CornerRadius = UDim.new(0, 9)
            circleCorner.Parent = circle

            local circleStroke = Instance.new("UIStroke")
            circleStroke.Color = Color3.fromRGB(200, 200, 200)
            circleStroke.Transparency = 0.5
            circleStroke.Thickness = 1
            circleStroke.Parent = circle

            local toggled = default or false

            toggle.MouseButton1Click:Connect(function()
                toggled = not toggled
                local newPos = toggled and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
                local newColor = toggled and Color3.fromRGB(50, 180, 50) or Color3.fromRGB(80, 80, 80)
                
                local tween1 = TweenService:Create(circle, TweenInfo.new(0.3, Enum.EasingStyle.Back), {Position = newPos})
                local tween2 = TweenService:Create(toggle, TweenInfo.new(0.3), {BackgroundColor3 = newColor})
                tween1:Play()
                tween2:Play()
                
                if callback then callback(toggled) end
            end)

            return {
                SetValue = function(self, value)
                    toggled = value
                    local newPos = toggled and UDim2.new(0, 25, 0, 2) or UDim2.new(0, 2, 0, 2)
                    local newColor = toggled and Color3.fromRGB(50, 180, 50) or Color3.fromRGB(80, 80, 80)
                    local tween1 = TweenService:Create(circle, TweenInfo.new(0.3), {Position = newPos})
                    local tween2 = TweenService:Create(toggle, TweenInfo.new(0.3), {BackgroundColor3 = newColor})
                    tween1:Play()
                    tween2:Play()
                end
            }
        end

        function aba:CreateSlider(txt, min, max, default, callback)
            elementCount = elementCount + 1
            local sliderFrame = Instance.new("Frame")
            sliderFrame.Size = UDim2.new(1, -20, 0, 55)
            sliderFrame.Position = UDim2.new(0, 10, 0, 0)
            sliderFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 80)
            sliderFrame.BackgroundTransparency = 0.3
            sliderFrame.BorderSizePixel = 0
            sliderFrame.Parent = abaFrame
            sliderFrame.LayoutOrder = elementCount

            local sliderCorner = Instance.new("UICorner")
            sliderCorner.CornerRadius = UDim.new(0, 8)
            sliderCorner.Parent = sliderFrame

            local sliderStroke = Instance.new("UIStroke")
            sliderStroke.Color = Color3.fromRGB(100, 200, 255)
            sliderStroke.Transparency = 0.8
            sliderStroke.Thickness = 1
            sliderStroke.Parent = sliderFrame

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
            sliderBar.BackgroundTransparency = 0.3
            sliderBar.BorderSizePixel = 0
            sliderBar.Parent = sliderFrame

            local barCorner = Instance.new("UICorner")
            barCorner.CornerRadius = UDim.new(0, 3)
            barCorner.Parent = sliderBar

            local barStroke = Instance.new("UIStroke")
            barStroke.Color = Color3.fromRGB(100, 200, 255)
            barStroke.Transparency = 0.6
            barStroke.Thickness = 1
            barStroke.Parent = sliderBar

            local fillBar = Instance.new("Frame")
            fillBar.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
            fillBar.Position = UDim2.new(0, 0, 0, 0)
            fillBar.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
            fillBar.BackgroundTransparency = 0.2
            fillBar.BorderSizePixel = 0
            fillBar.Parent = sliderBar

            local fillCorner = Instance.new("UICorner")
            fillCorner.CornerRadius = UDim.new(0, 3)
            fillCorner.Parent = fillBar

            local fillGradient = Instance.new("UIGradient")
            fillGradient.Color = ColorSequence.new{
                ColorSequenceKeypoint.new(0, Color3.fromRGB(120, 220, 255)),
                ColorSequenceKeypoint.new(1, Color3.fromRGB(80, 180, 255))
            }
            fillGradient.Parent = fillBar

            local slider = Instance.new("TextButton")
            slider.Size = UDim2.new(0, 20, 0, 20)
            slider.Position = UDim2.new((default - min) / (max - min), -10, 0, -7)
            slider.Text = ""
            slider.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            slider.BackgroundTransparency = 0.1
            slider.BorderSizePixel = 0
            slider.Parent = sliderBar

            local sliderBtnCorner = Instance.new("UICorner")
            sliderBtnCorner.CornerRadius = UDim.new(0, 10)
            sliderBtnCorner.Parent = slider

            local sliderBtnStroke = Instance.new("UIStroke")
            sliderBtnStroke.Color = Color3.fromRGB(100, 200, 255)
            sliderBtnStroke.Transparency = 0.4
            sliderBtnStroke.Thickness = 2
            sliderBtnStroke.Parent = slider

            local currentValue = default
            local dragging = false

            slider.MouseEnter:Connect(function()
                TweenService:Create(slider, TweenInfo.new(0.2), {
                    Size = UDim2.new(0, 24, 0, 24),
                    Position = UDim2.new((currentValue - min) / (max - min), -12, 0, -9)
                }):Play()
                TweenService:Create(sliderBtnStroke, TweenInfo.new(0.2), {Transparency = 0.2}):Play()
            end)

            slider.MouseLeave:Connect(function()
                if not dragging then
                    TweenService:Create(slider, TweenInfo.new(0.2), {
                        Size = UDim2.new(0, 20, 0, 20),
                        Position = UDim2.new((currentValue - min) / (max - min), -10, 0, -7)
                    }):Play()
                    TweenService:Create(sliderBtnStroke, TweenInfo.new(0.2), {Transparency = 0.4}):Play()
                end
            end)

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
                    
                    local fillTween = TweenService:Create(fillBar, TweenInfo.new(0.1), {Size = UDim2.new(relativePos, 0, 1, 0)})
                    local sliderTween = TweenService:Create(slider, TweenInfo.new(0.1), {Position = UDim2.new(relativePos, -12, 0, -9)})
                    fillTween:Play()
                    sliderTween:Play()
                    
                    if callback then callback(currentValue) end
                end
            end)

            UserInputService.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = false
                    TweenService:Create(slider, TweenInfo.new(0.2), {
                        Size = UDim2.new(0, 20, 0, 20),
                        Position = UDim2.new((currentValue - min) / (max - min), -10, 0, -7)
                    }):Play()
                    TweenService:Create(sliderBtnStroke, TweenInfo.new(0.2), {Transparency = 0.4}):Play()
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

    return window
end

return VixBlib
