--[[ 
    MM2 ULTIMATE HUB - AUTO DESTRUIÇÃO
    Criado por: light_use182
]]

local Player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

--// 1. INTERFACE DE SEGURANÇA INICIAL
local SafetyGui = Instance.new("ScreenGui", Player.PlayerGui)
SafetyGui.Name = "MM2_SafetyCheck_182"

local SafetyFrame = Instance.new("Frame", SafetyGui)
SafetyFrame.Size = UDim2.new(0, 320, 0, 180)
SafetyFrame.Position = UDim2.new(0.5, -160, 0.5, -90)
SafetyFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Instance.new("UICorner", SafetyFrame)

local Title = Instance.new("TextLabel", SafetyFrame)
Title.Size = UDim2.new(1, 0, 0, 40); Title.Text = "SISTEMA DE SEGURANÇA"; Title.TextColor3 = Color3.new(1, 1, 1); Title.Font = "GothamBold"; Title.BackgroundTransparency = 1

local Desc = Instance.new("TextLabel", SafetyFrame)
Desc.Size = UDim2.new(1, -20, 0, 60); Desc.Position = UDim2.new(0, 10, 0, 45); Desc.Text = "Executar script de light_use182?\n(Evite duplicatas)"; Desc.TextColor3 = Color3.fromRGB(200, 200, 200); Desc.Font = "Gotham"; Desc.BackgroundTransparency = 1

local SimBtn = Instance.new("TextButton", SafetyFrame)
SimBtn.Size = UDim2.new(0, 120, 0, 35); SimBtn.Position = UDim2.new(0, 25, 0, 120); SimBtn.Text = "SIM"; SimBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 0); SimBtn.TextColor3 = Color3.new(1, 1, 1); Instance.new("UICorner", SimBtn)

local NaoBtn = Instance.new("TextButton", SafetyFrame)
NaoBtn.Size = UDim2.new(0, 120, 0, 35); NaoBtn.Position = UDim2.new(0, 175, 0, 120); NaoBtn.Text = "NÃO"; NaoBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 0); NaoBtn.TextColor3 = Color3.new(1, 1, 1); Instance.new("UICorner", NaoBtn)

NaoBtn.MouseButton1Click:Connect(function() SafetyGui:Destroy() end)
SimBtn.MouseButton1Click:Connect(function() SafetyGui:Destroy(); ExecutarScriptPrincipal() end)

