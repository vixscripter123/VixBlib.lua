--[=[
 d888b  db    db d888888b      .d888b.      db      db    db  .d8b.  
88' Y8b 88    88   `88'        VP  `8D      88      88    88 d8' `8b 
88      88    88    88            odD'      88      88    88 88ooo88 
88  ooo 88    88    88          .88'        88      88    88 88~~~88 
88. ~8~ 88b  d88   .88.        j88.         88booo. 88b  d88 88   88    @uniquadev
 Y888P  ~Y8888P' Y888888P      888888D      Y88888P ~Y8888P' YP   YP  SCRIPT HUB

Biblioteca Moderna v4.0 - Design Português e Intuitivo
]=]

local Library = {}

-- Serviços
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")

-- Tema de Cores Moderno
local Cores = {
    FundoPrincipal = Color3.fromRGB(15, 15, 20),
    FundoSecundario = Color3.fromRGB(20, 22, 28),
    BarraSuperior = Color3.fromRGB(25, 27, 35),
    Elemento = Color3.fromRGB(30, 32, 40),
    Destaque = Color3.fromRGB(88, 101, 242),
    DestaqueBrilho = Color3.fromRGB(115, 125, 255),
    TextoBranco = Color3.fromRGB(255, 255, 255),
    TextoCinza = Color3.fromRGB(180, 185, 200),
    Sucesso = Color3.fromRGB(67, 181, 129),
    Erro = Color3.fromRGB(240, 71, 71),
    Aviso = Color3.fromRGB(250, 166, 26),
}

-- Utilitários
local function CriarArredondamento(parent, raio)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, raio or 10)
    corner.Parent = parent
    return corner
end

local function CriarSombra(parent, cor)
    local sombra = Instance.new("ImageLabel")
    sombra.Name = "Sombra"
    sombra.BackgroundTransparency = 1
    sombra.Position = UDim2.new(0, -15, 0, -15)
    sombra.Size = UDim2.new(1, 30, 1, 30)
    sombra.ZIndex = parent.ZIndex - 1
    sombra.Image = "rbxassetid://6015897843"
    sombra.ImageColor3 = cor or Color3.fromRGB(0, 0, 0)
    sombra.ImageTransparency = 0.5
    sombra.ScaleType = Enum.ScaleType.Slice
    sombra.SliceCenter = Rect.new(49, 49, 450, 450)
    sombra.Parent = parent
    return sombra
end

local function CriarGradiente(parent, cor1, cor2, rotacao)
    local gradiente = Instance.new("UIGradient")
    gradiente.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, cor1),
        ColorSequenceKeypoint.new(1, cor2)
    }
    gradiente.Rotation = rotacao or 90
    gradiente.Parent = parent
    return gradiente
end

local function AnimarBotao(botao)
    botao.MouseEnter:Connect(function()
        TweenService:Create(botao, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
            BackgroundColor3 = Cores.DestaqueBrilho
        }):Play()
    end)
    
    botao.MouseLeave:Connect(function()
        TweenService:Create(botao, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
            BackgroundColor3 = Cores.Destaque
        }):Play()
    end)
    
    botao.MouseButton1Down:Connect(function()
        TweenService:Create(botao, TweenInfo.new(0.1, Enum.EasingStyle.Quad), {
            Size = UDim2.new(botao.Size.X.Scale, botao.Size.X.Offset, botao.Size.Y.Scale - 0.02, botao.Size.Y.Offset)
        }):Play()
    end)
    
    botao.MouseButton1Up:Connect(function()
        TweenService:Create(botao, TweenInfo.new(0.1, Enum.EasingStyle.Quad), {
            Size = UDim2.new(botao.Size.X.Scale, botao.Size.X.Offset, botao.Size.Y.Scale + 0.02, botao.Size.Y.Offset)
        }):Play()
    end)
end

local function TornarArrastavel(frame, areaArrastar)
    local arrastando, dragInput, dragStart, startPos
    
    areaArrastar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = true
            dragStart = input.Position
            startPos = frame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    arrastando = false
                end
            end)
        end
    end)
    
    areaArrastar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and arrastando then
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

