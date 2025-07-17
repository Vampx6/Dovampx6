local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Camera = workspace.CurrentCamera

local function addESP(character)
    if character:FindFirstChild("HumanoidRootPart") and not character:FindFirstChild("RedESP") then
        local espBox = Instance.new("BoxHandleAdornment")
        espBox.Name = "RedESP"
        espBox.Adornee = character.HumanoidRootPart
        espBox.AlwaysOnTop = true
        espBox.ZIndex = 10
        espBox.Size = Vector3.new(4, 6, 2)
        espBox.Color3 = Color3.new(1, 0, 0)
        espBox.Transparency = 0.5
        espBox.Parent = character
    end
end

local function setupESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            addESP(player.Character)
        end
    end
    Players.PlayerAdded:Connect(function(player)
        if player ~= LocalPlayer then
            player.CharacterAdded:Connect(function(character)
                wait(1)
                addESP(character)
            end)
        end
    end)
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            player.CharacterAdded:Connect(function(character)
                wait(1)
                addESP(character)
            end)
        end
    end
end

local function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local head = player.Character.Head
            local headPos, onScreen = Camera:WorldToViewportPoint(head.Position)
            if onScreen then
                local distance = (Vector2.new(headPos.X, headPos.Y) - Vector2.new(Mouse.X, Mouse.Y)).magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    closestPlayer = player
                end
            end
        end
    end
    return closestPlayer
end

local function aimAt(target)
    if target and target.Character and target.Character:FindFirstChild("Head") then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
    end
end

Mouse.Button2Down:Connect(function()
    local target = getClosestPlayer()
    if target then
        aimAt(target)
    end
end)

setupESP()