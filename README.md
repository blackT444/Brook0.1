local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Criar a RemoteEvent se não existir
local KickEvent = Instance.new("RemoteEvent")
KickEvent.Name = "KickPlayer"
KickEvent.Parent = ReplicatedStorage

-- Criar a GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Criar botão Play
local PlayButton = Instance.new("TextButton")
PlayButton.Size = UDim2.new(0, 200, 0, 50)
PlayButton.Position = UDim2.new(0.5, -100, 0.4, 0)
PlayButton.Text = "Play"
PlayButton.Parent = ScreenGui

-- Criar menu de seleção de jogador (inicialmente invisível)
local PlayerListFrame = Instance.new("Frame")
PlayerListFrame.Size = UDim2.new(0, 200, 0, 150)
PlayerListFrame.Position = UDim2.new(0.5, -100, 0.5, 0)
PlayerListFrame.Visible = false
PlayerListFrame.Parent = ScreenGui

-- Criar botão Ban (inicialmente invisível)
local BanButton = Instance.new("TextButton")
BanButton.Size = UDim2.new(0, 200, 0, 50)
BanButton.Position = UDim2.new(0.5, -100, 0.65, 0)
BanButton.Text = "Ban"
BanButton.Visible = false
BanButton.Parent = ScreenGui

local SelectedPlayer = nil -- Armazena o jogador escolhido

-- Quando clicar no Play, mostrar lista de jogadores
PlayButton.MouseButton1Click:Connect(function()
    PlayerListFrame.Visible = true
    PlayButton.Visible = false

    -- Limpar lista de jogadores anterior
    for _, child in pairs(PlayerListFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    -- Criar botões para cada jogador
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local PlayerButton = Instance.new("TextButton")
            PlayerButton.Size = UDim2.new(1, 0, 0, 30)
            PlayerButton.Text = player.Name
            PlayerButton.Parent = PlayerListFrame

            -- Selecionar jogador e mostrar botão Ban
            PlayerButton.MouseButton1Click:Connect(function()
                SelectedPlayer = player
                BanButton.Visible = true
            end)
        end
    end
end)

-- Quando clicar no botão Ban, remover jogador
BanButton.MouseButton1Click:Connect(function()
    if SelectedPlayer then
        KickEvent:FireServer(SelectedPlayer) -- Chamar o evento para remover o jogador
        BanButton.Visible = false
        PlayerListFrame.Visible = false
        PlayButton.Visible = true
    end
end)

-- Código do servidor para remover jogadores
KickEvent.OnServerEvent:Connect(function(player, targetPlayer)
    if targetPlayer and targetPlayer:IsA("Player") then
        targetPlayer:Kick("Você foi removido da experiência.")
    end
end)