-- Sistema de Key
function Library:CriarSistemaKey(configuracao)
    configuracao = configuracao or {}
    configuracao.Titulo = configuracao.Titulo or "Sistema de Key"
    configuracao.Descricao = configuracao.Descricao or "Insira a key para continuar"
    configuracao.Nota = configuracao.Nota or "Pegue a key no nosso Discord"
    configuracao.KeyCorreta = configuracao.KeyCorreta or "KeyPadrao123"
    configuracao.SalvarKey = configuracao.SalvarKey or true
    configuracao.NomeArquivo = configuracao.NomeArquivo or "ChaveScript"
    configuracao.LinkKey = configuracao.LinkKey or nil
    
    local keyValida = false
    
    -- Verificar key salva
    if configuracao.SalvarKey then
        local sucesso, keySalva = pcall(function()
            return readfile(configuracao.NomeArquivo .. ".key")
        end)
        
        if sucesso and keySalva == configuracao.KeyCorreta then
            keyValida = true
            if configuracao.Callback then
                task.spawn(configuracao.Callback, true)
            end
            return
        end
    end
    
    -- Criar GUI da Key
    local KeyGui = Instance.new("ScreenGui")
    KeyGui.Name = "SistemaKey"
    KeyGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    if syn and syn.protect_gui then
        syn.protect_gui(KeyGui)
        KeyGui.Parent = CoreGui
    elseif gethui then
        KeyGui.Parent = gethui()
    else
        KeyGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    -- Fundo escuro
    local Fundo = Instance.new("Frame")
    Fundo.Name = "Fundo"
    Fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Fundo.BackgroundTransparency = 0.5
    Fundo.Size = UDim2.new(1, 0, 1, 0)
    Fundo.Parent = KeyGui
    
    -- Frame principal
    local FrameKey = Instance.new("Frame")
    FrameKey.Name = "FrameKey"
    FrameKey.BackgroundColor3 = Cores.FundoPrincipal
    FrameKey.Size = UDim2.new(0, 450, 0, 300)
    FrameKey.Position = UDim2.new(0.5, -225, 0.5, -150)
    FrameKey.Parent = KeyGui
    CriarArredondamento(FrameKey, 15)
    CriarSombra(FrameKey, Color3.fromRGB(0, 0, 0))
    
    local Borda = Instance.new("UIStroke")
    Borda.Color = Cores.Destaque
    Borda.Thickness = 2
    Borda.Transparency = 0.5
    Borda.Parent = FrameKey
    
    -- Header
    local Header = Instance.new("Frame")
    Header.BackgroundColor3 = Cores.BarraSuperior
    Header.Size = UDim2.new(1, 0, 0, 60)
    Header.Parent = FrameKey
    CriarArredondamento(Header, 15)
    CriarGradiente(Header, Cores.BarraSuperior, Cores.FundoSecundario, 90)
    
    local Icone = Instance.new("TextLabel")
    Icone.Text = "🔐"
    Icone.Font = Enum.Font.GothamBold
    Icone.TextSize = 24
    Icone.TextColor3 = Cores.Destaque
    Icone.BackgroundTransparency = 1
    Icone.Position = UDim2.new(0, 20, 0, 15)
    Icone.Size = UDim2.new(0, 30, 0, 30)
    Icone.Parent = Header
    
    local Titulo = Instance.new("TextLabel")
    Titulo.Text = configuracao.Titulo
    Titulo.Font = Enum.Font.GothamBold
    Titulo.TextSize = 20
    Titulo.TextColor3 = Cores.TextoBranco
    Titulo.BackgroundTransparency = 1
    Titulo.Position = UDim2.new(0, 60, 0, 0)
    Titulo.Size = UDim2.new(1, -60, 1, 0)
    Titulo.TextXAlignment = Enum.TextXAlignment.Left
    Titulo.Parent = Header
    
    -- Descrição
    local Descricao = Instance.new("TextLabel")
    Descricao.Text = configuracao.Descricao
    Descricao.Font = Enum.Font.Gotham
    Descricao.TextSize = 14
    Descricao.TextColor3 = Cores.TextoCinza
    Descricao.BackgroundTransparency = 1
    Descricao.Position = UDim2.new(0, 20, 0, 75)
    Descricao.Size = UDim2.new(1, -40, 0, 30)
    Descricao.TextXAlignment = Enum.TextXAlignment.Left
    Descricao.Parent = FrameKey
    
    -- Input da Key
    local InputKey = Instance.new("TextBox")
    InputKey.PlaceholderText = "Digite sua key aqui..."
    InputKey.Text = ""
    InputKey.Font = Enum.Font.Gotham
    InputKey.TextSize = 14
    InputKey.TextColor3 = Cores.TextoBranco
    InputKey.BackgroundColor3 = Cores.Elemento
    InputKey.Position = UDim2.new(0, 20, 0, 115)
    InputKey.Size = UDim2.new(1, -40, 0, 45)
    InputKey.Parent = FrameKey
    CriarArredondamento(InputKey, 10)
    
    local InputPadding = Instance.new("UIPadding")
    InputPadding.PaddingLeft = UDim.new(0, 15)
    InputPadding.Parent = InputKey
    
    -- Botão Verificar
    local BotaoVerificar = Instance.new("TextButton")
    BotaoVerificar.Text = "✓ Verificar Key"
    BotaoVerificar.Font = Enum.Font.GothamBold
    BotaoVerificar.TextSize = 15
    BotaoVerificar.TextColor3 = Cores.TextoBranco
    BotaoVerificar.BackgroundColor3 = Cores.Destaque
    BotaoVerificar.Position = UDim2.new(0, 20, 0, 175)
    BotaoVerificar.Size = UDim2.new(0.48, -15, 0, 45)
    BotaoVerificar.Parent = FrameKey
    CriarArredondamento(BotaoVerificar, 10)
    AnimarBotao(BotaoVerificar)
    
    -- Botão Pegar Key
    local BotaoPegarKey = Instance.new("TextButton")
    BotaoPegarKey.Text = "📋 Pegar Key"
    BotaoPegarKey.Font = Enum.Font.GothamBold
    BotaoPegarKey.TextSize = 15
    BotaoPegarKey.TextColor3 = Cores.TextoBranco
    BotaoPegarKey.BackgroundColor3 = Cores.Elemento
    BotaoPegarKey.Position = UDim2.new(0.52, 5, 0, 175)
    BotaoPegarKey.Size = UDim2.new(0.48, -15, 0, 45)
    BotaoPegarKey.Parent = FrameKey
    CriarArredondamento(BotaoPegarKey, 10)
    
    -- Nota
    local Nota = Instance.new("TextLabel")
    Nota.Text = configuracao.Nota
    Nota.Font = Enum.Font.Gotham
    Nota.TextSize = 12
    Nota.TextColor3 = Cores.TextoCinza
    Nota.BackgroundTransparency = 1
    Nota.Position = UDim2.new(0, 20, 0, 235)
    Nota.Size = UDim2.new(1, -40, 0, 25)
    Nota.Parent = FrameKey
    
    -- Status
    local Status = Instance.new("TextLabel")
    Status.Text = ""
    Status.Font = Enum.Font.GothamBold
    Status.TextSize = 13
    Status.TextColor3 = Cores.Erro
    Status.BackgroundTransparency = 1
    Status.Position = UDim2.new(0, 20, 0, 265)
    Status.Size = UDim2.new(1, -40, 0, 25)
    Status.Parent = FrameKey
    
    TornarArrastavel(FrameKey, Header)
    
    BotaoVerificar.MouseButton1Click:Connect(function()
        local keyDigitada = InputKey.Text
        
        if keyDigitada == configuracao.KeyCorreta then
            Status.Text = "✓ Key válida! Carregando..."
            Status.TextColor3 = Cores.Sucesso
            keyValida = true
            
            if configuracao.SalvarKey then
                pcall(function()
                    writefile(configuracao.NomeArquivo .. ".key", keyDigitada)
                end)
            end
            
            task.wait(1)
            KeyGui:Destroy()
            
            if configuracao.Callback then
                task.spawn(configuracao.Callback, true)
            end
        else
            Status.Text = "✗ Key inválida! Tente novamente"
            Status.TextColor3 = Cores.Erro
            
            -- Animação de shake
            local posOriginal = FrameKey.Position
            for i = 1, 3 do
                FrameKey.Position = posOriginal + UDim2.new(0, 15, 0, 0)
                task.wait(0.05)
                FrameKey.Position = posOriginal - UDim2.new(0, 15, 0, 0)
                task.wait(0.05)
            end
            FrameKey.Position = posOriginal
        end
    end)
    
    BotaoPegarKey.MouseButton1Click:Connect(function()
        if configuracao.LinkKey then
            setclipboard(configuracao.LinkKey)
            Status.Text = "✓ Link copiado para área de transferência!"
            Status.TextColor3 = Cores.Sucesso
        else
            Status.Text = "Link não disponível"
            Status.TextColor3 = Cores.TextoCinza
        end
    end)
