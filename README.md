-- ============================================
-- ANTI-KICK v5.2 - DELTA EDITION (SILENCIO TOTAL)
-- ============================================
-- Sin pruebas, sin mensajes sospechosos, solo protección pasiva

local player = game:GetService("Players").LocalPlayer
local runService = game:GetService("RunService")
local coreGui = (syn and syn.protect_gui) and game:GetService("CoreGui") or game:GetService("Players").LocalPlayer.PlayerGui

-- ============================================
-- CREAR GUI
-- ============================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AntiKickMonitor"
screenGui.ResetOnSpawn = false

if syn and syn.protect_gui then
    syn.protect_gui(screenGui)
end

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 400, 0, 450)
mainFrame.Position = UDim2.new(0, 10, 0, 10)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
mainFrame.BackgroundTransparency = 0.05
mainFrame.BorderSizePixel = 0

-- Barra superior
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 35)
topBar.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
topBar.BorderSizePixel = 0

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 1, 0)
title.Position = UDim2.new(0, 10, 0, 0)
title.Text = "🛡️ ANTI-KICK v5.2 | SILENCIO TOTAL"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 13
title.TextXAlignment = Enum.TextXAlignment.Left
title.Font = Enum.Font.GothamBold
title.BackgroundTransparency = 1

local statusDot = Instance.new("Frame")
statusDot.Size = UDim2.new(0, 10, 0, 10)
statusDot.Position = UDim2.new(1, -25, 0, 12)
statusDot.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
statusDot.BorderSizePixel = 0

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 25, 0, 25)
closeBtn.Position = UDim2.new(1, -30, 0, 5)
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.fromRGB(255, 100, 100)
closeBtn.TextSize = 16
closeBtn.Font = Enum.Font.GothamBold
closeBtn.BackgroundTransparency = 1

-- Área de logs
local logsFrame = Instance.new("ScrollingFrame")
logsFrame.Size = UDim2.new(1, -20, 1, -80)
logsFrame.Position = UDim2.new(0, 10, 0, 45)
logsFrame.BackgroundTransparency = 1
logsFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
logsFrame.ScrollBarThickness = 6
logsFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

local logsLayout = Instance.new("UIListLayout")
logsLayout.Padding = UDim.new(0, 4)
logsLayout.SortOrder = Enum.SortOrder.LayoutOrder
logsLayout.Parent = logsFrame

-- Barra inferior
local bottomBar = Instance.new("Frame")
bottomBar.Size = UDim2.new(1, 0, 0, 35)
bottomBar.Position = UDim2.new(0, 0, 1, -35)
bottomBar.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
bottomBar.BorderSizePixel = 0

local statusText = Instance.new("TextLabel")
statusText.Size = UDim2.new(1, -10, 1, 0)
statusText.Position = UDim2.new(0, 5, 0, 0)
statusText.Text = "🟢 ACTIVO | Modo silencio total"
statusText.TextColor3 = Color3.fromRGB(150, 255, 150)
statusText.TextSize = 11
statusText.TextXAlignment = Enum.TextXAlignment.Left
statusText.Font = Enum.Font.Gotham
statusText.BackgroundTransparency = 1

-- Esquinas
local uiCornerMain = Instance.new("UICorner")
uiCornerMain.CornerRadius = UDim.new(0, 8)
local uiCornerTop = Instance.new("UICorner")
uiCornerTop.CornerRadius = UDim.new(0, 8)
local uiCornerBottom = Instance.new("UICorner")
uiCornerBottom.CornerRadius = UDim.new(0, 8)

-- ============================================
-- FUNCIONES DE LOG (SILENCIOSAS)
-- ============================================
local function addLog(message, logType)
    local logFrame = Instance.new("Frame")
    logFrame.Size = UDim2.new(1, 0, 0, 28)
    logFrame.BackgroundTransparency = 1
    
    local logIcon = Instance.new("TextLabel")
    logIcon.Size = UDim2.new(0, 25, 1, 0)
    logIcon.BackgroundTransparency = 1
    logIcon.TextSize = 14
    
    local logText = Instance.new("TextLabel")
    logText.Size = UDim2.new(1, -30, 1, 0)
    logText.Position = UDim2.new(0, 25, 0, 0)
    logText.BackgroundTransparency = 1
    logText.TextSize = 11
    logText.Font = Enum.Font.Gotham
    logText.TextXAlignment = Enum.TextXAlignment.Left
    logText.Text = message
    
    local icons = {
        info = "ℹ️", success = "✅", warning = "⚠️", error = "❌"
    }
    local colors = {
        info = Color3.fromRGB(100, 180, 255),
        success = Color3.fromRGB(100, 255, 100),
        warning = Color3.fromRGB(255, 200, 100),
        error = Color3.fromRGB(255, 100, 100)
    }
    
    logIcon.Text = icons[logType] or "ℹ️"
    logText.TextColor3 = colors[logType] or colors.info
    
    logIcon.Parent = logFrame
    logText.Parent = logFrame
    logFrame.Parent = logsFrame
    
    task.wait(0.05)
    logsFrame.CanvasPosition = Vector2.new(0, logsFrame.CanvasSize.Y.Offset)
