-- Aimbot com Auto Shoot e FOV Visual (Testes Pessoais)
-- Feito para Arceus X / Hydrogen

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local VirtualInput = game:GetService("VirtualInputManager")

-- Configurações
local aimbotAtivo = false
local raio = 100 -- raio de FOV em pixels
local parteAlvo = "Head"
local autoShoot = true

-- Criar FOV Visual
local fovCircle = Drawing.new("Circle")
fovCircle.Radius = raio
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Thickness = 2
fovCircle.Filled = false
fovCircle.Visible = true
fovCircle.Transparency = 1

-- Função para encontrar o inimigo mais próximo dentro do FOV
local function encontrarAlvo()
    local alvoMaisProximo = nil
    local menorDistancia = math.huge

    for _, jogador in pairs(Players:GetPlayers()) do
        if jogador ~= LocalPlayer and jogador.Character and jogador.Character:FindFirstChild(parteAlvo) then
            local parte = jogador.Character[parteAlvo]
            local posTela, visivel = Camera:WorldToViewportPoint(parte.Position)
            if visivel then
                local distancia = (Vector2.new(posTela.X, posTela.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).magnitude
                if distancia < menorDistancia and distancia < raio then
                    menorDistancia = distancia
                    alvoMaisProximo = parte
                end
            end
        end
    end

    return alvoMaisProximo
end

-- Loop principal
RunService.RenderStepped:Connect(function()
    fovCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)

    if aimbotAtivo then
        local alvo = encontrarAlvo()
        if alvo then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, alvo.Position)

            if autoShoot then
                -- Simula clique do mouse (funciona no mobile com Arceus X/Hydrogen)
                VirtualInput:SendMouseButtonEvent(
                    0, 0, -- posição (0,0): não importa nesse caso
                    0, -- botão esquerdo
                    true, -- pressionar
                    game,
                    0
                )
                task.wait(0.05)
                VirtualInput:SendMouseButtonEvent(
                    0, 0,
                    0,
                    false, -- soltar
                    game,
                    0
                )
            end
        end
    end
end)

-- Tecla para ativar/desativar o aimbot (E)
UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.E then
        aimbotAtivo = not aimbotAtivo
        print("Aimbot:", aimbotAtivo and "Ativado" or "Desativado")
    end
end)
