local Players = game:GetService("Players")
local GuiService = game:GetService("GuiService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local Char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HR = Char:WaitForChild("HumanoidRootPart")
local StandHR = HR.CFrame

-- Initialize global variables
_G.AutoCast = false
_G.AutoShake = false
_G.AutoReel = false

-- Equip item function
equipitem = function(v)
    if LocalPlayer.Backpack:FindFirstChild(v) then
        local Eq = LocalPlayer.Backpack:FindFirstChild(v)
        LocalPlayer.Character.Humanoid:EquipTool(Eq)
    end
end

local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
if not Fluent then
    warn("Unable to load Fluent library")
    return
end

local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
local Window = Fluent:CreateWindow({
    Title = "รักตุ๊กตาจ้า" .. Fluent.Version,
    SubTitle = "bytitan 1.0",
    TabWidth = 100,
    Size = UDim2.fromOffset(555, 355),
    Acrylic = true,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "Main" }),
    Teleport = Window:AddTab({ Title = "Teleport", Icon = "Teleport" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

Fluent:Notify({
    Title = "Tastjaja",
    Content = "ยินดีต้อนรับ",
    SubContent = "สร้างโดย Titan",
    Duration = 100
})

Fluent:Notify({
    Title = "อ่านทำขวยไร",
    Content = "หยอก",
    Duration = 50
})

Tabs.Main:AddParagraph({
    Title = "ถ้าจะAFKแนะนำเปิด{เเเล้วเเต่มึง}",
    Content = " 1.Auto hold rod 2.Auto Cast 3.Auto Shake 4.Auto Reel"
})

local FreezeToggle = Tabs.Main:AddToggle("Freeze", { Title = "Freeze(แช่)", Default = false })
FreezeToggle:OnChanged(function(state)
    Char.HumanoidRootPart.Anchored = state
end)

spawn(function()
    while wait() do
        if _G.AutoCast then
            pcall(function()
                for _, v in pairs(LocalPlayer.Backpack:GetChildren()) do
                    if v:IsA("Tool") and v.Name:lower():find("rod") then
                        equipitem(v.Name)
                    end
                end
            end)
        end
    end
end)

local AutoHoldRodToggle = Tabs.Main:AddToggle("Autoholdrod", { Title = "Autoholdrod(ถือเบ็ด)", Default = false })
AutoHoldRodToggle:OnChanged(function(state)
    _G.AutoCast = state
    spawn(function()
        while _G.AutoCast do
            wait(0.1)
            for _, v in pairs(LocalPlayer.Backpack:GetChildren()) do
                if v:IsA("Tool") and v.Name:lower():find("rod") then
                    equipitem(v.Name)
                end
            end
        end
    end)
end)

local AutoCastToggle = Tabs.Main:AddToggle("AutoCast", { Title = "AutoCast(โยก)", Default = false })
AutoCastToggle:OnChanged(function(state)
    _G.AutoCast = state
    spawn(function()
        while _G.AutoCast do
            wait(0.1)
            pcall(function()
                local Rod = LocalPlayer.Character:FindFirstChildOfClass("Tool")
                if Rod and Rod:FindFirstChild("events") then
                    Rod.events.cast:FireServer(100, 1)
                end
            end)
        end
    end)
end)

local AutoShakeToggle = Tabs.Main:AddToggle("AutoShake", { Title = "AutoShake(เขย่า)", Default = false })
AutoShakeToggle:OnChanged(function(state)
    _G.AutoShake = state
    spawn(function()
        while _G.AutoShake do
            wait(0.01)
            pcall(function()
                local GUI = LocalPlayer:WaitForChild("PlayerGui")
                local shakeui = GUI:FindFirstChild("shakeui")
                if shakeui and shakeui.Enabled then
                    local safezone = shakeui:FindFirstChild("safezone")
                    if safezone then
                        local button = safezone:FindFirstChild("button")
                        if button and button:IsA("ImageButton") and button.Visible then
                            GuiService.SelectedCoreObject = button
                            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Return, false, game)
                            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Return, false, game)
                        end
                    end
                end
            end)
        end
    end)
end)

local AutoReelToggle = Tabs.Main:AddToggle("AutoReel", { Title = "AutoReel(ดึง)", Default = false })
AutoReelToggle:OnChanged(function(state)
    _G.AutoReel = state
    spawn(function()
        while _G.AutoReel do
            wait(0.1)
            pcall(function()
                local eventsFolder = ReplicatedStorage:FindFirstChild("events")
                if eventsFolder then
                    local reelfinished = eventsFolder:FindFirstChild("reelfinished")
                    if reelfinished then
                        for _, v in pairs(LocalPlayer.PlayerGui:GetChildren()) do
                            if v:IsA("ScreenGui") and v.Name == "reel" and v:FindFirstChild("bar") then
                                wait(1)
                                reelfinished:FireServer(100, true)
                            end
                        end
                    end
                end
            end)
        end
    end)
end)