end

-- Criar Janela
function Library:CriarJanela(configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Script Hub"
    configuracao.Tamanho = configuracao.Tamanho or {650, 480}
    
    local Janela = {
        Abas = {},
        AbaAtual = nil,
        Elementos = {}
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
    
    Janela.ScreenGui = ScreenGui
    
    -- Frame Principal
    local FramePrincipal = Instance.new("Frame")
    FramePrincipal.Name = "Principal"
    FramePrincipal.BackgroundColor3 = Cores.FundoPrincipal
    FramePrincipal.Size = UDim2.new(0, configuracao.Tamanho[1], 0, configuracao.Tamanho[2])
    FramePrincipal.Position = UDim2.new(0.5, -configuracao.Tamanho[1]/2, 0.5, -configuracao.Tamanho[2]/2)
    FramePrincipal.Parent = ScreenGui
    CriarArredondamento(FramePrincipal, 16)
    CriarSombra(FramePrincipal, Color3.fromRGB(0, 0, 0))
    
    local Borda = Instance.new("UIStroke")
    Borda.Color = Cores.Destaque
    Borda.Thickness = 2
    Borda.Transparency = 0.6
    Borda.Parent = FramePrincipal
    
    -- Barra Superior
    local BarraSuperior = Instance.new("Frame")
    BarraSuperior.Name = "BarraSuperior"
    BarraSuperior.BackgroundColor3 = Cores.BarraSuperior
    BarraSuperior.Size = UDim2.new(1, 0, 0, 50)
    BarraSuperior.Parent = FramePrincipal
    CriarArredondamento(BarraSuperior, 16)
    CriarGradiente(BarraSuperior, Cores.BarraSuperior, Cores.FundoSecundario, 90)
    
    local Logo = Instance.new("TextLabel")
    Logo.Text = "🎮"
    Logo.Font = Enum.Font.GothamBold
    Logo.TextSize = 20
    Logo.TextColor3 = Cores.Destaque
    Logo.BackgroundTransparency = 1
    Logo.Position = UDim2.new(0, 15, 0, 12)
    Logo.Size = UDim2.new(0, 26, 0, 26)
    Logo.Parent = BarraSuperior
    
    local Titulo = Instance.new("TextLabel")
    Titulo.Text = configuracao.Nome
    Titulo.Font = Enum.Font.GothamBold
    Titulo.TextSize = 17
    Titulo.TextColor3 = Cores.TextoBranco
    Titulo.BackgroundTransparency = 1
    Titulo.Position = UDim2.new(0, 50, 0, 0)
    Titulo.Size = UDim2.new(0.5, 0, 1, 0)
    Titulo.TextXAlignment = Enum.TextXAlignment.Left
    Titulo.Parent = BarraSuperior
    
    -- Botão Minimizar
    local BotaoMinimizar = Instance.new("TextButton")
    BotaoMinimizar.Text = "─"
    BotaoMinimizar.Font = Enum.Font.GothamBold
    BotaoMinimizar.TextSize = 18
    BotaoMinimizar.TextColor3 = Cores.TextoBranco
    BotaoMinimizar.BackgroundColor3 = Cores.Elemento
    BotaoMinimizar.Size = UDim2.new(0, 38, 0, 32)
    BotaoMinimizar.Position = UDim2.new(1, -85, 0.5, -16)
    BotaoMinimizar.Parent = BarraSuperior
    CriarArredondamento(BotaoMinimizar, 8)
    
    -- Botão Fechar
    local BotaoFechar = Instance.new("TextButton")
    BotaoFechar.Text = "✕"
    BotaoFechar.Font = Enum.Font.GothamBold
    BotaoFechar.TextSize = 17
    BotaoFechar.TextColor3 = Cores.TextoBranco
    BotaoFechar.BackgroundColor3 = Cores.Erro
    BotaoFechar.Size = UDim2.new(0, 38, 0, 32)
    BotaoFechar.Position = UDim2.new(1, -42, 0.5, -16)
    BotaoFechar.Parent = BarraSuperior
    CriarArredondamento(BotaoFechar, 8)
    
    -- Container de Abas
    local ContainerAbas = Instance.new("Frame")
    ContainerAbas.Name = "Abas"
    ContainerAbas.BackgroundColor3 = Cores.FundoSecundario
    ContainerAbas.Size = UDim2.new(0, 165, 1, -60)
    ContainerAbas.Position = UDim2.new(0, 5, 0, 55)
    ContainerAbas.Parent = FramePrincipal
    CriarArredondamento(ContainerAbas, 12)
    
    local ListaAbas = Instance.new("UIListLayout")
    ListaAbas.Padding = UDim.new(0, 6)
    ListaAbas.SortOrder = Enum.SortOrder.LayoutOrder
    ListaAbas.Parent = ContainerAbas
    
    local PaddingAbas = Instance.new("UIPadding")
    PaddingAbas.PaddingTop = UDim.new(0, 8)
    PaddingAbas.PaddingLeft = UDim.new(0, 8)
    PaddingAbas.PaddingRight = UDim.new(0, 8)
    PaddingAbas.Parent = ContainerAbas
    
    -- Container de Conteúdo
    local ContainerConteudo = Instance.new("Frame")
    ContainerConteudo.Name = "Conteudo"
    ContainerConteudo.BackgroundTransparency = 1
    ContainerConteudo.Size = UDim2.new(1, -180, 1, -60)
    ContainerConteudo.Position = UDim2.new(0, 175, 0, 55)
    ContainerConteudo.Parent = FramePrincipal
    
    TornarArrastavel(FramePrincipal, BarraSuperior)
    
    -- Eventos
    local minimizado = false
    BotaoMinimizar.MouseButton1Click:Connect(function()
        minimizado = not minimizado
        local tamanhoAlvo = minimizado and UDim2.new(0, configuracao.Tamanho[1], 0, 50) or UDim2.new(0, configuracao.Tamanho[1], 0, configuracao.Tamanho[2])
        
        TweenService:Create(FramePrincipal, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {
            Size = tamanhoAlvo
        }):Play()
        
        ContainerAbas.Visible = not minimizado
        ContainerConteudo.Visible = not minimizado
    end)
    
    BotaoFechar.MouseButton1Click:Connect(function()
        TweenService:Create(FramePrincipal, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {
            Size = UDim2.new(0, 0, 0, 0),
            Position = UDim2.new(0.5, 0, 0.5, 0)
        }):Play()
        task.wait(0.3)
        ScreenGui:Destroy()
    end)
    
    -- Criar Aba
    function Janela:Aba(configuracao)
        configuracao = configuracao or {}
        configuracao.Nome = configuracao.Nome or "Nova Aba"
        configuracao.Icone = configuracao.Icone or "📄"
        
        local Aba = {
            Nome = configuracao.Nome,
            Elementos = {}
        }
        
        -- Botão da Aba
        local BotaoAba = Instance.new("TextButton")
        BotaoAba.Name = configuracao.Nome
        BotaoAba.Text = "  " .. configuracao.Icone .. "  " .. configuracao.Nome
        BotaoAba.Font = Enum.Font.GothamBold
        BotaoAba.TextSize = 14
        BotaoAba.TextColor3 = Cores.TextoCinza
        BotaoAba.TextXAlignment = Enum.TextXAlignment.Left
        BotaoAba.BackgroundColor3 = Cores.Elemento
        BotaoAba.Size = UDim2.new(1, 0, 0, 42)
        BotaoAba.Parent = ContainerAbas
        CriarArredondamento(BotaoAba, 10)
        
        Aba.Botao = BotaoAba
        
        -- Conteúdo da Aba
        local ConteudoAba = Instance.new("ScrollingFrame")
        ConteudoAba.Name = configuracao.Nome .. "_Conteudo"
        ConteudoAba.BackgroundTransparency = 1
        ConteudoAba.BorderSizePixel = 0
        ConteudoAba.Size = UDim2.new(1, 0, 1, 0)
        ConteudoAba.ScrollBarThickness = 5
        ConteudoAba.ScrollBarImageColor3 = Cores.Destaque
        ConteudoAba.CanvasSize = UDim2.new(0, 0, 0, 0)
        ConteudoAba.Visible = false
        ConteudoAba.Parent = ContainerConteudo
        
        local LayoutConteudo = Instance.new("UIListLayout")
        LayoutConteudo.Padding = UDim.new(0, 10)
        LayoutConteudo.SortOrder = Enum.SortOrder.LayoutOrder
        LayoutConteudo.Parent = ConteudoAba
        
        local PaddingConteudo = Instance.new("UIPadding")
        PaddingConteudo.PaddingTop = UDim.new(0, 8)
        PaddingConteudo.PaddingLeft = UDim.new(0, 8)
        PaddingConteudo.PaddingRight = UDim.new(0, 8)
        PaddingConteudo.PaddingBottom = UDim.new(0, 8)
        PaddingConteudo.Parent = ConteudoAba
        
        Aba.Conteudo = ConteudoAba
        
        LayoutConteudo:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            ConteudoAba.CanvasSize = UDim2.new(0, 0, 0, LayoutConteudo.AbsoluteContentSize.Y + 16)
        end)
        
        BotaoAba.MouseButton1Click:Connect(function()
            for _, aba in pairs(Janela.Abas) do
                aba.Botao.BackgroundColor3 = Cores.Elemento
                aba.Botao.TextColor3 = Cores.TextoCinza
                aba.Conteudo.Visible = false
            end
            
            BotaoAba.BackgroundColor3 = Cores.Destaque
            BotaoAba.TextColor3 = Cores.TextoBranco
            ConteudoAba.Visible = true
            Janela.AbaAtual = Aba
        end)
        
        table.insert(Janela.Abas, Aba)
        
        if #Janela.Abas == 1 then
            BotaoAba.BackgroundColor3 = Cores.Destaque
            BotaoAba.TextColor3 = Cores.TextoBranco
            ConteudoAba.Visible = true
            Janela.AbaAtual = Aba
        end
        
        return Aba
    end
    
    return Janela
end

-- Criar Botão
function Library:Botao(aba, configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Botão"
    configuracao.Callback = configuracao.Callback or function() end
    
    local FrameBotao = Instance.new("Frame")
    FrameBotao.BackgroundColor3 = Cores.Elemento
    FrameBotao.Size = UDim2.new(1, -16, 0, 48)
    FrameBotao.Parent = aba.Conteudo
    CriarArredondamento(FrameBotao, 10)
    
    local Botao = Instance.new("TextButton")
    Botao.Text = configuracao.Nome
    Botao.Font = Enum.Font.GothamBold
    Botao.TextSize = 14
    Botao.TextColor3 = Cores.TextoBranco
    Botao.BackgroundColor3 = Cores.Destaque
    Botao.Size = UDim2.new(1, -12, 1, -12)
    Botao.Position = UDim2.new(0, 6, 0, 6)
    Botao.Parent = FrameBotao
    CriarArredondamento(Botao, 8)
    CriarGradiente(Botao, Cores.Destaque, Cores.DestaqueBrilho, 45)
    AnimarBotao(Botao)
    
    Botao.MouseButton1Click:Connect(function()
        task.spawn(function()
            pcall(configuracao.Callback)
        end)
    end)
    
    return Botao
end

-- Criar Toggle
function Library:Toggle(aba, configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Toggle"
    configuracao.Padrao = configuracao.Padrao or false
    configuracao.Callback = configuracao.Callback or function(v) end
    
    local FrameToggle = Instance.new("Frame")
    FrameToggle.BackgroundColor3 = Cores.Elemento
    FrameToggle.Size = UDim2.new(1, -16, 0, 48)
    FrameToggle.Parent = aba.Conteudo
    CriarArredondamento(FrameToggle, 10)
    
    local Label = Instance.new("TextLabel")
    Label.Text = configuracao.Nome
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 14
    Label.TextColor3 = Cores.TextoBranco
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 0)
    Label.Size = UDim2.new(0.65, 0, 1, 0)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = FrameToggle
    
    local BotaoToggle = Instance.new("TextButton")
    BotaoToggle.Text = ""
    BotaoToggle.BackgroundColor3 = configuracao.Padrao and Cores.Sucesso or Cores.FundoPrincipal
    BotaoToggle.Size = UDim2.new(0, 56, 0, 28)
    BotaoToggle.Position = UDim2.new(1, -70, 0.5, -14)
    BotaoToggle.Parent = FrameToggle
    CriarArredondamento(BotaoToggle, 14)
    
    local Indicador = Instance.new("Frame")
    Indicador.BackgroundColor3 = Cores.TextoBranco
    Indicador.Size = UDim2.new(0, 22, 0, 22)
    Indicador.Position = configuracao.Padrao and UDim2.new(1, -25, 0.5, -11) or UDim2.new(0, 3, 0.5, -11)
    Indicador.Parent = BotaoToggle
    CriarArredondamento(Indicador, 11)
    
    local ativado = configuracao.Padrao
    
    BotaoToggle.MouseButton1Click:Connect(function()
        ativado = not ativado
        
        TweenService:Create(Indicador, TweenInfo.new(0.25, Enum.EasingStyle.Quad), {
            Position = ativado and UDim2.new(1, -25, 0.5, -11) or UDim2.new(0, 3, 0.5, -11)
        }):Play()
        
        TweenService:Create(BotaoToggle, TweenInfo.new(0.25, Enum.EasingStyle.Quad), {
            BackgroundColor3 = ativado and Cores.Sucesso or Cores.FundoPrincipal
        }):Play()
        
        task.spawn(function()
            pcall(configuracao.Callback, ativado)
        end)
    end)
    
    return {
        Definir = function(valor)
            ativado = valor
            Indicador.Position = ativado and UDim2.new(1, -25, 0.5, -11) or UDim2.new(0, 3, 0.5, -11)
            BotaoToggle.BackgroundColor3 = ativado and Cores.Sucesso or Cores.FundoPrincipal
        end
    }
end

-- Criar Slider
function Library:Slider(aba, configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Slider"
    configuracao.Min = configuracao.Min or 0
    configuracao.Max = configuracao.Max or 100
    configuracao.Padrao = configuracao.Padrao or configuracao.Min
    configuracao.Incremento = configuracao.Incremento or 1
    configuracao.Callback = configuracao.Callback or function(v) end
    
    local FrameSlider = Instance.new("Frame")
    FrameSlider.BackgroundColor3 = Cores.Elemento
    FrameSlider.Size = UDim2.new(1, -16, 0, 65)
    FrameSlider.Parent = aba.Conteudo
    CriarArredondamento(FrameSlider, 10)
    
    local Label = Instance.new("TextLabel")
    Label.Text = configuracao.Nome
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 14
    Label.TextColor3 = Cores.TextoBranco
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 8)
    Label.Size = UDim2.new(0.6, 0, 0, 22)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = FrameSlider
    
    local ValorLabel = Instance.new("TextLabel")
    ValorLabel.Text = tostring(configuracao.Padrao)
    ValorLabel.Font = Enum.Font.GothamBold
    ValorLabel.TextSize = 14
    ValorLabel.TextColor3 = Cores.Destaque
    ValorLabel.BackgroundTransparency = 1
    ValorLabel.Position = UDim2.new(0.65, 0, 0, 8)
    ValorLabel.Size = UDim2.new(0.3, 0, 0, 22)
    ValorLabel.TextXAlignment = Enum.TextXAlignment.Right
    ValorLabel.Parent = FrameSlider
    
    local FundoSlider = Instance.new("Frame")
    FundoSlider.BackgroundColor3 = Cores.FundoPrincipal
    FundoSlider.Position = UDim2.new(0, 15, 1, -22)
    FundoSlider.Size = UDim2.new(1, -30, 0, 8)
    FundoSlider.Parent = FrameSlider
    CriarArredondamento(FundoSlider, 4)
    
    local PreenchimentoSlider = Instance.new("Frame")
    PreenchimentoSlider.BackgroundColor3 = Cores.Destaque
    PreenchimentoSlider.Size = UDim2.new(0, 0, 1, 0)
    PreenchimentoSlider.Parent = FundoSlider
    CriarArredondamento(PreenchimentoSlider, 4)
    CriarGradiente(PreenchimentoSlider, Cores.Destaque, Cores.DestaqueBrilho, 0)
    
    local function AtualizarSlider(input)
        local posX = math.clamp((input.Position.X - FundoSlider.AbsolutePosition.X) / FundoSlider.AbsoluteSize.X, 0, 1)
        local valor = math.floor(configuracao.Min + (configuracao.Max - configuracao.Min) * posX)
        valor = math.floor(valor / configuracao.Incremento + 0.5) * configuracao.Incremento
        valor = math.clamp(valor, configuracao.Min, configuracao.Max)
        
        ValorLabel.Text = tostring(valor)
        PreenchimentoSlider.Size = UDim2.new(posX, 0, 1, 0)
        
        task.spawn(function()
            pcall(configuracao.Callback, valor)
        end)
    end
    
    local arrastando = false
    
    FundoSlider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = true
            AtualizarSlider(input)
        end
    end)
    
    FundoSlider.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            arrastando = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if arrastando and input.UserInputType == Enum.UserInputType.MouseMovement then
            AtualizarSlider(input)
        end
    end)
    
    local percentInicial = (configuracao.Padrao - configuracao.Min) / (configuracao.Max - configuracao.Min)
    PreenchimentoSlider.Size = UDim2.new(percentInicial, 0, 1, 0)
    
    return {
        Definir = function(valor)
            ValorLabel.Text = tostring(valor)
            local percent = (valor - configuracao.Min) / (configuracao.Max - configuracao.Min)
            PreenchimentoSlider.Size = UDim2.new(percent, 0, 1, 0)
        end
    }
