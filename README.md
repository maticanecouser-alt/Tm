-- Skill Aim System — Roblox Studio
-- Uso: experiência própria, com alvos de teste.
-- Configurações
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

local MAX_DISTANCE = 150
local IGNORE_TEAMMATES = true

-- Obtém o personagem e sua raiz
local function getRoot(player)
    local character = player.Character
    if not character then
        return nil
    end

    local humanoid = character:FindFirstChildOfClass("Humanoid")
    local root = character:FindFirstChild("HumanoidRootPart")

    if not humanoid or humanoid.Health <= 0 or not root then
        return nil
    end

    return root
end

-- Seleciona o jogador válido mais próximo
local function getNearestTarget()
    local myRoot = getRoot(LocalPlayer)
    if not myRoot then
        return nil
    end

    local nearestPlayer = nil
    local nearestDistance = MAX_DISTANCE

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local sameTeam =
                IGNORE_TEAMMATES
                and LocalPlayer.Team ~= nil
                and player.Team == LocalPlayer.Team

            if not sameTeam then
                local targetRoot = getRoot(player)

                if targetRoot then
                    local distance =
                        (targetRoot.Position - myRoot.Position).Magnitude

                    if distance < nearestDistance then
                        nearestDistance = distance
                        nearestPlayer = player
                    end
                end
            end
        end
    end

    return nearestPlayer, nearestDistance
end

-- Retorna a posição atual do alvo para uma habilidade
local function getSkillAimPosition()
    local target = getNearestTarget()
    if not target then
        return nil
    end

    local root = getRoot(target)
    return root and root.Position or nil
end

-- Exemplo: consulta a mira somente quando necessário.
-- Chame esta função no sistema de habilidades do seu próprio jogo.
local function activateTestSkill()
    local aimPosition = getSkillAimPosition()

    if not aimPosition then
        warn("Nenhum alvo válido no alcance.")
        return
    end

    print("Posição da habilidade:", aimPosition)

    -- Integre aqui a habilidade da sua experiência.
    -- O servidor deve validar alvo, alcance e linha de visão.
end

-- Exemplo de atualização leve, sem varredura contínua:
-- activateTestSkill() quando o jogador ativar a habilidade.