end

-- ============================================
-- PROTECCIÓN (COMPLETAMENTE PASIVA)
-- ============================================
local originalKick = nil
local heartbeatConnection = nil
local hookActive = true

-- Capturar método original (sin mostrar errores)
local success, capturedKick = pcall(function()
    return player.Kick
end)

if success and capturedKick then
    originalKick = capturedKick
    addLog("📦 Sistema listo", "success")
end

-- Hook de protección (sin mensajes llamativos)
local ourKick = function(self, message)
    -- Solo log interno, nada que enviar al servidor
    addLog("🛡️ Intento bloqueado", "warning")
    statusText.Text = "⚠️ Intento bloqueado"
    statusDot.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
    task.wait(0.3)
    statusDot.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    statusText.Text = "🟢 ACTIVO | Modo silencio total"
    return nil
end

-- Aplicar hook silenciosamente
pcall(function()
    rawset(player, "Kick", ourKick)
    addLog("🔗 Protección activa", "success")
end)

-- Heartbeat para mantener hook
heartbeatConnection = runService.Heartbeat:Connect(function()
    if hookActive and player.Kick ~= ourKick then
        pcall(function()
            rawset(player, "Kick", ourKick)
        end)
    end
end)

addLog("💓 Corazón latiendo", "success")

-- Interceptar RemoteEvents (silenciosamente)
pcall(function()
    local rs = game:GetService("ReplicatedStorage")
    local kickRemotes = {}
    
    for _, v in pairs(rs:GetDescendants()) do
        if v:IsA("RemoteEvent") and (v.Name:lower():find("kick") or v.Name:lower():find("ban")) then
            table.insert(kickRemotes, v)
        end
    end
    
    for _, remote in pairs(kickRemotes) do
        local oldEvent = remote.OnClientEvent
        remote.OnClientEvent = function(...)
            addLog("🚫 Remote bloqueado: " .. remote.Name, "warning")
            return nil
        end
    end
    
    if #kickRemotes > 0 then
        addLog("📡 " .. #kickRemotes .. " remotes monitoreados", "success")
    end
end)

-- Autodestrucción silenciosa (sin dejar rastro)
spawn(function()
    task.wait(8)
    if hookActive then
        hookActive = false
        if heartbeatConnection then
            heartbeatConnection:Disconnect()
        end
        addLog("💀 Modo fantasma activado", "success")
        statusText.Text = "👻 Modo fantasma"
    end
end)

-- ============================================
-- CONFIGURAR GUI
-- ============================================
uiCornerMain.Parent = mainFrame
topBar.Parent = mainFrame
uiCornerTop.Parent = topBar
title.Parent = topBar
statusDot.Parent = topBar
closeBtn.Parent = topBar
logsFrame.Parent = mainFrame
logsLayout.Parent = logsFrame
bottomBar.Parent = mainFrame
uiCornerBottom.Parent = bottomBar
statusText.Parent = bottomBar
mainFrame.Parent = screenGui

pcall(function()
    screenGui.Parent = coreGui
end)

-- ============================================
-- MENSAJE FINAL (SOLO EN GUI, NO EN EJECUCIÓN)
-- ============================================
addLog("━━━━━━━━━━━━━━━━━━━━━━━━━━", "info")
addLog("✨ ANTI-KICK v5.2 ACTIVO", "success")
addLog("━━━━━━━━━━━━━━━━━━━━━━━━━━", "info")
addLog("ℹ️ Sin pruebas automáticas", "info")
addLog("⚠️ Solo bloquea kicks del cliente", "warning")

print("✅ Anti-Kick v5.2 cargado - Sin pruebas, solo protección")