end

-- Criar Input
function Library:Input(aba, configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Input"
    configuracao.Placeholder = configuracao.Placeholder or "Digite aqui..."
    configuracao.LimparAoPerder = configuracao.LimparAoPerder or false
    configuracao.Callback = configuracao.Callback or function(v) end
    
    local FrameInput = Instance.new("Frame")
    FrameInput.BackgroundColor3 = Cores.Elemento
    FrameInput.Size = UDim2.new(1, -16, 0, 75)
    FrameInput.Parent = aba.Conteudo
    CriarArredondamento(FrameInput, 10)
    
    local Label = Instance.new("TextLabel")
    Label.Text = configuracao.Nome
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 14
    Label.TextColor3 = Cores.TextoBranco
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 8)
    Label.Size = UDim2.new(1, -30, 0, 22)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = FrameInput
    
    local Input = Instance.new("TextBox")
    Input.PlaceholderText = configuracao.Placeholder
    Input.Text = ""
    Input.Font = Enum.Font.Gotham
    Input.TextSize = 13
    Input.TextColor3 = Cores.TextoBranco
    Input.BackgroundColor3 = Cores.FundoPrincipal
    Input.Position = UDim2.new(0, 15, 0, 38)
    Input.Size = UDim2.new(1, -30, 0, 32)
    Input.TextXAlignment = Enum.TextXAlignment.Left
    Input.ClearTextOnFocus = false
    Input.Parent = FrameInput
    CriarArredondamento(Input, 8)
    
    local InputPadding = Instance.new("UIPadding")
    InputPadding.PaddingLeft = UDim.new(0, 10)
    InputPadding.PaddingRight = UDim.new(0, 10)
    InputPadding.Parent = Input
    
    Input.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            task.spawn(function()
                pcall(configuracao.Callback, Input.Text)
            end)
            if configuracao.LimparAoPerder then
                Input.Text = ""
            end
        end
    end)
    
    return {
        Definir = function(texto)
            Input.Text = texto
        end
    }
