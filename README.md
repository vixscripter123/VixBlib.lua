--// VixBlib - Biblioteca de UI personalizada
--// Feito por Vix Scripter

local VixBlib = {}

-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Variáveis globais
VixBlib.Windows = {}

-- Função principal de criar janela
function VixBlib:CreateWindow(Config)
    local Nome = Config.Nome or "Vix Brainrot"
    local Tema = Config.Tema or "Dark"

    -- ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "VixBlibUI"
    ScreenGui.Parent = game:GetService("CoreGui")

    -- Frame principal
    local Main = Instance.new("Frame")
    Main.Size = UDim2.new(0, 600, 0, 400)
    Main.Position = UDim2.new(0.5, -300, 0.5, -200)
    Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    Main.BackgroundTransparency = 0.1
    Main.BorderSizePixel = 0
    Main.AnchorPoint = Vector2.new(0.5, 0.5)
    Main.Parent = ScreenGui

    -- Cantos arredondados
    Instance.new("UICorner", Main)

    -- Título
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -60, 0, 40)
    Title.BackgroundTransparency = 1
    Title.Text = Nome
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.Parent = Main

    -- Botão Fechar
    local Close = Instance.new("TextButton")
    Close.Size = UDim2.new(0, 40, 0, 40)
    Close.Position = UDim2.new(1, -40, 0, 0)
    Close.BackgroundTransparency = 1
    Close.Text = "X"
    Close.TextColor3 = Color3.fromRGB(200, 50, 50)
    Close.Font = Enum.Font.GothamBold
    Close.TextSize = 20
    Close.Parent = Main
    Close.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)

    -- Botão Minimizar
    local Minimize = Instance.new("TextButton")
    Minimize.Size = UDim2.new(0, 40, 0, 40)
    Minimize.Position = UDim2.new(1, -80, 0, 0)
    Minimize.BackgroundTransparency = 1
    Minimize.Text = "-"
    Minimize.TextColor3 = Color3.fromRGB(200, 200, 200)
    Minimize.Font = Enum.Font.GothamBold
    Minimize.TextSize = 30
    Minimize.Parent = Main

    local Minimized = false
    Minimize.MouseButton1Click:Connect(function()
        Minimized = not Minimized
        if Minimized then
            Main.Size = UDim2.new(0, 600, 0, 40)
        else
            Main.Size = UDim2.new(0, 600, 0, 400)
        end
    end)

    -- Sidebar de abas
    local Sidebar = Instance.new("Frame")
    Sidebar.Size = UDim2.new(0, 120, 1, -40)
    Sidebar.Position = UDim2.new(0, 0, 0, 40)
    Sidebar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    Sidebar.BorderSizePixel = 0
    Sidebar.Parent = Main
    Instance.new("UICorner", Sidebar)

    -- Área principal
    local Container = Instance.new("Frame")
    Container.Size = UDim2.new(1, -120, 1, -40)
    Container.Position = UDim2.new(0, 120, 0, 40)
    Container.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    Container.BorderSizePixel = 0
    Container.Parent = Main
    Instance.new("UICorner", Container)

    -- Foto do jogador
    local PlayerImage = Instance.new("ImageLabel")
    PlayerImage.Size = UDim2.new(0, 60, 0, 60)
    PlayerImage.Position = UDim2.new(0, 10, 1, -70)
    PlayerImage.BackgroundTransparency = 1
    PlayerImage.Image = "rbxthumb://type=AvatarHeadShot&id=" .. LocalPlayer.UserId .. "&w=150&h=150"
    PlayerImage.Parent = Sidebar
    Instance.new("UICorner", PlayerImage)

    -- Funções das abas
    local Tabs = {}
    function Tabs:CreateTab(NomeAba)
        local TabButton = Instance.new("TextButton")
        TabButton.Size = UDim2.new(1, 0, 0, 40)
        TabButton.BackgroundTransparency = 1
        TabButton.Text = NomeAba
        TabButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        TabButton.Font = Enum.Font.Gotham
        TabButton.TextSize = 16
        TabButton.Parent = Sidebar

        local TabFrame = Instance.new("ScrollingFrame")
        TabFrame.Size = UDim2.new(1, 0, 1, 0)
        TabFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
        TabFrame.BackgroundTransparency = 1
        TabFrame.Visible = false
        TabFrame.Parent = Container

        -- Layout dos elementos
        local Layout = Instance.new("UIListLayout")
        Layout.Parent = TabFrame
        Layout.Padding = UDim.new(0, 10)

        TabButton.MouseButton1Click:Connect(function()
            for _, v in pairs(Container:GetChildren()) do
                if v:IsA("ScrollingFrame") then
                    v.Visible = false
                end
            end
            TabFrame.Visible = true
        end)

        local Elements = {}

        function Elements:CreateButton(Nome, Callback)
            local Btn = Instance.new("TextButton")
            Btn.Size = UDim2.new(1, -20, 0, 40)
            Btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            Btn.Text = Nome
            Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            Btn.Font = Enum.Font.Gotham
            Btn.TextSize = 16
            Btn.Parent = TabFrame
            Instance.new("UICorner", Btn)

            Btn.MouseButton1Click:Connect(Callback)
        end

        function Elements:CreateToggle(Nome, ValorInicial, Callback)
            local Toggle = Instance.new("TextButton")
            Toggle.Size = UDim2.new(1, -20, 0, 40)
            Toggle.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            Toggle.Text = Nome .. ": " .. tostring(ValorInicial)
            Toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
            Toggle.Font = Enum.Font.Gotham
            Toggle.TextSize = 16
            Toggle.Parent = TabFrame
            Instance.new("UICorner", Toggle)

            local Ativado = ValorInicial
            Toggle.MouseButton1Click:Connect(function()
                Ativado = not Ativado
                Toggle.Text = Nome .. ": " .. tostring(Ativado)
                Callback(Ativado)
            end)
        end

        function Elements:CreateSlider(Nome, Min, Max, ValorInicial, Callback)
            local SliderFrame = Instance.new("Frame")
            SliderFrame.Size = UDim2.new(1, -20, 0, 60)
            SliderFrame.BackgroundTransparency = 1
            SliderFrame.Parent = TabFrame

            local SliderLabel = Instance.new("TextLabel")
            SliderLabel.Size = UDim2.new(1, 0, 0, 20)
            SliderLabel.BackgroundTransparency = 1
            SliderLabel.Text = Nome .. ": " .. ValorInicial
            SliderLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            SliderLabel.Font = Enum.Font.Gotham
            SliderLabel.TextSize = 14
            SliderLabel.Parent = SliderFrame

            local Bar = Instance.new("Frame")
            Bar.Size = UDim2.new(1, -20, 0, 10)
            Bar.Position = UDim2.new(0, 10, 0, 30)
            Bar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            Bar.Parent = SliderFrame
            Instance.new("UICorner", Bar)

            local Fill = Instance.new("Frame")
            Fill.Size = UDim2.new((ValorInicial - Min) / (Max - Min), 0, 1, 0)
            Fill.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
            Fill.Parent = Bar
            Instance.new("UICorner", Fill)

            local dragging = false
            Bar.InputBegan:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = true
                end
            end)
            UserInputService.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = false
                end
            end)
            UserInputService.InputChanged:Connect(function(input)
                if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                    local pos = math.clamp((input.Position.X - Bar.AbsolutePosition.X) / Bar.AbsoluteSize.X, 0, 1)
                    local value = math.floor(Min + (Max - Min) * pos)
                    Fill.Size = UDim2.new(pos, 0, 1, 0)
                    SliderLabel.Text = Nome .. ": " .. value
                    Callback(value)
                end
            end)
        end

        return Elements
    end

    return Tabs
end

return VixBlib