--// 2. SCRIPT PRINCIPAL
function ExecutarScriptPrincipal()
    local Active = true
    local ScreenGui = Instance.new("ScreenGui", Player.PlayerGui)
    ScreenGui.Name = "MM2_Final_UI"
    
    -- Elementos do Menu
    local MainFrame = Instance.new("Frame", ScreenGui)
    MainFrame.Size = UDim2.new(0, 180, 0, 360)
    MainFrame.Position = UDim2.new(0.5, -90, 0.4, 0)
    MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    MainFrame.Visible = false
    Instance.new("UICorner", MainFrame)

    local OpenBtn = Instance.new("TextButton", ScreenGui)
    OpenBtn.Size = UDim2.new(0, 50, 0, 50); OpenBtn.Position = UDim2.new(0, 20, 0.5, -25); OpenBtn.Text = "UI"; OpenBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45); OpenBtn.TextColor3 = Color3.new(1, 1, 1); Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0)

    -- Função de Arrasto
    local function Drag(gui)
        local d, s, p
        gui.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then d = true; s = i.Position; p = gui.Position end end)
        UIS.InputChanged:Connect(function(i) if d and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then local del = i.Position - s; gui.Position = UDim2.new(p.X.Scale, p.X.Offset + del.X, p.Y.Scale, p.Y.Offset + del.Y) end end)
        UIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then d = false end end)
    end
    Drag(MainFrame); Drag(OpenBtn)
    OpenBtn.MouseButton1Click:Connect(function() MainFrame.Visible = not MainFrame.Visible end)

    -- Botões
    local function CreateBtn(txt, pos, color)
        local b = Instance.new("TextButton", MainFrame)
        b.Size = UDim2.new(0, 160, 0, 35); b.Position = UDim2.new(0, 10, 0, pos); b.Text = txt; b.BackgroundColor3 = color; b.TextColor3 = Color3.new(1, 1, 1); b.Font = "SourceSansBold"; Instance.new("UICorner", b); return b
    end

    local bM = CreateBtn("Murder ESP", 15, Color3.fromRGB(150, 0, 0))
    local bX = CreateBtn("Xerife ESP", 55, Color3.fromRGB(0, 0, 150))
    local bI = CreateBtn("Inocente ESP", 95, Color3.fromRGB(0, 120, 0))
    local bG = CreateBtn("Arma Dropada", 135, Color3.fromRGB(180, 150, 0))
    local bLook = CreateBtn("IA: Foco Mira", 185, Color3.fromRGB(60, 60, 60))
    local bDist = CreateBtn("IA: Mais Perto", 230, Color3.fromRGB(60, 60, 60))
    local bDestruct = CreateBtn("AUTO DESTRUIÇÃO", 290, Color3.fromRGB(100, 0, 0))

    local esp = {M = false, X = false, I = false, G = false}
    local activeIA = "None"; local armaCache = nil

    bM.MouseButton1Click:Connect(function() esp.M = not esp.M end)
    bX.MouseButton1Click:Connect(function() esp.X = not esp.X end)
    bI.MouseButton1Click:Connect(function() esp.I = not esp.I end)
    bG.MouseButton1Click:Connect(function() esp.G = not esp.G end)
    
    --// LÓGICA DE AUTO DESTRUIÇÃO
    bDestruct.MouseButton1Click:Connect(function()
        local Conf = Instance.new("Frame", ScreenGui)
        Conf.Size = UDim2.new(0, 200, 0, 100); Conf.Position = UDim2.new(0.5, -100, 0.5, -50); Conf.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1); Instance.new("UICorner", Conf)
        local t = Instance.new("TextLabel", Conf); t.Size = UDim2.new(1, 0, 0, 50); t.Text = "DELETAR TUDO?"; t.TextColor3 = Color3.new(1, 1, 1); t.BackgroundTransparency = 1
        local s = Instance.new("TextButton", Conf); s.Size = UDim2.new(0, 80, 0, 30); s.Position = UDim2.new(0, 15, 0, 55); s.Text = "SIM"; s.BackgroundColor3 = Color3.new(0, 0.6, 0); Instance.new("UICorner", s)
        local n = Instance.new("TextButton", Conf); n.Size = UDim2.new(0, 80, 0, 30); n.Position = UDim2.new(0, 105, 0, 55); n.Text = "NÃO"; n.BackgroundColor3 = Color3.new(0.6, 0, 0); Instance.new("UICorner", n)

        n.MouseButton1Click:Connect(function() Conf:Destroy() end)
        s.MouseButton1Click:Connect(function()
            Active = false
            -- Limpeza de Highlights
            for _, p in pairs(game.Players:GetPlayers()) do
                if p.Character and p.Character:FindFirstChild("ESPAura") then p.Character.ESPAura:Destroy() end
            end
            -- Limpeza de Armas
            for _, o in pairs(workspace:GetDescendants()) do
                if o.Name == "GunLabel" then o:Destroy() end
            end
            ScreenGui:Destroy()
        end)
    end)

    -- Loop de Cache da Arma
    task.spawn(function()
        while Active do
            task.wait(1)
            if esp.G then
                local f = nil
                for _, o in pairs(workspace:GetDescendants()) do
                    if (o.Name == "GunDrop" or (o:IsA("Part") and o:FindFirstChild("GunMesh"))) and not o:FindFirstAncestorOfClass("Model") then f = o; break end
                end
                armaCache = f
            end
        end
    end)

    -- Loop Principal
    RunService.RenderStepped:Connect(function()
        if not Active then return end
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= Player and p.Character then
                local isM = p.Backpack:FindFirstChild("Knife") or p.Character:FindFirstChild("Knife")
                local isX = p.Backpack:FindFirstChild("Gun") or p.Character:FindFirstChild("Gun")
                local cor = (isM and esp.M and Color3.new(1,0,0)) or (isX and esp.X and Color3.new(0,0,1)) or (not isM and not isX and esp.I and Color3.new(0,1,0))
                local h = p.Character:FindFirstChild("ESPAura")
                if cor then
                    if not h then h = Instance.new("Highlight", p.Character); h.Name = "ESPAura"; h.DepthMode = "AlwaysOnTop" end
                    h.FillColor = cor; h.Enabled = true
                elseif h then h.Enabled = false end
            end
        end
        if esp.G and armaCache and armaCache.Parent then
            if not armaCache:FindFirstChild("GunLabel") then
                local bg = Instance.new("BillboardGui", armaCache); bg.Name = "GunLabel"; bg.AlwaysOnTop = true; bg.Size = UDim2.new(0, 100, 0, 40)
                local txt = Instance.new("TextLabel", bg); txt.Size = UDim2.new(1, 0, 1, 0); txt.Text = "GUN!"; txt.TextColor3 = Color3.new(1, 1, 0); txt.TextSize = 25; txt.BackgroundTransparency = 1; txt.Font = "GothamBlack"
            end
        end
    end)
end
