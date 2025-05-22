getgenv().WalkSpeedValue = 300
getgenv().WalkSpeedEnabled = false

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local humanoid
local walkSpeedConnection

-- Function to apply the walkspeed logic
local function enableWalkSpeed()
    if humanoid then
        humanoid.WalkSpeed = getgenv().WalkSpeedValue
        walkSpeedConnection = humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
            humanoid.WalkSpeed = getgenv().WalkSpeedValue
        end)
    end
end

-- Function to stop the walkspeed override
local function disableWalkSpeed()
    if walkSpeedConnection then
        walkSpeedConnection:Disconnect()
        walkSpeedConnection = nil
    end
    if humanoid then
        humanoid.WalkSpeed = 16 -- default Roblox speed
    end
end

-- Toggle logic
local function toggleWalkSpeed()
    getgenv().WalkSpeedEnabled = not getgenv().WalkSpeedEnabled
    if getgenv().WalkSpeedEnabled then
        enableWalkSpeed()
    else
        disableWalkSpeed()
    end
end

-- Handle character added (respawn)
local function onCharacterAdded(char)
    humanoid = char:WaitForChild("Humanoid")
    if getgenv().WalkSpeedEnabled then
        enableWalkSpeed()
    end
end

-- Bind character and key events
if Player.Character then
    onCharacterAdded(Player.Character)
end
Player.CharacterAdded:Connect(onCharacterAdded)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.Z then
        toggleWalkSpeed()
    end
end)
