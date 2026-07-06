# Snippets: Lua & Roblox DataStore Pro 🎮

Template robusto para persistência de dados de jogadores no Roblox utilizando o serviço **DataStoreService** com tratamento de retentativas (`pcall`) e auto-save.

## 1. DataStore Manager (Server Script)

```lua
local DataStoreService = game:GetService("DataStoreService")
local Players = game:GetService("Players")

-- Referência ao banco de dados do jogo
local PlayerDataStore = DataStoreService:GetDataStore("GameData_v1")

local function setupPlayerData(player)
	local leaderstats = Instance.new("Folder")
	leaderstats.Name = "leaderstats"
	leaderstats.Parent = player

	local coins = Instance.new("IntValue")
	coins.Name = "Coins"
	coins.Value = 0
	coins.Parent = leaderstats

	-- Carregamento de dados com pcall (tratamento de falha de conexão)
	local userId = player.UserId
	local dataKey = "Player_" .. userId
	
	local success, savedData = pcall(function()
		return PlayerDataStore:GetAsync(dataKey)
	end)

	if success and savedData then
		coins.Value = savedData.Coins or 0
		print("Dados carregados com sucesso para " .. player.Name)
	else
		warn("Falha ao carregar dados do jogador: " .. tostring(savedData))
	end
end

local function savePlayerData(player)
	local userId = player.UserId
	local dataKey = "Player_" .. userId
	
	local coinsValue = player.leaderstats.Coins.Value
	local dataToSave = {
		Coins = coinsValue
	}

	-- Tentativa de gravação segura
	local success, err = pcall(function()
		PlayerDataStore:SetAsync(dataKey, dataToSave)
	end)

	if success then
		print("Dados salvos com sucesso para " .. player.Name)
	else
		warn("Erro ao salvar dados de " .. player.Name .. ": " .. tostring(err))
	end
end

-- Eventos de conexão e desconexão
Players.PlayerAdded:Connect(setupPlayerData)
Players.PlayerRemoving:Connect(savePlayerData)

-- Salvar dados caso o servidor feche de repente (BindToClose)
game:BindToClose(function()
	for _, player in ipairs(Players:GetPlayers()) do
		savePlayerData(player)
	end
end)
```