end

-- Criar Dropdown
function Library:Dropdown(aba, configuracao)
    configuracao = configuracao or {}
    configuracao.Nome = configuracao.Nome or "Dropdown"
    configuracao.Opcoes = configuracao.Opcoes or {"Opção 1", "Opção 2"}
    configuracao.Padrao = configuracao.Padrao or configuracao.Opcoes[1]
    configuracao.Callback = configuracao.Callback or function(v) end
    
    local FrameDropdown = Instance.new("Frame")
    FrameDropdown.BackgroundColor3 = Cores.Elemento
    FrameDropdown.Size = UDim2.new(1, -16, 0, 48)
    FrameDropdown.Parent = aba.Conteudo
    CriarArredondamento(FrameDropdown, 10)
    
    local Label = Instance.new("TextLabel")
    Label.Text = configuracao.Nome
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 14
    Label.TextColor3 = Cores.TextoBranco
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 0)
    Label.Size = UDim2.new(0.45, 0, 1, 0)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = FrameDropdown
    
    local BotaoDropdown = Instance.new("TextButton")
    BotaoDropdown.Text = configuracao.Padrao .. " ▼"
    BotaoDropdown.Font = Enum.Font.Gotham
    BotaoDropdown.TextSize = 13
    BotaoDropdown.TextColor3 = Cores.TextoBranco
    BotaoDropdown.BackgroundColor3 = Cores.FundoPrincipal
    BotaoDropdown.Position = UDim2.new(0.48, 0, 0.5, -16)
    BotaoDropdown.Size = UDim2.new(0.48, 0, 0, 32)
    BotaoDropdown.Parent = FrameDropdown
    CriarArredondamento(BotaoDropdown, 8)
    
    local ListaOpcoes = Instance.new("Frame")
    ListaOpcoes.BackgroundColor3 = Cores.FundoSecundario
    ListaOpcoes.Position = UDim2.new(0, 0, 1, 6)
    ListaOpcoes.Size = UDim2.new(1, 0, 0, 0)
    ListaOpcoes.Visible = false
    ListaOpcoes.ZIndex = 10
    ListaOpcoes.Parent = FrameDropdown
    CriarArredondamento(ListaOpcoes, 10)
    
    local BordaLista = Instance.new("UIStroke")
    BordaLista.Color = Cores.Destaque
    BordaLista.Thickness = 1
    BordaLista.Transparency = 0.7
    BordaLista.Parent = ListaOpcoes
    
    local LayoutOpcoes = Instance.new("UIListLayout")
    LayoutOpcoes.Padding = UDim.new(0, 3)
    LayoutOpcoes.Parent = ListaOpcoes
    
    local PaddingOpcoes = Instance.new("UIPadding")
    PaddingOpcoes.PaddingTop = UDim.new(0, 5)
    PaddingOpcoes.PaddingBottom = UDim.new(0, 5)
    PaddingOpcoes.PaddingLeft = UDim.new(0, 5)
    PaddingOpcoes.PaddingRight = UDim.new(0, 5)
    PaddingOpcoes.Parent = ListaOpcoes
    
    local opcaoAtual = configuracao.Padrao
    local expandido = false
    
    for _, opcao in ipairs(configuracao.Opcoes) do
        local BotaoOpcao = Instance.new("TextButton")
        BotaoOpcao.Text = opcao
        BotaoOpcao.Font = Enum.Font.Gotham
        BotaoOpcao.TextSize = 13
        BotaoOpcao.TextColor3 = Cores.TextoBranco
        BotaoOpcao.BackgroundColor3 = Cores.Elemento
        BotaoOpcao.Size = UDim2.new(1, 0, 0, 32)
        BotaoOpcao.ZIndex = 11
        BotaoOpcao.Parent = ListaOpcoes
        CriarArredondamento(BotaoOpcao, 8)
        
        BotaoOpcao.MouseButton1Click:Connect(function()
            opcaoAtual = opcao
            BotaoDropdown.Text = opcao .. " ▼"
            expandido = false
            ListaOpcoes.Visible = false
            FrameDropdown.Size = UDim2.new(1, -16, 0, 48)
            task.spawn(function()
                pcall(configuracao.Callback, opcao)
            end)
        end)
    end
    
    LayoutOpcoes:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        ListaOpcoes.Size = UDim2.new(1, 0, 0, LayoutOpcoes.AbsoluteContentSize.Y + 10)
    end)
    
    BotaoDropdown.MouseButton1Click:Connect(function()
        expandido = not expandido
        ListaOpcoes.Visible = expandido
        
        if expandido then
            FrameDropdown.Size = UDim2.new(1, -16, 0, 48 + ListaOpcoes.AbsoluteSize.Y + 6)
            BotaoDropdown.Text = opcaoAtual .. " ▲"
        else
            FrameDropdown.Size = UDim2.new(1, -16, 0, 48)
            BotaoDropdown.Text = opcaoAtual .. " ▼"
        end
    end)
    
    return {
        Definir = function(opcao)
            opcaoAtual = opcao
            BotaoDropdown.Text = opcao .. " ▼"
        end
    }
end

-- Criar Label
function Library:Label(aba, texto)
    local FrameLabel = Instance.new("Frame")
    FrameLabel.BackgroundColor3 = Cores.Elemento
    FrameLabel.Size = UDim2.new(1, -16, 0, 40)
    FrameLabel.Parent = aba.Conteudo
    CriarArredondamento(FrameLabel, 10)
    
    local Label = Instance.new("TextLabel")
    Label.Text = texto or "Label"
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 13
    Label.TextColor3 = Cores.TextoBranco
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 0)
    Label.Size = UDim2.new(1, -30, 1, 0)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextWrapped = true
    Label.Parent = FrameLabel
    
    return {
        Definir = function(novoTexto)
            Label.Text = novoTexto
        end
    }
end

-- Notificações
function Library:Notificar(configuracao)
    configuracao = configuracao or {}
    configuracao.Titulo = configuracao.Titulo or "Notificação"
    configuracao.Descricao = configuracao.Descricao or "Descrição"
    configuracao.Duracao = configuracao.Duracao or 3
    configuracao.Tipo = configuracao.Tipo or "padrao" -- padrao, sucesso, erro, aviso
    
    local cores = {
        padrao = Cores.Destaque,
        sucesso = Cores.Sucesso,
        erro = Cores.Erro,
        aviso = Cores.Aviso
    }
    
    local icones = {
        padrao = "ℹ️",
        sucesso = "✓",
        erro = "✕",
        aviso = "⚠️"
    }
    
    local NotifGui = Instance.new("ScreenGui")
    NotifGui.Name = "Notificacao"
    NotifGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    if syn and syn.protect_gui then
        syn.protect_gui(NotifGui)
        NotifGui.Parent = CoreGui
    elseif gethui then
        NotifGui.Parent = gethui()
    else
        NotifGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    end
    
    local FrameNotif = Instance.new("Frame")
    FrameNotif.BackgroundColor3 = Cores.FundoSecundario
    FrameNotif.Size = UDim2.new(0, 320, 0, 90)
    FrameNotif.Position = UDim2.new(1, 10, 1, -100)
    FrameNotif.Parent = NotifGui
    CriarArredondamento(FrameNotif, 12)
    CriarSombra(FrameNotif, Color3.fromRGB(0, 0, 0))
    
    local Borda = Instance.new("UIStroke")
    Borda.Color = cores[configuracao.Tipo]
    Borda.Thickness = 2
    Borda.Parent = FrameNotif
    
    local BarraLateral = Instance.new("Frame")
    BarraLateral.BackgroundColor3 = cores[configuracao.Tipo]
    BarraLateral.Size = UDim2.new(0, 5, 1, 0)
    BarraLateral.Parent = FrameNotif
    CriarArredondamento(BarraLateral, 12)
    
    local Icone = Instance.new("TextLabel")
    Icone.Text = icones[configuracao.Tipo]
    Icone.Font = Enum.Font.GothamBold
    Icone.TextSize = 20
    Icone.TextColor3 = cores[configuracao.Tipo]
    Icone.BackgroundTransparency = 1
    Icone.Position = UDim2.new(0, 20, 0, 10)
    Icone.Size = UDim2.new(0, 30, 0, 30)
    Icone.Parent = FrameNotif
    
    local Titulo = Instance.new("TextLabel")
    Titulo.Text = configuracao.Titulo
    Titulo.Font = Enum.Font.GothamBold
    Titulo.TextSize = 15
    Titulo.TextColor3 = Cores.TextoBranco
    Titulo.BackgroundTransparency = 1
    Titulo.Position = UDim2.new(0, 60, 0, 10)
    Titulo.Size = UDim2.new(1, -70, 0, 25)
    Titulo.TextXAlignment = Enum.TextXAlignment.Left
    Titulo.Parent = FrameNotif
    
    local Descricao = Instance.new("TextLabel")
    Descricao.Text = configuracao.Descricao
    Descricao.Font = Enum.Font.Gotham
    Descricao.TextSize = 13
    Descricao.TextColor3 = Cores.TextoCinza
    Descricao.BackgroundTransparency = 1
    Descricao.Position = UDim2.new(0, 60, 0, 38)
    Descricao.Size = UDim2.new(1, -70, 1, -45)
    Descricao.TextXAlignment = Enum.TextXAlignment.Left
    Descricao.TextYAlignment = Enum.TextYAlignment.Top
    Descricao.TextWrapped = true
    Descricao.Parent = FrameNotif
    
    TweenService:Create(FrameNotif, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -330, 1, -100)
    }):Play()
    
    task.wait(configuracao.Duracao)
    
    TweenService:Create(FrameNotif, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, 10, 1, -100)
    }):Play()
    
    task.wait(0.5)
    NotifGui:Destroy()
end

return Library
