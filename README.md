--[[
	Ciallo Hub UI Library v1.3.5 (Full Chinese Edition)
	Ciallo～(∠・ω< )⌒★

	Repository / Raw:
	https://raw.githubusercontent.com/cocqwertyuiop/AOauto/refs/heads/main/README.md

	Features:
	- Built-in 820+ Lucide Vector Icons (Zero external HTTP dependency, instant load)
	- Enhanced Roblox Decal ID & Image ID Loader (Auto-resolves Decal IDs via GetObjects & rbxthumb)
	- Default Crisp White Theme ("Light") + 11 Built-in Themes & Up to 7 Custom Themes
	- Native Toast Notification System, Floating Windows, Watermark & Keybind List
	- Full JSON Configuration & Favorites Management
]]

local UIS = game:GetService("UserInputService")
local TS = game:GetService("TweenService")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local TextService = game:GetService("TextService")
local ContentProvider = game:GetService("ContentProvider")

local genv = (getgenv and getgenv()) or _G
if genv.CialloHub and genv.CialloHub.Destroy then
	pcall(genv.CialloHub.Destroy, genv.CialloHub)
end

local function C(r, g, b) return Color3.fromRGB(r, g, b) end

local INTRO_ICON = "rbxassetid://10110319522"
local INTRO_TITLE = "Ciallo Hub"
local INTRO_SUBTITLE = "Ciallo～(∠・ω< )⌒★"

local Themes = {
	Crimson = { Window = C(19, 19, 19), Side = C(14, 14, 14), Panel = C(17, 17, 17), Card = C(13, 13, 13), Element = C(27, 27, 27), Hover = C(38, 38, 38), Border = C(7, 7, 7), Accent = C(255, 24, 24), Text = C(240, 240, 240), Sub = C(112, 112, 112), Off = C(34, 34, 34) },
	Dark = { Window = C(24, 24, 26), Side = C(19, 19, 21), Panel = C(28, 28, 31), Card = C(33, 33, 37), Element = C(42, 42, 47), Hover = C(54, 54, 60), Border = C(10, 10, 12), Accent = C(96, 205, 255), Text = C(240, 240, 244), Sub = C(140, 142, 152), Off = C(70, 70, 80) },
	Darker = { Window = C(12, 12, 14), Side = C(8, 8, 10), Panel = C(15, 15, 18), Card = C(20, 20, 24), Element = C(28, 28, 33), Hover = C(38, 38, 44), Border = C(3, 3, 5), Accent = C(96, 205, 255), Text = C(240, 240, 244), Sub = C(130, 132, 144), Off = C(48, 48, 58) },
	Light = { Window = C(243, 243, 246), Side = C(234, 234, 239), Panel = C(247, 247, 250), Card = C(255, 255, 255), Element = C(240, 240, 244), Hover = C(228, 228, 234), Border = C(214, 214, 222), Accent = C(0, 120, 212), Text = C(24, 24, 28), Sub = C(100, 102, 112), Off = C(205, 205, 214) },
	Aqua = { Window = C(14, 24, 26), Side = C(11, 19, 21), Panel = C(16, 28, 31), Card = C(20, 35, 39), Element = C(26, 46, 50), Hover = C(34, 60, 65), Border = C(5, 11, 13), Accent = C(60, 220, 200), Text = C(232, 250, 248), Sub = C(120, 160, 158), Off = C(44, 74, 78) },
	Amethyst = { Window = C(22, 18, 30), Side = C(17, 14, 24), Panel = C(25, 20, 36), Card = C(30, 25, 43), Element = C(40, 33, 57), Hover = C(52, 44, 73), Border = C(8, 6, 14), Accent = C(176, 120, 255), Text = C(242, 238, 252), Sub = C(150, 140, 176), Off = C(62, 52, 88) },
	Rose = { Window = C(28, 18, 22), Side = C(22, 14, 18), Panel = C(32, 21, 26), Card = C(38, 25, 31), Element = C(50, 33, 41), Hover = C(64, 44, 54), Border = C(11, 6, 9), Accent = C(255, 120, 170), Text = C(252, 238, 244), Sub = C(176, 140, 154), Off = C(82, 58, 70) },
	Onyx = { Window = C(10, 10, 10), Side = C(5, 5, 5), Panel = C(8, 8, 8), Card = C(14, 14, 14), Element = C(22, 22, 22), Hover = C(34, 34, 34), Border = C(0, 0, 0), Accent = C(255, 255, 255), Text = C(245, 245, 245), Sub = C(140, 140, 140), Off = C(42, 42, 42), OnAccent = C(10, 10, 10) },
	Emerald = { Window = C(14, 24, 19), Side = C(10, 18, 14), Panel = C(17, 29, 23), Card = C(21, 36, 28), Element = C(28, 47, 37), Hover = C(37, 62, 49), Border = C(5, 11, 8), Accent = C(52, 211, 153), Text = C(232, 250, 242), Sub = C(122, 163, 145), Off = C(40, 66, 54) },
	Toxic = { Window = C(12, 18, 10), Side = C(8, 13, 7), Panel = C(14, 21, 12), Card = C(18, 27, 15), Element = C(25, 38, 21), Hover = C(34, 52, 29), Border = C(3, 6, 2), Accent = C(166, 255, 0), Text = C(236, 250, 225), Sub = C(130, 160, 110), Off = C(38, 58, 32), OnAccent = C(10, 18, 0) },
	Noir = { Window = C(10, 8, 9), Side = C(5, 4, 5), Panel = C(8, 6, 7), Card = C(14, 10, 12), Element = C(23, 17, 20), Hover = C(36, 26, 31), Border = C(0, 0, 0), Accent = C(255, 92, 150), Text = C(250, 240, 245), Sub = C(150, 125, 136), Off = C(44, 32, 38) },
	Lagoon = { Window = C(8, 20, 30), Side = C(5, 15, 23), Panel = C(10, 25, 37), Card = C(13, 31, 45), Element = C(19, 43, 61), Hover = C(27, 58, 80), Border = C(2, 8, 13), Accent = C(34, 230, 222), Text = C(225, 248, 252), Sub = C(110, 160, 175), Off = C(30, 60, 78), OnAccent = C(4, 24, 30) },
}

for _, th in pairs(Themes) do
	local card, text = th.Card, th.Text
	local lum = 0.299 * card.R + 0.587 * card.G + 0.114 * card.B
	th.Border = card:Lerp(text, lum > 0.5 and 0.3 or 0.38)
end

local ThemeOrder = { "Light", "Rose", "Crimson", "Onyx", "Dark", "Darker", "Aqua", "Lagoon", "Amethyst", "Emerald", "Toxic", "Noir" }

local ThemeLabels = {
	Light = "纯白 (Light)",
	Rose = "樱粉 (Rose)",
	Crimson = "绯红 (Crimson)",
	Onyx = "曜黑 (Onyx)",
	Dark = "深色 (Dark)",
	Darker = "极夜 (Darker)",
	Aqua = "水青 (Aqua)",
	Lagoon = "深海 (Lagoon)",
	Amethyst = "紫晶 (Amethyst)",
	Emerald = "翡翠 (Emerald)",
	Toxic = "霓虹 (Toxic)",
	Noir = "暗粉 (Noir)",
}

local CialloHub = {
	Version = "1.3.5",
	Flags = {},
	Options = {},
	ThemeName = "Light",
	ThemeOrder = table.clone(ThemeOrder),
	ThemeLabels = ThemeLabels,
	ToggleKeys = { Enum.KeyCode.Insert, Enum.KeyCode.RightShift },
	ConfigFolder = "CialloHub",
	IntroIcon = INTRO_ICON,
	Windows = {},
	_conns = {}, _glass = {}, _glassLayers = {}, _binds = {}, _paints = {}, _drops = {}, _configLists = {}, _tips = {}, _index = {}, _scrollers = {},
	TipLength = 16,
	ButtonTipLength = 22,
	TitleCase = false,
	TextScale = 1.15,
	ThemeSweep = true,
	BlurMode = "Parts",
	BlurSize = 16,
	_wipes = {},
}

local TitleSmall = {
	a = true, an = true, the = true, ["and"] = true, but = true, ["or"] = true, nor = true, ["for"] = true, so = true, yet = true,
	as = true, at = true, by = true, ["in"] = true, of = true, off = true, on = true, per = true, to = true, up = true,
	via = true, vs = true, ["if"] = true,
}
local function TC(s)
	s = tostring(s == nil and "" or s)
	if CialloHub.TitleCase == false then return s end
	local total = 0
	for _ in s:gmatch("%S+") do total = total + 1 end
	local n, force = 0, true
	return (s:gsub("%S+", function(w)
		n = n + 1
		local must = force or n == total
		force = w:find("[:?!]$") ~= nil
		if w:find("://", 1, true) or w:find("@", 1, true) or w:find("%a%.%a") then return w end
		local idx = 0
		local slash = w:find("/", 1, true) ~= nil
		return (w:gsub("[^%-/]+", function(part)
			idx = idx + 1
			if part:find("%u") then return part end
			local pre, c, rest = part:match("^(%p*)(%l)(.*)$")
			if not pre then return part end
			local core = part:lower():gsub("[^%a]", "")
			if TitleSmall[core] and not (slash or (must and idx == 1)) then return part end
			return pre .. c:upper() .. rest
		end))
	end))
end

local T = { Font = Enum.Font.BuilderSansMedium, FontBold = Enum.Font.BuilderSansBold, FontBlack = Enum.Font.BuilderSansExtraBold }
for k, v in pairs(Themes.Light) do T[k] = v end
T.OnAccent = Color3.new(1, 1, 1)
CialloHub.Theme = T

local GLASS_KEYS = { Window = true, Side = true, Panel = true, Card = true, Element = true }
local Lighting = game:GetService("Lighting")
local GuiService = game:GetService("GuiService")

local GLASS = { Window = 0.32, Side = 0.5, Panel = 0.55, Card = 0.58, Element = 0.62, Popup = 0.1 }
local glassOn = false

local function glassAlpha(key, base)
	base = base or 0
	local g = T.Glass
	if key == "Hide" then return g and 1 or base end
	if not g or not g[key] then return base end
	return base + (1 - base) * g[key]
end
CialloHub.GlassAlpha = glassAlpha

local BLUR_DIST = 0.001
local CORNER_STEPS = 8
local BASE_RADIUS = 12

local blurDOF, blurConn
local blurSlots = {}

local function newSlot()
	local part = Instance.new("Part")
	part.Name = "CialloHub_Glass"
	part.Color = Color3.new(0, 0, 0)
	part.Material = Enum.Material.Glass
	part.Size = Vector3.new(1, 1, 0)
	part.Anchored = true
	part.CanCollide = false
	part.CanQuery = false
	part.CanTouch = false
	part.Locked = true
	part.CastShadow = false
	part.Transparency = 0.98
	local mesh = Instance.new("SpecialMesh")
	mesh.MeshType = Enum.MeshType.Brick
	mesh.Offset = Vector3.new(0, 0, -0.000001)
	mesh.Parent = part
	return { part = part, mesh = mesh }
end

local function getSlot(i)
	local s = blurSlots[i]
	if not s then
		s = newSlot()
		blurSlots[i] = s
	end
	return s
end

local function placeSlot(cam, i, x0, y0, x1, y1)
	local s = getSlot(i)
	if x1 - x0 < 1 or y1 - y0 < 1 then
		if s.part.Parent then s.part.Parent = nil end
		return
	end
	local function at(x, y)
		local r = cam:ViewportPointToRay(x, y)
		return r.Origin + r.Direction * BLUR_DIST
	end
	local tl, tr, br = at(x0, y0), at(x1, y0), at(x1, y1)
	s.part.CFrame = CFrame.fromMatrix((tl + br) / 2, cam.CFrame.RightVector, cam.CFrame.UpVector)
	s.mesh.Scale = Vector3.new((tr - tl).Magnitude, (tr - br).Magnitude, 0)
	if s.part.Parent ~= cam then s.part.Parent = cam end
end

local function hideFrom(i)
	for n = i, #blurSlots do
		local s = blurSlots[n]
		if s and s.part.Parent then s.part.Parent = nil end
	end
end

local function stopBlur()
	if blurConn then blurConn:Disconnect() blurConn = nil end
	local fx = blurDOF
	blurDOF = nil
	for _, s in ipairs(blurSlots) do
		pcall(function() s.part:Destroy() end)
	end
	blurSlots = {}
	if fx then
		local prop = fx:IsA("BlurEffect") and "Size" or "NearIntensity"
		pcall(function() TS:Create(fx, TweenInfo.new(0.2, Enum.EasingStyle.Quad), { [prop] = 0 }):Play() end)
		task.delay(0.25, function() pcall(function() fx:Destroy() end) end)
	end
end

local blurSig = { 0, 0, 0, 0, 0, 0, 0, 0 }
local blurLastCF
local blurParts, blurOffs, blurCFs, blurN = {}, {}, {}, 0

local function blurStep()
	local cam = workspace.CurrentCamera
	if not cam then return end

	local main, mscale
	for _, w in ipairs(CialloHub.Windows) do
		if w.Visible and w._main and w._main.Parent and w._main.Visible then
			main, mscale = w._main, w._scale
			break
		end
	end
	if not main then
		if blurSig[1] ~= -1 then
			blurSig[1] = -1
			blurN = 0
			hideFrom(1)
		end
		return
	end

	local gs = CialloHub.Gui and CialloHub.Gui.AbsoluteSize
	local pos, size = main.AbsolutePosition, main.AbsoluteSize
	if gs and gs.X > 0 and gs.Y > 0 then
		local mp, ms = main.Position, main.Size
		local sc = mscale and mscale.Scale or 1
		pos = Vector2.new(mp.X.Scale * gs.X + mp.X.Offset, mp.Y.Scale * gs.Y + mp.Y.Offset)
		size = Vector2.new((ms.X.Scale * gs.X + ms.X.Offset) * sc, (ms.Y.Scale * gs.Y + ms.Y.Offset) * sc)
		local vs = cam.ViewportSize
		pos = Vector2.new(pos.X * vs.X / gs.X, pos.Y * vs.Y / gs.Y)
		size = Vector2.new(size.X * vs.X / gs.X, size.Y * vs.Y / gs.Y)
	end
	pos = pos + (CialloHub.GlassBlurOffset or Vector2.zero)

	local ins = CialloHub.GlassBlurInset or 6
	local x0, y0 = pos.X + ins, pos.Y + ins
	local x1, y1 = pos.X + size.X - ins, pos.Y + size.Y - ins
	local bw, bh = x1 - x0, y1 - y0
	if bw <= 1 or bh <= 1 then
		if blurSig[1] ~= -1 then
			blurSig[1] = -1
			blurN = 0
			hideFrom(1)
		end
		return
	end

	local kk = size.X / math.max(main.Size.X.Offset, 1)
	local radius = ((CialloHub.GlassBlurRadius or BASE_RADIUS) * kk) + ins * 0.5
	radius = math.clamp(radius, 0, math.min(bw, bh) / 2)

	local vp, fov, camCF = cam.ViewportSize, cam.FieldOfView, cam.CFrame
	local first = blurSlots[1]
	local sg = blurSig
	local changed = not (first and first.built) or sg[1] ~= x0 or sg[2] ~= y0 or sg[3] ~= x1 or sg[4] ~= y1
		or sg[5] ~= radius or sg[6] ~= vp.X or sg[7] ~= vp.Y or sg[8] ~= fov

	if changed then
		sg[1], sg[2], sg[3], sg[4], sg[5], sg[6], sg[7], sg[8] = x0, y0, x1, y1, radius, vp.X, vp.Y, fov
		local rects, n = {}, 0
		local function add(a, b, c, d)
			n = n + 1
			rects[n] = { a, b, c, d }
		end
		if radius < 2 then
			add(x0, y0, x1, y1)
		else
			add(x0, y0 + radius, x1, y1 - radius)
			local band = radius / CORNER_STEPS
			for st = 1, CORNER_STEPS do
				local top = (st - 1) * band
				local mid = top + band / 2
				local dy = radius - mid
				local cut = radius - math.sqrt(math.max(radius * radius - dy * dy, 0))
				add(x0 + cut, y0 + top, x1 - cut, y0 + top + band + 1)
				add(x0 + cut, y1 - top - band - 1, x1 - cut, y1 - top)
			end
		end
		local function at(x, y)
			local ray = cam:ViewportPointToRay(x, y)
			return camCF:PointToObjectSpace(ray.Origin + ray.Direction * BLUR_DIST)
		end
		blurN = 0
		for i = 1, n do
			local r = rects[i]
			local s = getSlot(i)
			s.built = true
			if r[3] - r[1] < 1 or r[4] - r[2] < 1 then
				s.active = false
				if s.part.Parent then s.part.Parent = nil end
			else
				local tl, tr, br = at(r[1], r[2]), at(r[3], r[2]), at(r[3], r[4])
				s.off = CFrame.fromMatrix((tl + br) / 2, Vector3.xAxis, Vector3.yAxis)
				s.mesh.Scale = Vector3.new((tr - tl).Magnitude, (tr - br).Magnitude, 0)
				s.active = true
				if s.part.Parent ~= cam then s.part.Parent = cam end
				blurN = blurN + 1
				blurParts[blurN] = s.part
				blurOffs[blurN] = s.off
			end
		end
		for i = #blurParts, blurN + 1, -1 do
			blurParts[i] = nil
			blurOffs[i] = nil
			blurCFs[i] = nil
		end
		hideFrom(n + 1)
	elseif camCF == blurLastCF then
		return
	end

	blurLastCF = camCF
	for i = 1, blurN do blurCFs[i] = camCF * blurOffs[i] end
	local ok = pcall(workspace.BulkMoveTo, workspace, blurParts, blurCFs, Enum.BulkMoveMode.FireCFrameChanged)
	if not ok then
		for i = 1, blurN do blurParts[i].CFrame = blurCFs[i] end
	end
end

local function startBlur()
	if blurDOF or blurConn then return end
	if CialloHub.BlurMode == "Screen" then
		local fx = Instance.new("BlurEffect")
		fx.Name = "CialloHub_Blur"
		fx.Size = 0
		pcall(function() fx.Parent = Lighting end)
		blurDOF = fx
		TS:Create(fx, TweenInfo.new(0.25, Enum.EasingStyle.Quad), { Size = CialloHub.BlurSize or 16 }):Play()
		return
	end
	local dof = Instance.new("DepthOfFieldEffect")
	dof.Name = "CialloHub_Blur"
	dof.FarIntensity = 0
	dof.InFocusRadius = 0.1
	dof.FocusDistance = 0.05
	dof.NearIntensity = 0
	pcall(function() dof.Parent = Lighting end)
	blurDOF = dof
	TS:Create(dof, TweenInfo.new(0.25, Enum.EasingStyle.Quad), { NearIntensity = 1 }):Play()
	blurConn = RunService.RenderStepped:Connect(blurStep)
end

local function updateBlur()
	local want = false
	if glassOn and CialloHub.GlassBlur ~= false then
		for _, w in ipairs(CialloHub.Windows) do
			if w.Visible and w._main and w._main.Parent then want = true break end
		end
	end
	if want then startBlur() else stopBlur() end
end
CialloHub.UpdateBlur = updateBlur

function CialloHub:SetBlurSize(v)
	self.BlurSize = math.clamp(tonumber(v) or 16, 0, 56)
	if blurDOF and blurDOF:IsA("BlurEffect") then blurDOF.Size = self.BlurSize end
end

function CialloHub:SetGlassBlur(v)
	self.GlassBlur = v and true or false
	updateBlur()
end

local function ensureFolder(path)
	pcall(function()
		if makefolder and isfolder and not isfolder(path) then makefolder(path) end
	end)
end

local BuiltinIcons = {
	["lucide-accessibility"] = "rbxassetid://10709751939",
	["lucide-activity"] = "rbxassetid://10709752035",
	["lucide-air-vent"] = "rbxassetid://10709752131",
	["lucide-airplay"] = "rbxassetid://10709752254",
	["lucide-alarm-check"] = "rbxassetid://10709752405",
	["lucide-alarm-clock"] = "rbxassetid://10709752630",
	["lucide-alarm-clock-off"] = "rbxassetid://10709752508",
	["lucide-alarm-minus"] = "rbxassetid://10709752732",
	["lucide-alarm-plus"] = "rbxassetid://10709752825",
	["lucide-album"] = "rbxassetid://10709752906",
	["lucide-alert-circle"] = "rbxassetid://10709752996",
	["lucide-alert-octagon"] = "rbxassetid://10709753064",
	["lucide-alert-triangle"] = "rbxassetid://10709753149",
	["lucide-align-center"] = "rbxassetid://10709753570",
	["lucide-align-center-horizontal"] = "rbxassetid://10709753272",
	["lucide-align-center-vertical"] = "rbxassetid://10709753421",
	["lucide-align-end-horizontal"] = "rbxassetid://10709753692",
	["lucide-align-end-vertical"] = "rbxassetid://10709753808",
	["lucide-align-horizontal-distribute-center"] = "rbxassetid://10747779791",
	["lucide-align-horizontal-distribute-end"] = "rbxassetid://10747784534",
	["lucide-align-horizontal-distribute-start"] = "rbxassetid://10709754118",
	["lucide-align-horizontal-justify-center"] = "rbxassetid://10709754204",
	["lucide-align-horizontal-justify-end"] = "rbxassetid://10709754317",
	["lucide-align-horizontal-justify-start"] = "rbxassetid://10709754436",
	["lucide-align-horizontal-space-around"] = "rbxassetid://10709754590",
	["lucide-align-horizontal-space-between"] = "rbxassetid://10709754749",
	["lucide-align-justify"] = "rbxassetid://10709759610",
	["lucide-align-left"] = "rbxassetid://10709759764",
	["lucide-align-right"] = "rbxassetid://10709759895",
	["lucide-align-start-horizontal"] = "rbxassetid://10709760051",
	["lucide-align-start-vertical"] = "rbxassetid://10709760244",
	["lucide-align-vertical-distribute-center"] = "rbxassetid://10709760351",
	["lucide-align-vertical-distribute-end"] = "rbxassetid://10709760434",
	["lucide-align-vertical-distribute-start"] = "rbxassetid://10709760612",
	["lucide-align-vertical-justify-center"] = "rbxassetid://10709760814",
	["lucide-align-vertical-justify-end"] = "rbxassetid://10709761003",
	["lucide-align-vertical-justify-start"] = "rbxassetid://10709761176",
	["lucide-align-vertical-space-around"] = "rbxassetid://10709761324",
	["lucide-align-vertical-space-between"] = "rbxassetid://10709761434",
	["lucide-anchor"] = "rbxassetid://10709761530",
	["lucide-angry"] = "rbxassetid://10709761629",
	["lucide-annoyed"] = "rbxassetid://10709761722",
	["lucide-aperture"] = "rbxassetid://10709761813",
	["lucide-apple"] = "rbxassetid://10709761889",
	["lucide-archive"] = "rbxassetid://10709762233",
	["lucide-archive-restore"] = "rbxassetid://10709762058",
	["lucide-armchair"] = "rbxassetid://10709762327",
	["lucide-arrow-big-down"] = "rbxassetid://10747796644",
	["lucide-arrow-big-left"] = "rbxassetid://10709762574",
	["lucide-arrow-big-right"] = "rbxassetid://10709762727",
	["lucide-arrow-big-up"] = "rbxassetid://10709762879",
	["lucide-arrow-down"] = "rbxassetid://10709767827",
	["lucide-arrow-down-circle"] = "rbxassetid://10709763034",
	["lucide-arrow-down-left"] = "rbxassetid://10709767656",
	["lucide-arrow-down-right"] = "rbxassetid://10709767750",
	["lucide-arrow-left"] = "rbxassetid://10709768114",
	["lucide-arrow-left-circle"] = "rbxassetid://10709767936",
	["lucide-arrow-left-right"] = "rbxassetid://10709768019",
	["lucide-arrow-right"] = "rbxassetid://10709768347",
	["lucide-arrow-right-circle"] = "rbxassetid://10709768226",
	["lucide-arrow-up"] = "rbxassetid://10709768939",
	["lucide-arrow-up-circle"] = "rbxassetid://10709768432",
	["lucide-arrow-up-down"] = "rbxassetid://10709768538",
	["lucide-arrow-up-left"] = "rbxassetid://10709768661",
	["lucide-arrow-up-right"] = "rbxassetid://10709768787",
	["lucide-asterisk"] = "rbxassetid://10709769095",
	["lucide-at-sign"] = "rbxassetid://10709769286",
	["lucide-award"] = "rbxassetid://10709769406",
	["lucide-axe"] = "rbxassetid://10709769508",
	["lucide-axis-3d"] = "rbxassetid://10709769598",
	["lucide-baby"] = "rbxassetid://10709769732",
	["lucide-backpack"] = "rbxassetid://10709769841",
	["lucide-baggage-claim"] = "rbxassetid://10709769935",
	["lucide-banana"] = "rbxassetid://10709770005",
	["lucide-banknote"] = "rbxassetid://10709770178",
	["lucide-bar-chart"] = "rbxassetid://10709773755",
	["lucide-bar-chart-2"] = "rbxassetid://10709770317",
	["lucide-bar-chart-3"] = "rbxassetid://10709770431",
	["lucide-bar-chart-4"] = "rbxassetid://10709770560",
	["lucide-bar-chart-horizontal"] = "rbxassetid://10709773669",
	["lucide-barcode"] = "rbxassetid://10747360675",
	["lucide-baseline"] = "rbxassetid://10709773863",
	["lucide-bath"] = "rbxassetid://10709773963",
	["lucide-battery"] = "rbxassetid://10709774640",
	["lucide-battery-charging"] = "rbxassetid://10709774068",
	["lucide-battery-full"] = "rbxassetid://10709774206",
	["lucide-battery-low"] = "rbxassetid://10709774370",
	["lucide-battery-medium"] = "rbxassetid://10709774513",
	["lucide-beaker"] = "rbxassetid://10709774756",
	["lucide-bed"] = "rbxassetid://10709775036",
	["lucide-bed-double"] = "rbxassetid://10709774864",
	["lucide-bed-single"] = "rbxassetid://10709774968",
	["lucide-beer"] = "rbxassetid://10709775167",
	["lucide-bell"] = "rbxassetid://10709775704",
	["lucide-bell-minus"] = "rbxassetid://10709775241",
	["lucide-bell-off"] = "rbxassetid://10709775320",
	["lucide-bell-plus"] = "rbxassetid://10709775448",
	["lucide-bell-ring"] = "rbxassetid://10709775560",
	["lucide-bike"] = "rbxassetid://10709775894",
	["lucide-binary"] = "rbxassetid://10709776050",
	["lucide-bitcoin"] = "rbxassetid://10709776126",
	["lucide-bluetooth"] = "rbxassetid://10709776655",
	["lucide-bluetooth-connected"] = "rbxassetid://10709776240",
	["lucide-bluetooth-off"] = "rbxassetid://10709776344",
	["lucide-bluetooth-searching"] = "rbxassetid://10709776501",
	["lucide-bold"] = "rbxassetid://10747813908",
	["lucide-bomb"] = "rbxassetid://10709781460",
	["lucide-bone"] = "rbxassetid://10709781605",
	["lucide-book"] = "rbxassetid://10709781824",
	["lucide-book-open"] = "rbxassetid://10709781717",
	["lucide-bookmark"] = "rbxassetid://10709782154",
	["lucide-bookmark-minus"] = "rbxassetid://10709781919",
	["lucide-bookmark-plus"] = "rbxassetid://10709782044",
	["lucide-bot"] = "rbxassetid://10709782230",
	["lucide-box"] = "rbxassetid://10709782497",
	["lucide-box-select"] = "rbxassetid://10709782342",
	["lucide-boxes"] = "rbxassetid://10709782582",
	["lucide-briefcase"] = "rbxassetid://10709782662",
	["lucide-brush"] = "rbxassetid://10709782758",
	["lucide-bug"] = "rbxassetid://10709782845",
	["lucide-building"] = "rbxassetid://10709783051",
	["lucide-building-2"] = "rbxassetid://10709782939",
	["lucide-bus"] = "rbxassetid://10709783137",
	["lucide-cake"] = "rbxassetid://10709783217",
	["lucide-calculator"] = "rbxassetid://10709783311",
	["lucide-calendar"] = "rbxassetid://10709789505",
	["lucide-calendar-check"] = "rbxassetid://10709783474",
	["lucide-calendar-check-2"] = "rbxassetid://10709783392",
	["lucide-calendar-clock"] = "rbxassetid://10709783577",
	["lucide-calendar-days"] = "rbxassetid://10709783673",
	["lucide-calendar-heart"] = "rbxassetid://10709783835",
	["lucide-calendar-minus"] = "rbxassetid://10709783959",
	["lucide-calendar-off"] = "rbxassetid://10709788784",
	["lucide-calendar-plus"] = "rbxassetid://10709788937",
	["lucide-calendar-range"] = "rbxassetid://10709789053",
	["lucide-calendar-search"] = "rbxassetid://10709789200",
	["lucide-calendar-x"] = "rbxassetid://10709789407",
	["lucide-calendar-x-2"] = "rbxassetid://10709789329",
	["lucide-camera"] = "rbxassetid://10709789686",
	["lucide-camera-off"] = "rbxassetid://10747822677",
	["lucide-car"] = "rbxassetid://10709789810",
	["lucide-carrot"] = "rbxassetid://10709789960",
	["lucide-cast"] = "rbxassetid://10709790097",
	["lucide-charge"] = "rbxassetid://10709790202",
	["lucide-check"] = "rbxassetid://10709790644",
	["lucide-check-circle"] = "rbxassetid://10709790387",
	["lucide-check-circle-2"] = "rbxassetid://10709790298",
	["lucide-check-square"] = "rbxassetid://10709790537",
	["lucide-chef-hat"] = "rbxassetid://10709790757",
	["lucide-cherry"] = "rbxassetid://10709790875",
	["lucide-chevron-down"] = "rbxassetid://10709790948",
	["lucide-chevron-first"] = "rbxassetid://10709791015",
	["lucide-chevron-last"] = "rbxassetid://10709791130",
	["lucide-chevron-left"] = "rbxassetid://10709791281",
	["lucide-chevron-right"] = "rbxassetid://10709791437",
	["lucide-chevron-up"] = "rbxassetid://10709791523",
	["lucide-chevrons-down"] = "rbxassetid://10709796864",
	["lucide-chevrons-down-up"] = "rbxassetid://10709791632",
	["lucide-chevrons-left"] = "rbxassetid://10709797151",
	["lucide-chevrons-left-right"] = "rbxassetid://10709797006",
	["lucide-chevrons-right"] = "rbxassetid://10709797382",
	["lucide-chevrons-right-left"] = "rbxassetid://10709797274",
	["lucide-chevrons-up"] = "rbxassetid://10709797622",
	["lucide-chevrons-up-down"] = "rbxassetid://10709797508",
	["lucide-chrome"] = "rbxassetid://10709797725",
	["lucide-circle"] = "rbxassetid://10709798174",
	["lucide-circle-dot"] = "rbxassetid://10709797837",
	["lucide-circle-ellipsis"] = "rbxassetid://10709797985",
	["lucide-circle-slashed"] = "rbxassetid://10709798100",
	["lucide-citrus"] = "rbxassetid://10709798276",
	["lucide-clapperboard"] = "rbxassetid://10709798350",
	["lucide-clipboard"] = "rbxassetid://10709799288",
	["lucide-clipboard-check"] = "rbxassetid://10709798443",
	["lucide-clipboard-copy"] = "rbxassetid://10709798574",
	["lucide-clipboard-edit"] = "rbxassetid://10709798682",
	["lucide-clipboard-list"] = "rbxassetid://10709798792",
	["lucide-clipboard-signature"] = "rbxassetid://10709798890",
	["lucide-clipboard-type"] = "rbxassetid://10709798999",
	["lucide-clipboard-x"] = "rbxassetid://10709799124",
	["lucide-clock"] = "rbxassetid://10709805144",
	["lucide-clock-1"] = "rbxassetid://10709799535",
	["lucide-clock-10"] = "rbxassetid://10709799718",
	["lucide-clock-11"] = "rbxassetid://10709799818",
	["lucide-clock-12"] = "rbxassetid://10709799962",
	["lucide-clock-2"] = "rbxassetid://10709803876",
	["lucide-clock-3"] = "rbxassetid://10709803989",
	["lucide-clock-4"] = "rbxassetid://10709804164",
	["lucide-clock-5"] = "rbxassetid://10709804291",
	["lucide-clock-6"] = "rbxassetid://10709804435",
	["lucide-clock-7"] = "rbxassetid://10709804599",
	["lucide-clock-8"] = "rbxassetid://10709804784",
	["lucide-clock-9"] = "rbxassetid://10709804996",
	["lucide-cloud"] = "rbxassetid://10709806740",
	["lucide-cloud-cog"] = "rbxassetid://10709805262",
	["lucide-cloud-drizzle"] = "rbxassetid://10709805371",
	["lucide-cloud-fog"] = "rbxassetid://10709805477",
	["lucide-cloud-hail"] = "rbxassetid://10709805596",
	["lucide-cloud-lightning"] = "rbxassetid://10709805727",
	["lucide-cloud-moon"] = "rbxassetid://10709805942",
	["lucide-cloud-moon-rain"] = "rbxassetid://10709805838",
	["lucide-cloud-off"] = "rbxassetid://10709806060",
	["lucide-cloud-rain"] = "rbxassetid://10709806277",
	["lucide-cloud-rain-wind"] = "rbxassetid://10709806166",
	["lucide-cloud-snow"] = "rbxassetid://10709806374",
	["lucide-cloud-sun"] = "rbxassetid://10709806631",
	["lucide-cloud-sun-rain"] = "rbxassetid://10709806475",
	["lucide-cloudy"] = "rbxassetid://10709806859",
	["lucide-clover"] = "rbxassetid://10709806995",
	["lucide-code"] = "rbxassetid://10709810463",
	["lucide-code-2"] = "rbxassetid://10709807111",
	["lucide-codepen"] = "rbxassetid://10709810534",
	["lucide-codesandbox"] = "rbxassetid://10709810676",
	["lucide-coffee"] = "rbxassetid://10709810814",
	["lucide-cog"] = "rbxassetid://10709810948",
	["lucide-coins"] = "rbxassetid://10709811110",
	["lucide-columns"] = "rbxassetid://10709811261",
	["lucide-command"] = "rbxassetid://10709811365",
	["lucide-compass"] = "rbxassetid://10709811445",
	["lucide-component"] = "rbxassetid://10709811595",
	["lucide-concierge-bell"] = "rbxassetid://10709811706",
	["lucide-connection"] = "rbxassetid://10747361219",
	["lucide-contact"] = "rbxassetid://10709811834",
	["lucide-contrast"] = "rbxassetid://10709811939",
	["lucide-cookie"] = "rbxassetid://10709812067",
	["lucide-copy"] = "rbxassetid://10709812159",
	["lucide-copyleft"] = "rbxassetid://10709812251",
	["lucide-copyright"] = "rbxassetid://10709812311",
	["lucide-corner-down-left"] = "rbxassetid://10709812396",
	["lucide-corner-down-right"] = "rbxassetid://10709812485",
	["lucide-corner-left-down"] = "rbxassetid://10709812632",
	["lucide-corner-left-up"] = "rbxassetid://10709812784",
	["lucide-corner-right-down"] = "rbxassetid://10709812939",
	["lucide-corner-right-up"] = "rbxassetid://10709813094",
	["lucide-corner-up-left"] = "rbxassetid://10709813185",
	["lucide-corner-up-right"] = "rbxassetid://10709813281",
	["lucide-cpu"] = "rbxassetid://10709813383",
	["lucide-croissant"] = "rbxassetid://10709818125",
	["lucide-crop"] = "rbxassetid://10709818245",
	["lucide-cross"] = "rbxassetid://10709818399",
	["lucide-crosshair"] = "rbxassetid://10709818534",
	["lucide-crown"] = "rbxassetid://10709818626",
	["lucide-cup-soda"] = "rbxassetid://10709818763",
	["lucide-curly-braces"] = "rbxassetid://10709818847",
	["lucide-currency"] = "rbxassetid://10709818931",
	["lucide-database"] = "rbxassetid://10709818996",
	["lucide-delete"] = "rbxassetid://10709819059",
	["lucide-diamond"] = "rbxassetid://10709819149",
	["lucide-dice-1"] = "rbxassetid://10709819266",
	["lucide-dice-2"] = "rbxassetid://10709819361",
	["lucide-dice-3"] = "rbxassetid://10709819508",
	["lucide-dice-4"] = "rbxassetid://10709819670",
	["lucide-dice-5"] = "rbxassetid://10709819801",
	["lucide-dice-6"] = "rbxassetid://10709819896",
	["lucide-dices"] = "rbxassetid://10723343321",
	["lucide-diff"] = "rbxassetid://10723343416",
	["lucide-disc"] = "rbxassetid://10723343537",
	["lucide-divide"] = "rbxassetid://10723343805",
	["lucide-divide-circle"] = "rbxassetid://10723343636",
	["lucide-divide-square"] = "rbxassetid://10723343737",
	["lucide-dollar-sign"] = "rbxassetid://10723343958",
	["lucide-download"] = "rbxassetid://10723344270",
	["lucide-download-cloud"] = "rbxassetid://10723344088",
	["lucide-droplet"] = "rbxassetid://10723344432",
	["lucide-droplets"] = "rbxassetid://10734883356",
	["lucide-drumstick"] = "rbxassetid://10723344737",
	["lucide-edit"] = "rbxassetid://10734883598",
	["lucide-edit-2"] = "rbxassetid://10723344885",
	["lucide-edit-3"] = "rbxassetid://10723345088",
	["lucide-egg"] = "rbxassetid://10723345518",
	["lucide-egg-fried"] = "rbxassetid://10723345347",
	["lucide-electricity"] = "rbxassetid://10723345749",
	["lucide-electricity-off"] = "rbxassetid://10723345643",
	["lucide-equal"] = "rbxassetid://10723345990",
	["lucide-equal-not"] = "rbxassetid://10723345866",
	["lucide-eraser"] = "rbxassetid://10723346158",
	["lucide-euro"] = "rbxassetid://10723346372",
	["lucide-expand"] = "rbxassetid://10723346553",
	["lucide-external-link"] = "rbxassetid://10723346684",
	["lucide-eye"] = "rbxassetid://10723346959",
	["lucide-eye-off"] = "rbxassetid://10723346871",
	["lucide-factory"] = "rbxassetid://10723347051",
	["lucide-fan"] = "rbxassetid://10723354359",
	["lucide-fast-forward"] = "rbxassetid://10723354521",
	["lucide-feather"] = "rbxassetid://10723354671",
	["lucide-figma"] = "rbxassetid://10723354801",
	["lucide-file"] = "rbxassetid://10723374641",
	["lucide-file-archive"] = "rbxassetid://10723354921",
	["lucide-file-audio"] = "rbxassetid://10723355148",
	["lucide-file-audio-2"] = "rbxassetid://10723355026",
	["lucide-file-axis-3d"] = "rbxassetid://10723355272",
	["lucide-file-badge"] = "rbxassetid://10723355622",
	["lucide-file-badge-2"] = "rbxassetid://10723355451",
	["lucide-file-bar-chart"] = "rbxassetid://10723355887",
	["lucide-file-bar-chart-2"] = "rbxassetid://10723355746",
	["lucide-file-box"] = "rbxassetid://10723355989",
	["lucide-file-check"] = "rbxassetid://10723356210",
	["lucide-file-check-2"] = "rbxassetid://10723356100",
	["lucide-file-clock"] = "rbxassetid://10723356329",
	["lucide-file-code"] = "rbxassetid://10723356507",
	["lucide-file-cog"] = "rbxassetid://10723356830",
	["lucide-file-cog-2"] = "rbxassetid://10723356676",
	["lucide-file-diff"] = "rbxassetid://10723357039",
	["lucide-file-digit"] = "rbxassetid://10723357151",
	["lucide-file-down"] = "rbxassetid://10723357322",
	["lucide-file-edit"] = "rbxassetid://10723357495",
	["lucide-file-heart"] = "rbxassetid://10723357637",
	["lucide-file-image"] = "rbxassetid://10723357790",
	["lucide-file-input"] = "rbxassetid://10723357933",
	["lucide-file-json"] = "rbxassetid://10723364435",
	["lucide-file-json-2"] = "rbxassetid://10723364361",
	["lucide-file-key"] = "rbxassetid://10723364605",
	["lucide-file-key-2"] = "rbxassetid://10723364515",
	["lucide-file-line-chart"] = "rbxassetid://10723364725",
	["lucide-file-lock"] = "rbxassetid://10723364957",
	["lucide-file-lock-2"] = "rbxassetid://10723364861",
	["lucide-file-minus"] = "rbxassetid://10723365254",
	["lucide-file-minus-2"] = "rbxassetid://10723365086",
	["lucide-file-output"] = "rbxassetid://10723365457",
	["lucide-file-pie-chart"] = "rbxassetid://10723365598",
	["lucide-file-plus"] = "rbxassetid://10723365877",
	["lucide-file-plus-2"] = "rbxassetid://10723365766",
	["lucide-file-question"] = "rbxassetid://10723365987",
	["lucide-file-scan"] = "rbxassetid://10723366167",
	["lucide-file-search"] = "rbxassetid://10723366550",
	["lucide-file-search-2"] = "rbxassetid://10723366340",
	["lucide-file-signature"] = "rbxassetid://10723366741",
	["lucide-file-spreadsheet"] = "rbxassetid://10723366962",
	["lucide-file-symlink"] = "rbxassetid://10723367098",
	["lucide-file-terminal"] = "rbxassetid://10723367244",
	["lucide-file-text"] = "rbxassetid://10723367380",
	["lucide-file-type"] = "rbxassetid://10723367606",
	["lucide-file-type-2"] = "rbxassetid://10723367509",
	["lucide-file-up"] = "rbxassetid://10723367734",
	["lucide-file-video"] = "rbxassetid://10723373884",
	["lucide-file-video-2"] = "rbxassetid://10723367834",
	["lucide-file-volume"] = "rbxassetid://10723374172",
	["lucide-file-volume-2"] = "rbxassetid://10723374030",
	["lucide-file-warning"] = "rbxassetid://10723374276",
	["lucide-file-x"] = "rbxassetid://10723374544",
	["lucide-file-x-2"] = "rbxassetid://10723374378",
	["lucide-files"] = "rbxassetid://10723374759",
	["lucide-film"] = "rbxassetid://10723374981",
	["lucide-filter"] = "rbxassetid://10723375128",
	["lucide-fingerprint"] = "rbxassetid://10723375250",
	["lucide-flag"] = "rbxassetid://10723375890",
	["lucide-flag-off"] = "rbxassetid://10723375443",
	["lucide-flag-triangle-left"] = "rbxassetid://10723375608",
	["lucide-flag-triangle-right"] = "rbxassetid://10723375727",
	["lucide-flame"] = "rbxassetid://10723376114",
	["lucide-flashlight"] = "rbxassetid://10723376471",
	["lucide-flashlight-off"] = "rbxassetid://10723376365",
	["lucide-flask-conical"] = "rbxassetid://10734883986",
	["lucide-flask-round"] = "rbxassetid://10723376614",
	["lucide-flip-horizontal"] = "rbxassetid://10723376884",
	["lucide-flip-horizontal-2"] = "rbxassetid://10723376745",
	["lucide-flip-vertical"] = "rbxassetid://10723377138",
	["lucide-flip-vertical-2"] = "rbxassetid://10723377026",
	["lucide-flower"] = "rbxassetid://10747830374",
	["lucide-flower-2"] = "rbxassetid://10723377305",
	["lucide-focus"] = "rbxassetid://10723377537",
	["lucide-folder"] = "rbxassetid://10723387563",
	["lucide-folder-archive"] = "rbxassetid://10723384478",
	["lucide-folder-check"] = "rbxassetid://10723384605",
	["lucide-folder-clock"] = "rbxassetid://10723384731",
	["lucide-folder-closed"] = "rbxassetid://10723384893",
	["lucide-folder-cog"] = "rbxassetid://10723385213",
	["lucide-folder-cog-2"] = "rbxassetid://10723385036",
	["lucide-folder-down"] = "rbxassetid://10723385338",
	["lucide-folder-edit"] = "rbxassetid://10723385445",
	["lucide-folder-heart"] = "rbxassetid://10723385545",
	["lucide-folder-input"] = "rbxassetid://10723385721",
	["lucide-folder-key"] = "rbxassetid://10723385848",
	["lucide-folder-lock"] = "rbxassetid://10723386005",
	["lucide-folder-minus"] = "rbxassetid://10723386127",
	["lucide-folder-open"] = "rbxassetid://10723386277",
	["lucide-folder-output"] = "rbxassetid://10723386386",
	["lucide-folder-plus"] = "rbxassetid://10723386531",
	["lucide-folder-search"] = "rbxassetid://10723386787",
	["lucide-folder-search-2"] = "rbxassetid://10723386674",
	["lucide-folder-symlink"] = "rbxassetid://10723386930",
	["lucide-folder-tree"] = "rbxassetid://10723387085",
	["lucide-folder-up"] = "rbxassetid://10723387265",
	["lucide-folder-x"] = "rbxassetid://10723387448",
	["lucide-folders"] = "rbxassetid://10723387721",
	["lucide-form-input"] = "rbxassetid://10723387841",
	["lucide-forward"] = "rbxassetid://10723388016",
	["lucide-frame"] = "rbxassetid://10723394389",
	["lucide-framer"] = "rbxassetid://10723394565",
	["lucide-frown"] = "rbxassetid://10723394681",
	["lucide-fuel"] = "rbxassetid://10723394846",
	["lucide-function-square"] = "rbxassetid://10723395041",
	["lucide-gamepad"] = "rbxassetid://10723395457",
	["lucide-gamepad-2"] = "rbxassetid://10723395215",
	["lucide-gauge"] = "rbxassetid://10723395708",
	["lucide-gavel"] = "rbxassetid://10723395896",
	["lucide-gem"] = "rbxassetid://10723396000",
	["lucide-ghost"] = "rbxassetid://10723396107",
	["lucide-gift"] = "rbxassetid://10723396402",
	["lucide-gift-card"] = "rbxassetid://10723396225",
	["lucide-git-branch"] = "rbxassetid://10723396676",
	["lucide-git-branch-plus"] = "rbxassetid://10723396542",
	["lucide-git-commit"] = "rbxassetid://10723396812",
	["lucide-git-compare"] = "rbxassetid://10723396954",
	["lucide-git-fork"] = "rbxassetid://10723397049",
	["lucide-git-merge"] = "rbxassetid://10723397165",
	["lucide-git-pull-request"] = "rbxassetid://10723397431",
	["lucide-git-pull-request-closed"] = "rbxassetid://10723397268",
	["lucide-git-pull-request-draft"] = "rbxassetid://10734884302",
	["lucide-glass"] = "rbxassetid://10723397788",
	["lucide-glass-2"] = "rbxassetid://10723397529",
	["lucide-glass-water"] = "rbxassetid://10723397678",
	["lucide-glasses"] = "rbxassetid://10723397895",
	["lucide-globe"] = "rbxassetid://10723404337",
	["lucide-globe-2"] = "rbxassetid://10723398002",
	["lucide-grab"] = "rbxassetid://10723404472",
	["lucide-graduation-cap"] = "rbxassetid://10723404691",
	["lucide-grape"] = "rbxassetid://10723404822",
	["lucide-grid"] = "rbxassetid://10723404936",
	["lucide-grip-horizontal"] = "rbxassetid://10723405089",
	["lucide-grip-vertical"] = "rbxassetid://10723405236",
	["lucide-hammer"] = "rbxassetid://10723405360",
	["lucide-hand"] = "rbxassetid://10723405649",
	["lucide-hand-metal"] = "rbxassetid://10723405508",
	["lucide-hard-drive"] = "rbxassetid://10723405749",
	["lucide-hard-hat"] = "rbxassetid://10723405859",
	["lucide-hash"] = "rbxassetid://10723405975",
	["lucide-haze"] = "rbxassetid://10723406078",
	["lucide-headphones"] = "rbxassetid://10723406165",
	["lucide-heart"] = "rbxassetid://10723406885",
	["lucide-heart-crack"] = "rbxassetid://10723406299",
	["lucide-heart-handshake"] = "rbxassetid://10723406480",
	["lucide-heart-off"] = "rbxassetid://10723406662",
	["lucide-heart-pulse"] = "rbxassetid://10723406795",
	["lucide-help-circle"] = "rbxassetid://10723406988",
	["lucide-hexagon"] = "rbxassetid://10723407092",
	["lucide-highlighter"] = "rbxassetid://10723407192",
	["lucide-history"] = "rbxassetid://10723407335",
	["lucide-home"] = "rbxassetid://10723407389",
	["lucide-hourglass"] = "rbxassetid://10723407498",
	["lucide-ice-cream"] = "rbxassetid://10723414308",
	["lucide-image"] = "rbxassetid://10723415040",
	["lucide-image-minus"] = "rbxassetid://10723414487",
	["lucide-image-off"] = "rbxassetid://10723414677",
	["lucide-image-plus"] = "rbxassetid://10723414827",
	["lucide-import"] = "rbxassetid://10723415205",
	["lucide-inbox"] = "rbxassetid://10723415335",
	["lucide-indent"] = "rbxassetid://10723415494",
	["lucide-indian-rupee"] = "rbxassetid://10723415642",
	["lucide-infinity"] = "rbxassetid://10723415766",
	["lucide-info"] = "rbxassetid://10723415903",
	["lucide-inspect"] = "rbxassetid://10723416057",
	["lucide-italic"] = "rbxassetid://10723416195",
	["lucide-japanese-yen"] = "rbxassetid://10723416363",
	["lucide-joystick"] = "rbxassetid://10723416527",
	["lucide-key"] = "rbxassetid://10723416652",
	["lucide-keyboard"] = "rbxassetid://10723416765",
	["lucide-lamp"] = "rbxassetid://10723417513",
	["lucide-lamp-ceiling"] = "rbxassetid://10723416922",
	["lucide-lamp-desk"] = "rbxassetid://10723417016",
	["lucide-lamp-floor"] = "rbxassetid://10723417131",
	["lucide-lamp-wall-down"] = "rbxassetid://10723417240",
	["lucide-lamp-wall-up"] = "rbxassetid://10723417356",
	["lucide-landmark"] = "rbxassetid://10723417608",
	["lucide-languages"] = "rbxassetid://10723417703",
	["lucide-laptop"] = "rbxassetid://10723423881",
	["lucide-laptop-2"] = "rbxassetid://10723417797",
	["lucide-lasso"] = "rbxassetid://10723424235",
	["lucide-lasso-select"] = "rbxassetid://10723424058",
	["lucide-laugh"] = "rbxassetid://10723424372",
	["lucide-layers"] = "rbxassetid://10723424505",
	["lucide-layout"] = "rbxassetid://10723425376",
	["lucide-layout-dashboard"] = "rbxassetid://10723424646",
	["lucide-layout-grid"] = "rbxassetid://10723424838",
	["lucide-layout-list"] = "rbxassetid://10723424963",
	["lucide-layout-template"] = "rbxassetid://10723425187",
	["lucide-leaf"] = "rbxassetid://10723425539",
	["lucide-library"] = "rbxassetid://10723425615",
	["lucide-life-buoy"] = "rbxassetid://10723425685",
	["lucide-lightbulb"] = "rbxassetid://10723425852",
	["lucide-lightbulb-off"] = "rbxassetid://10723425762",
	["lucide-line-chart"] = "rbxassetid://10723426393",
	["lucide-link"] = "rbxassetid://10723426722",
	["lucide-link-2"] = "rbxassetid://10723426595",
	["lucide-link-2-off"] = "rbxassetid://10723426513",
	["lucide-list"] = "rbxassetid://10723433811",
	["lucide-list-checks"] = "rbxassetid://10734884548",
	["lucide-list-end"] = "rbxassetid://10723426886",
	["lucide-list-minus"] = "rbxassetid://10723426986",
	["lucide-list-music"] = "rbxassetid://10723427081",
	["lucide-list-ordered"] = "rbxassetid://10723427199",
	["lucide-list-plus"] = "rbxassetid://10723427334",
	["lucide-list-start"] = "rbxassetid://10723427494",
	["lucide-list-video"] = "rbxassetid://10723427619",
	["lucide-list-x"] = "rbxassetid://10723433655",
	["lucide-loader"] = "rbxassetid://10723434070",
	["lucide-loader-2"] = "rbxassetid://10723433935",
	["lucide-locate"] = "rbxassetid://10723434557",
	["lucide-locate-fixed"] = "rbxassetid://10723434236",
	["lucide-locate-off"] = "rbxassetid://10723434379",
	["lucide-lock"] = "rbxassetid://10723434711",
	["lucide-log-in"] = "rbxassetid://10723434830",
	["lucide-log-out"] = "rbxassetid://10723434906",
	["lucide-luggage"] = "rbxassetid://10723434993",
	["lucide-magnet"] = "rbxassetid://10723435069",
	["lucide-mail"] = "rbxassetid://10734885430",
	["lucide-mail-check"] = "rbxassetid://10723435182",
	["lucide-mail-minus"] = "rbxassetid://10723435261",
	["lucide-mail-open"] = "rbxassetid://10723435342",
	["lucide-mail-plus"] = "rbxassetid://10723435443",
	["lucide-mail-question"] = "rbxassetid://10723435515",
	["lucide-mail-search"] = "rbxassetid://10734884739",
	["lucide-mail-warning"] = "rbxassetid://10734885015",
	["lucide-mail-x"] = "rbxassetid://10734885247",
	["lucide-mails"] = "rbxassetid://10734885614",
	["lucide-map"] = "rbxassetid://10734886202",
	["lucide-map-pin"] = "rbxassetid://10734886004",
	["lucide-map-pin-off"] = "rbxassetid://10734885803",
	["lucide-maximize"] = "rbxassetid://10734886735",
	["lucide-maximize-2"] = "rbxassetid://10734886496",
	["lucide-medal"] = "rbxassetid://10734887072",
	["lucide-megaphone"] = "rbxassetid://10734887454",
	["lucide-megaphone-off"] = "rbxassetid://10734887311",
	["lucide-meh"] = "rbxassetid://10734887603",
	["lucide-menu"] = "rbxassetid://10734887784",
	["lucide-message-circle"] = "rbxassetid://10734888000",
	["lucide-message-square"] = "rbxassetid://10734888228",
	["lucide-mic"] = "rbxassetid://10734888864",
	["lucide-mic-2"] = "rbxassetid://10734888430",
	["lucide-mic-off"] = "rbxassetid://10734888646",
	["lucide-microscope"] = "rbxassetid://10734889106",
	["lucide-microwave"] = "rbxassetid://10734895076",
	["lucide-milestone"] = "rbxassetid://10734895310",
	["lucide-minimize"] = "rbxassetid://10734895698",
	["lucide-minimize-2"] = "rbxassetid://10734895530",
	["lucide-minus"] = "rbxassetid://10734896206",
	["lucide-minus-circle"] = "rbxassetid://10734895856",
	["lucide-minus-square"] = "rbxassetid://10734896029",
	["lucide-monitor"] = "rbxassetid://10734896881",
	["lucide-monitor-off"] = "rbxassetid://10734896360",
	["lucide-monitor-speaker"] = "rbxassetid://10734896512",
	["lucide-moon"] = "rbxassetid://10734897102",
	["lucide-more-horizontal"] = "rbxassetid://10734897250",
	["lucide-more-vertical"] = "rbxassetid://10734897387",
	["lucide-mountain"] = "rbxassetid://10734897956",
	["lucide-mountain-snow"] = "rbxassetid://10734897665",
	["lucide-mouse"] = "rbxassetid://10734898592",
	["lucide-mouse-pointer"] = "rbxassetid://10734898476",
	["lucide-mouse-pointer-2"] = "rbxassetid://10734898194",
	["lucide-mouse-pointer-click"] = "rbxassetid://10734898355",
	["lucide-move"] = "rbxassetid://10734900011",
	["lucide-move-3d"] = "rbxassetid://10734898756",
	["lucide-move-diagonal"] = "rbxassetid://10734899164",
	["lucide-move-diagonal-2"] = "rbxassetid://10734898934",
	["lucide-move-horizontal"] = "rbxassetid://10734899414",
	["lucide-move-vertical"] = "rbxassetid://10734899821",
	["lucide-music"] = "rbxassetid://10734905958",
	["lucide-music-2"] = "rbxassetid://10734900215",
	["lucide-music-3"] = "rbxassetid://10734905665",
	["lucide-music-4"] = "rbxassetid://10734905823",
	["lucide-navigation"] = "rbxassetid://10734906744",
	["lucide-navigation-2"] = "rbxassetid://10734906332",
	["lucide-navigation-2-off"] = "rbxassetid://10734906144",
	["lucide-navigation-off"] = "rbxassetid://10734906580",
	["lucide-network"] = "rbxassetid://10734906975",
	["lucide-newspaper"] = "rbxassetid://10734907168",
	["lucide-octagon"] = "rbxassetid://10734907361",
	["lucide-option"] = "rbxassetid://10734907649",
	["lucide-outdent"] = "rbxassetid://10734907933",
	["lucide-package"] = "rbxassetid://10734909540",
	["lucide-package-2"] = "rbxassetid://10734908151",
	["lucide-package-check"] = "rbxassetid://10734908384",
	["lucide-package-minus"] = "rbxassetid://10734908626",
	["lucide-package-open"] = "rbxassetid://10734908793",
	["lucide-package-plus"] = "rbxassetid://10734909016",
	["lucide-package-search"] = "rbxassetid://10734909196",
	["lucide-package-x"] = "rbxassetid://10734909375",
	["lucide-paint-bucket"] = "rbxassetid://10734909847",
	["lucide-paintbrush"] = "rbxassetid://10734910187",
	["lucide-paintbrush-2"] = "rbxassetid://10734910030",
	["lucide-palette"] = "rbxassetid://10734910430",
	["lucide-palmtree"] = "rbxassetid://10734910680",
	["lucide-panel-left"] = "rbxassetid://10734954301",
	["lucide-paperclip"] = "rbxassetid://10734910927",
	["lucide-party-popper"] = "rbxassetid://10734918735",
	["lucide-pause"] = "rbxassetid://10734919336",
	["lucide-pause-circle"] = "rbxassetid://10735024209",
	["lucide-pause-octagon"] = "rbxassetid://10734919143",
	["lucide-pen-tool"] = "rbxassetid://10734919503",
	["lucide-pencil"] = "rbxassetid://10734919691",
	["lucide-percent"] = "rbxassetid://10734919919",
	["lucide-person-standing"] = "rbxassetid://10734920149",
	["lucide-phone"] = "rbxassetid://10734921524",
	["lucide-phone-call"] = "rbxassetid://10734920305",
	["lucide-phone-forwarded"] = "rbxassetid://10734920508",
	["lucide-phone-incoming"] = "rbxassetid://10734920694",
	["lucide-phone-missed"] = "rbxassetid://10734920845",
	["lucide-phone-off"] = "rbxassetid://10734921077",
	["lucide-phone-outgoing"] = "rbxassetid://10734921288",
	["lucide-pie-chart"] = "rbxassetid://10734921727",
	["lucide-piggy-bank"] = "rbxassetid://10734921935",
	["lucide-pin"] = "rbxassetid://10734922324",
	["lucide-pin-off"] = "rbxassetid://10734922180",
	["lucide-pipette"] = "rbxassetid://10734922497",
	["lucide-pizza"] = "rbxassetid://10734922774",
	["lucide-plane"] = "rbxassetid://10734922971",
	["lucide-play"] = "rbxassetid://10734923549",
	["lucide-play-circle"] = "rbxassetid://10734923214",
	["lucide-plus"] = "rbxassetid://10734924532",
	["lucide-plus-circle"] = "rbxassetid://10734923868",
	["lucide-plus-square"] = "rbxassetid://10734924219",
	["lucide-podcast"] = "rbxassetid://10734929553",
	["lucide-pointer"] = "rbxassetid://10734929723",
	["lucide-pound-sterling"] = "rbxassetid://10734929981",
	["lucide-power"] = "rbxassetid://10734930466",
	["lucide-power-off"] = "rbxassetid://10734930257",
	["lucide-printer"] = "rbxassetid://10734930632",
	["lucide-puzzle"] = "rbxassetid://10734930886",
	["lucide-quote"] = "rbxassetid://10734931234",
	["lucide-radio"] = "rbxassetid://10734931596",
	["lucide-radio-receiver"] = "rbxassetid://10734931402",
	["lucide-rectangle-horizontal"] = "rbxassetid://10734931777",
	["lucide-rectangle-vertical"] = "rbxassetid://10734932081",
	["lucide-recycle"] = "rbxassetid://10734932295",
	["lucide-redo"] = "rbxassetid://10734932822",
	["lucide-redo-2"] = "rbxassetid://10734932586",
	["lucide-refresh-ccw"] = "rbxassetid://10734933056",
	["lucide-refresh-cw"] = "rbxassetid://10734933222",
	["lucide-refrigerator"] = "rbxassetid://10734933465",
	["lucide-regex"] = "rbxassetid://10734933655",
	["lucide-repeat"] = "rbxassetid://10734933966",
	["lucide-repeat-1"] = "rbxassetid://10734933826",
	["lucide-reply"] = "rbxassetid://10734934252",
	["lucide-reply-all"] = "rbxassetid://10734934132",
	["lucide-rewind"] = "rbxassetid://10734934347",
	["lucide-rocket"] = "rbxassetid://10734934585",
	["lucide-rocking-chair"] = "rbxassetid://10734939942",
	["lucide-rotate-3d"] = "rbxassetid://10734940107",
	["lucide-rotate-ccw"] = "rbxassetid://10734940376",
	["lucide-rotate-cw"] = "rbxassetid://10734940654",
	["lucide-rss"] = "rbxassetid://10734940825",
	["lucide-ruler"] = "rbxassetid://10734941018",
	["lucide-russian-ruble"] = "rbxassetid://10734941199",
	["lucide-sailboat"] = "rbxassetid://10734941354",
	["lucide-save"] = "rbxassetid://10734941499",
	["lucide-scale"] = "rbxassetid://10734941912",
	["lucide-scale-3d"] = "rbxassetid://10734941739",
	["lucide-scaling"] = "rbxassetid://10734942072",
	["lucide-scan"] = "rbxassetid://10723377537",
	["lucide-scan-face"] = "rbxassetid://10734942198",
	["lucide-scan-line"] = "rbxassetid://10734942351",
	["lucide-scissors"] = "rbxassetid://10734942778",
	["lucide-screen-share"] = "rbxassetid://10734943193",
	["lucide-screen-share-off"] = "rbxassetid://10734942967",
	["lucide-scroll"] = "rbxassetid://10734943448",
	["lucide-search"] = "rbxassetid://10734943674",
	["lucide-send"] = "rbxassetid://10734943902",
	["lucide-separator-horizontal"] = "rbxassetid://10734944115",
	["lucide-separator-vertical"] = "rbxassetid://10734944326",
	["lucide-server"] = "rbxassetid://10734949856",
	["lucide-server-cog"] = "rbxassetid://10734944444",
	["lucide-server-crash"] = "rbxassetid://10734944554",
	["lucide-server-off"] = "rbxassetid://10734944668",
	["lucide-settings"] = "rbxassetid://10734950309",
	["lucide-settings-2"] = "rbxassetid://10734950020",
	["lucide-share"] = "rbxassetid://10734950813",
	["lucide-share-2"] = "rbxassetid://10734950553",
	["lucide-sheet"] = "rbxassetid://10734951038",
	["lucide-shield"] = "rbxassetid://10734951847",
	["lucide-shield-alert"] = "rbxassetid://10734951173",
	["lucide-shield-check"] = "rbxassetid://10734951367",
	["lucide-shield-close"] = "rbxassetid://10734951535",
	["lucide-shield-off"] = "rbxassetid://10734951684",
	["lucide-shirt"] = "rbxassetid://10734952036",
	["lucide-shopping-bag"] = "rbxassetid://10734952273",
	["lucide-shopping-cart"] = "rbxassetid://10734952479",
	["lucide-shovel"] = "rbxassetid://10734952773",
	["lucide-shower-head"] = "rbxassetid://10734952942",
	["lucide-shrink"] = "rbxassetid://10734953073",
	["lucide-shrub"] = "rbxassetid://10734953241",
	["lucide-shuffle"] = "rbxassetid://10734953451",
	["lucide-sidebar"] = "rbxassetid://10734954301",
	["lucide-sidebar-close"] = "rbxassetid://10734953715",
	["lucide-sidebar-open"] = "rbxassetid://10734954000",
	["lucide-sigma"] = "rbxassetid://10734954538",
	["lucide-signal"] = "rbxassetid://10734961133",
	["lucide-signal-high"] = "rbxassetid://10734954807",
	["lucide-signal-low"] = "rbxassetid://10734955080",
	["lucide-signal-medium"] = "rbxassetid://10734955336",
	["lucide-signal-zero"] = "rbxassetid://10734960878",
	["lucide-siren"] = "rbxassetid://10734961284",
	["lucide-skip-back"] = "rbxassetid://10734961526",
	["lucide-skip-forward"] = "rbxassetid://10734961809",
	["lucide-skull"] = "rbxassetid://10734962068",
	["lucide-slack"] = "rbxassetid://10734962339",
	["lucide-slash"] = "rbxassetid://10734962600",
	["lucide-slice"] = "rbxassetid://10734963024",
	["lucide-sliders"] = "rbxassetid://10734963400",
	["lucide-sliders-horizontal"] = "rbxassetid://10734963191",
	["lucide-smartphone"] = "rbxassetid://10734963940",
	["lucide-smartphone-charging"] = "rbxassetid://10734963671",
	["lucide-smile"] = "rbxassetid://10734964441",
	["lucide-smile-plus"] = "rbxassetid://10734964188",
	["lucide-snowflake"] = "rbxassetid://10734964600",
	["lucide-sofa"] = "rbxassetid://10734964852",
	["lucide-sort-asc"] = "rbxassetid://10734965115",
	["lucide-sort-desc"] = "rbxassetid://10734965287",
	["lucide-sparkles"] = "rbxassetid://10734966248",
	["lucide-speaker"] = "rbxassetid://10734965419",
	["lucide-sprout"] = "rbxassetid://10734965572",
	["lucide-square"] = "rbxassetid://10734965702",
	["lucide-star"] = "rbxassetid://10734966248",
	["lucide-star-half"] = "rbxassetid://10734965897",
	["lucide-star-off"] = "rbxassetid://10734966097",
	["lucide-stethoscope"] = "rbxassetid://10734966384",
	["lucide-sticker"] = "rbxassetid://10734972234",
	["lucide-sticky-note"] = "rbxassetid://10734972463",
	["lucide-stop-circle"] = "rbxassetid://10734972621",
	["lucide-stretch-horizontal"] = "rbxassetid://10734972862",
	["lucide-stretch-vertical"] = "rbxassetid://10734973130",
	["lucide-strikethrough"] = "rbxassetid://10734973290",
	["lucide-subscript"] = "rbxassetid://10734973457",
	["lucide-sun"] = "rbxassetid://10734974297",
	["lucide-sun-dim"] = "rbxassetid://10734973645",
	["lucide-sun-medium"] = "rbxassetid://10734973778",
	["lucide-sun-moon"] = "rbxassetid://10734973999",
	["lucide-sun-snow"] = "rbxassetid://10734974130",
	["lucide-sunrise"] = "rbxassetid://10734974522",
	["lucide-sunset"] = "rbxassetid://10734974689",
	["lucide-superscript"] = "rbxassetid://10734974850",
	["lucide-swiss-franc"] = "rbxassetid://10734975024",
	["lucide-switch-camera"] = "rbxassetid://10734975214",
	["lucide-sword"] = "rbxassetid://10734975486",
	["lucide-swords"] = "rbxassetid://10734975692",
	["lucide-syringe"] = "rbxassetid://10734975932",
	["lucide-table"] = "rbxassetid://10734976230",
	["lucide-table-2"] = "rbxassetid://10734976097",
	["lucide-tablet"] = "rbxassetid://10734976394",
	["lucide-tag"] = "rbxassetid://10734976528",
	["lucide-tags"] = "rbxassetid://10734976739",
	["lucide-target"] = "rbxassetid://10734977012",
	["lucide-tent"] = "rbxassetid://10734981750",
	["lucide-terminal"] = "rbxassetid://10734982144",
	["lucide-terminal-square"] = "rbxassetid://10734981995",
	["lucide-text-cursor"] = "rbxassetid://10734982395",
	["lucide-text-cursor-input"] = "rbxassetid://10734982297",
	["lucide-thermometer"] = "rbxassetid://10734983134",
	["lucide-thermometer-snowflake"] = "rbxassetid://10734982571",
	["lucide-thermometer-sun"] = "rbxassetid://10734982771",
	["lucide-thumbs-down"] = "rbxassetid://10734983359",
	["lucide-thumbs-up"] = "rbxassetid://10734983629",
	["lucide-ticket"] = "rbxassetid://10734983868",
	["lucide-timer"] = "rbxassetid://10734984606",
	["lucide-timer-off"] = "rbxassetid://10734984138",
	["lucide-timer-reset"] = "rbxassetid://10734984355",
	["lucide-toggle-left"] = "rbxassetid://10734984834",
	["lucide-toggle-right"] = "rbxassetid://10734985040",
	["lucide-tornado"] = "rbxassetid://10734985247",
	["lucide-toy-brick"] = "rbxassetid://10747361919",
	["lucide-train"] = "rbxassetid://10747362105",
	["lucide-trash"] = "rbxassetid://10747362393",
	["lucide-trash-2"] = "rbxassetid://10747362241",
	["lucide-tree-deciduous"] = "rbxassetid://10747362534",
	["lucide-tree-pine"] = "rbxassetid://10747362748",
	["lucide-trees"] = "rbxassetid://10747363016",
	["lucide-trending-down"] = "rbxassetid://10747363205",
	["lucide-trending-up"] = "rbxassetid://10747363465",
	["lucide-triangle"] = "rbxassetid://10747363621",
	["lucide-trophy"] = "rbxassetid://10747363809",
	["lucide-truck"] = "rbxassetid://10747364031",
	["lucide-tv"] = "rbxassetid://10747364593",
	["lucide-tv-2"] = "rbxassetid://10747364302",
	["lucide-type"] = "rbxassetid://10747364761",
	["lucide-umbrella"] = "rbxassetid://10747364971",
	["lucide-underline"] = "rbxassetid://10747365191",
	["lucide-undo"] = "rbxassetid://10747365484",
	["lucide-undo-2"] = "rbxassetid://10747365359",
	["lucide-unlink"] = "rbxassetid://10747365771",
	["lucide-unlink-2"] = "rbxassetid://10747397871",
	["lucide-unlock"] = "rbxassetid://10747366027",
	["lucide-upload"] = "rbxassetid://10747366434",
	["lucide-upload-cloud"] = "rbxassetid://10747366266",
	["lucide-usb"] = "rbxassetid://10747366606",
	["lucide-user"] = "rbxassetid://10747373176",
	["lucide-user-check"] = "rbxassetid://10747371901",
	["lucide-user-cog"] = "rbxassetid://10747372167",
	["lucide-user-minus"] = "rbxassetid://10747372346",
	["lucide-user-plus"] = "rbxassetid://10747372702",
	["lucide-user-x"] = "rbxassetid://10747372992",
	["lucide-users"] = "rbxassetid://10747373426",
	["lucide-utensils"] = "rbxassetid://10747373821",
	["lucide-utensils-crossed"] = "rbxassetid://10747373629",
	["lucide-venetian-mask"] = "rbxassetid://10747374003",
	["lucide-verified"] = "rbxassetid://10747374131",
	["lucide-vibrate"] = "rbxassetid://10747374489",
	["lucide-vibrate-off"] = "rbxassetid://10747374269",
	["lucide-video"] = "rbxassetid://10747374938",
	["lucide-video-off"] = "rbxassetid://10747374721",
	["lucide-view"] = "rbxassetid://10747375132",
	["lucide-voicemail"] = "rbxassetid://10747375281",
	["lucide-volume"] = "rbxassetid://10747376008",
	["lucide-volume-1"] = "rbxassetid://10747375450",
	["lucide-volume-2"] = "rbxassetid://10747375679",
	["lucide-volume-x"] = "rbxassetid://10747375880",
	["lucide-wallet"] = "rbxassetid://10747376205",
	["lucide-wand"] = "rbxassetid://10747376565",
	["lucide-wand-2"] = "rbxassetid://10747376349",
	["lucide-watch"] = "rbxassetid://10747376722",
	["lucide-waves"] = "rbxassetid://10747376931",
	["lucide-webcam"] = "rbxassetid://10747381992",
	["lucide-wifi"] = "rbxassetid://10747382504",
	["lucide-wifi-off"] = "rbxassetid://10747382268",
	["lucide-wind"] = "rbxassetid://10747382750",
	["lucide-wrap-text"] = "rbxassetid://10747383065",
	["lucide-wrench"] = "rbxassetid://10747383470",
	["lucide-x"] = "rbxassetid://10747384394",
	["lucide-x-circle"] = "rbxassetid://10747383819",
	["lucide-x-octagon"] = "rbxassetid://10747384037",
	["lucide-x-square"] = "rbxassetid://10747384217",
	["lucide-zap"] = "rbxassetid://10723345749",
	["lucide-zoom-in"] = "rbxassetid://10747384552",
	["lucide-zoom-out"] = "rbxassetid://10747384679",
}

local IconAssets = BuiltinIcons
CialloHub.Icons = IconAssets

local preloaded = genv.CialloHubPreloaded or {}
genv.CialloHubPreloaded = preloaded

local iconsReady = true
local iconQueue, iconQueued = {}, {}
local iconFlushScheduled, iconFlushing = false, false

local function flushIcons()
	if iconFlushing then return end
	iconFlushing = true
	task.spawn(function()
		while #iconQueue > 0 do
			local batch = iconQueue
			iconQueue = {}
			for i = 1, #batch, 100 do
				local chunk = { table.unpack(batch, i, math.min(i + 99, #batch)) }
				local ok = pcall(function() ContentProvider:PreloadAsync(chunk) end)
				for _, id in ipairs(chunk) do
					if ok then preloaded[id] = true end
					iconQueued[id] = nil
				end
			end
		end
		iconFlushing = false
		iconsReady = true
	end)
end

local function queueIcon(id)
	if type(id) ~= "string" or preloaded[id] or iconQueued[id] then return end
	iconQueued[id] = true
	iconQueue[#iconQueue + 1] = id
	iconsReady = false
	if not iconFlushScheduled then
		iconFlushScheduled = true
		task.defer(function()
			iconFlushScheduled = false
			flushIcons()
		end)
	end
end

local Cache = { dir = "CialloHub/cache", pending = 0, counter = 0 }

function Cache.wipe()
	pcall(function()
		if isfolder and listfiles and delfile and isfolder(Cache.dir) then
			for _, f in ipairs(listfiles(Cache.dir)) do
				pcall(delfile, f)
			end
		end
	end)
end

function Cache.release()
	Cache.pending = math.max(0, Cache.pending - 1)
	if Cache.pending == 0 then Cache.wipe() end
end

function Cache.name()
	Cache.counter = Cache.counter + 1
	local ok, guid = pcall(function() return HttpService:GenerateGUID(false) end)
	if not ok or type(guid) ~= "string" then
		guid = tostring(os.time()) .. tostring(math.random(100000, 999999))
	end
	return Cache.dir .. "/" .. Cache.counter .. "_" .. (guid:gsub("[^%w]", "")) .. ".png"
end

local function isImageData(d)
	return type(d) == "string" and (d:sub(2, 4) == "PNG" or d:sub(1, 2) == "\255\216")
end

local function urlAsset(url)
	local getAsset = getcustomasset or getsynasset
	if not writefile then return nil, "writefile is not supported by your executor" end
	if not getAsset then return nil, "getcustomasset is not supported by your executor" end
	pcall(function()
		if makefolder and isfolder then
			if not isfolder("CialloHub") then makefolder("CialloHub") end
			if not isfolder(Cache.dir) then makefolder(Cache.dir) end
		end
	end)

	local ok, data = pcall(function() return game:HttpGet(url) end)
	if not ok then return nil, "download failed: " .. tostring(data) end
	if type(data) ~= "string" or #data < 8 then return nil, "download returned no data" end
	if not isImageData(data) then
		local head = (data:sub(1, 16):gsub("[^%w%p ]", "."))
		return nil, "the link is not a PNG or JPG file (it starts with: " .. head .. ")"
	end

	Cache.pending = Cache.pending + 1
	local path = Cache.name()
	local wok, werr = pcall(writefile, path, data)
	if not wok then
		Cache.release()
		return nil, "writefile failed: " .. tostring(werr)
	end
	local gok, asset = pcall(getAsset, path)
	if not gok or not asset then
		Cache.release()
		return nil, "getcustomasset failed: " .. tostring(asset)
	end
	return asset
end

local warnedIcons = {}
local function resolveIcon(name)
	if type(name) == "number" then return "rbxassetid://" .. name end
	if type(name) ~= "string" or name == "" then return nil end
	if name:find("^https?://") then
		local a = urlAsset(name)
		if a then task.delay(5, Cache.release) end
		return a
	end
	if name:find("rbxasset") then return name end
	local found = IconAssets["lucide-" .. name] or IconAssets[name]
	if found then
		queueIcon(found)
	else
		if next(IconAssets) == nil then
			if not warnedIcons[""] then
				warnedIcons[""] = true
				print("[Ciallo Hub] ERROR: The icon list could not be loaded, icons will not show.")
			end
		elseif not warnedIcons[name] then
			warnedIcons[name] = true
			print("[Ciallo Hub] ERROR: Unknown icon \"" .. name .. "\"")
		end
	end
	return found
end

function CialloHub:GetIcon(name) return resolveIcon(name) end

function CialloHub:PreloadAllIcons()
	for _, id in pairs(IconAssets) do queueIcon(id) end
end

local function fs(n) return math.floor(n * (CialloHub.TextScale or 1) + 0.5) end

local function New(class, props, kids)
	local o = Instance.new(class)
	local raw = props and props.RawSize
	for k, v in pairs(props or {}) do
		if k == "RawSize" or k == "GlassKey" then
		elseif k == "TextSize" and type(v) == "number" and not raw then
			o[k] = fs(v)
		elseif type(v) == "string" and v:sub(1, 1) == "$" and k:find("Color") then
			local key = v:sub(2)
			o[k] = T[key]
			table.insert(CialloHub._binds, { o, k, key })
		else
			o[k] = v
		end
	end
	for _, c in ipairs(kids or {}) do c.Parent = o end
	local bg = props and props.BackgroundColor3
	if type(bg) == "string" and bg:sub(1, 1) == "$" and o:IsA("GuiObject") and o.BackgroundTransparency < 1 then
		local key = props.GlassKey or bg:sub(2)
		if GLASS_KEYS[key] or key == "Popup" or key == "Hide" then
			local base = o.BackgroundTransparency
			table.insert(CialloHub._glass, { o, base, key })
			if T.Glass then o.BackgroundTransparency = glassAlpha(key, base) end
		end
	end
	return o
end

local function Corner(r) return New("UICorner", { CornerRadius = UDim.new(0, r <= 2 and r or math.floor(r * 1.5 + 0.5)) }) end
local function Stroke(key, t)
	return New("UIStroke", { Color = "$" .. (key or "Border"), Transparency = t or 0, ApplyStrokeMode = Enum.ApplyStrokeMode.Border })
end

local function GlassLayer(parent, key, patches)
	local group = New("CanvasGroup", {
		Name = "GlassLayer", Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0,
		ZIndex = 0, Visible = T.Glass ~= nil, GroupTransparency = T.Glass and T.Glass[key] or 1, Parent = parent,
	})
	New("Frame", {
		Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$" .. key, GlassKey = "Solid", BorderSizePixel = 0, Parent = group,
	}, { Corner(8) })
	for _, p in ipairs(patches) do
		p.BackgroundColor3, p.GlassKey, p.BorderSizePixel, p.Parent = "$" .. key, "Solid", 0, group
		New("Frame", p)
	end
	table.insert(CialloHub._glassLayers, { group, key })
	return group
end

local function List(pad)
	return New("UIListLayout", { Padding = UDim.new(0, pad or 0), SortOrder = Enum.SortOrder.LayoutOrder })
end
local function Pad(l, t, r, b)
	return New("UIPadding", { PaddingLeft = UDim.new(0, l), PaddingTop = UDim.new(0, t), PaddingRight = UDim.new(0, r), PaddingBottom = UDim.new(0, b) })
end

local function Tween(o, props, t)
	local tw = TS:Create(o, TweenInfo.new(t or 0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), props)
	tw:Play()
	return tw
end

local function Text(parent, text, props)
	local p = {
		BackgroundTransparency = 1, Text = text, Font = T.Font, TextSize = 13,
		TextColor3 = "$Text", TextXAlignment = Enum.TextXAlignment.Left, Parent = parent,
	}
	for k, v in pairs(props or {}) do p[k] = v end
	return New("TextLabel", p)
end

local function Icon(parent, name, size, colorKey, props)
	local img = resolveIcon(name)
	local p = {
		BackgroundTransparency = 1, Size = UDim2.fromOffset(size, size), Image = img or "",
		ImageColor3 = "$" .. (colorKey or "Text"), Visible = img ~= nil, Parent = parent,
	}
	for k, v in pairs(props or {}) do p[k] = v end
	return New("ImageLabel", p)
end

local BadgeColors = {
	BETA = Color3.fromRGB(255, 184, 76),
	HOT = Color3.fromRGB(255, 92, 92),
	FIXED = Color3.fromRGB(96, 214, 140),
}

local function textWidth(s, size, font)
	local ok, v = pcall(function()
		return TextService:GetTextSize(tostring(s), size, font, Vector2.new(1000, 100)).X
	end)
	return ok and v or (#tostring(s) * size * 0.56)
end

local function Badge(parent, spec, solid)
	if not spec or spec == "" then return nil end
	local text, col = spec, nil
	if type(spec) == "table" then text, col = spec.Text or spec.Title or "", spec.Color end
	text = TC(string.lower(tostring(text)))
	if text == "" then return nil end
	col = col or BadgeColors[text:upper()]
	local w = math.ceil(textWidth(text, fs(9), T.FontBold)) + 12
	w = w + w % 2
	local frame = New("Frame", {
		AnchorPoint = Vector2.new(0, 8 / 15), Size = UDim2.fromOffset(w, 15),
		BackgroundColor3 = solid and Color3.new(0, 0, 0) or (col or "$Accent"),
		BackgroundTransparency = solid and 0.35 or 0.86, Parent = parent,
	}, {
		Corner(4),
		New("UIStroke", { Color = col or "$Accent", Transparency = 0.55, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
	})
	Text(frame, text, {
		Position = UDim2.fromOffset(0, -1), Size = UDim2.fromScale(1, 1), Font = T.FontBold, TextSize = 9, TextColor3 = col or "$Accent",
		TextXAlignment = Enum.TextXAlignment.Center, TextYAlignment = Enum.TextYAlignment.Center,
	})
	return frame, w
end

local function attachBadge(label, badge, x0, cy)
	if not badge then return end
	local function place()
		local tw = textWidth(label.Text, label.TextSize, label.Font)
		badge.Position = UDim2.fromOffset(x0 + math.min(tw, label.AbsoluteSize.X) + 6, cy)
	end
	label:GetPropertyChangedSignal("AbsoluteSize"):Connect(place)
	place()
end

local function imageSrc(v)
	if type(v) == "number" then return "rbxassetid://" .. v end
	if type(v) ~= "string" or v == "" then return "" end
	if v:match("^%d+$") then return "rbxassetid://" .. v end
	return v
end

local resolvedDecalCache = {}
local function loadIcon(icon, id)
	if id == nil or id == "" then
		icon.Image = ""
		return false
	end
	if type(id) == "string" and id:match("^https?://") then
		local a, why = urlAsset(id)
		if not a then
			warn("[Ciallo Hub] 图片链接加载失败 (" .. id .. "): " .. tostring(why))
			icon.Image = ""
			return false
		end
		icon.Image = a
		local done = false
		task.spawn(function()
			pcall(function() ContentProvider:PreloadAsync({ icon }) end)
			done = true
		end)
		local waited = 0
		while not done and waited < 3 do
			task.wait(0.05)
			waited = waited + 0.05
		end
		Cache.release()
		return true
	end
	if type(id) == "string" and id:match("^rbxthumb://") then
		icon.Image = id
		return true
	end

	local s = tostring(id)
	local num = s:match("^rbxassetid://(%d+)$") or s:match("^(%d+)$") or s:match("id=(%d+)")
	if num then
		if resolvedDecalCache[num] then
			icon.Image = resolvedDecalCache[num]
			return true
		end
		-- 优先通过 GetObjects 解析贴花 (Decal) 对应的真实高清 Texture ID
		local fetched, objs = pcall(function() return game:GetObjects("rbxassetid://" .. num) end)
		if fetched and type(objs) == "table" then
			for _, ob in ipairs(objs) do
				local tex
				if ob:IsA("Decal") or ob:IsA("Texture") then tex = ob.Texture end
				if ob:IsA("ImageLabel") or ob:IsA("ImageButton") then tex = ob.Image end
				pcall(function() ob:Destroy() end)
				if type(tex) == "string" and tex ~= "" then
					resolvedDecalCache[num] = tex
					icon.Image = tex
					return true
				end
			end
		end
		-- 兼容 Decal ID 与 Image ID 的官方缩略图通道 (rbxthumb://type=Asset)
		local thumb = "rbxthumb://type=Asset&id=" .. num .. "&w=420&h=420"
		resolvedDecalCache[num] = thumb
		icon.Image = thumb
		return true
	end

	local found = resolveIcon(s)
	if found then
		icon.Image = found
		return true
	end

	icon.Image = s
	return true
end

local function connect(signal, fn)
	local c = signal:Connect(fn)
	table.insert(CialloHub._conns, c)
	return c
end

local function isPress(i) return i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch end
local function isMove(i) return i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch end

local function cutText(s, max)
	s = tostring(s or "")
	max = max or CialloHub.TipLength or 16
	local len = utf8.len(s)
	if not len or len <= max then return s, false end
	return s:sub(1, utf8.offset(s, max + 1) - 1) .. "...", true
end

local function attachTip(label, full, noCut, max)
	local text = tostring(full or "")
	label.Text = text
	if label.TextTruncate == Enum.TextTruncate.None then
		label.TextTruncate = Enum.TextTruncate.AtEnd
	end

	table.insert(CialloHub._tips, { label = label, text = text, cut = false })
end

local function inside(o, p)
	local a, s = o.AbsolutePosition, o.AbsoluteSize
	return p.X >= a.X and p.X <= a.X + s.X and p.Y >= a.Y and p.Y <= a.Y + s.Y
end

local function hoverable(label, p, ignoreCovered)
	if not inside(label, p) then return false end
	local o = label.Parent
	while o do
		if o:IsA("GuiObject") then
			if not o.Visible or (not ignoreCovered and o:GetAttribute("Covered")) then return false end
			if (o.ClipsDescendants or o:IsA("ScrollingFrame")) and not inside(o, p) then return false end
		elseif o:IsA("ScreenGui") and not o.Enabled then
			return false
		end
		o = o.Parent
	end
	return true
end

local function setupTooltip(gui)
	local tip = New("Frame", {
		Name = "Tooltip", Visible = false, ZIndex = 1000, AutomaticSize = Enum.AutomaticSize.XY,
		Size = UDim2.fromOffset(0, 0), BackgroundColor3 = "$Element", GlassKey = "Popup", BorderSizePixel = 0, Parent = gui,
	}, { Corner(5), Stroke("Border", 0.1), Pad(8, 4, 8, 4) })
	local txt = Text(tip, "", {
		ZIndex = 1001, AutomaticSize = Enum.AutomaticSize.XY, Size = UDim2.fromOffset(0, 0),
		TextSize = 12, TextWrapped = false,
	})

	local function place(pos)
		local vp = gui.AbsoluteSize
		local s = tip.AbsoluteSize
		local x = math.floor(math.min(pos.X + 14, vp.X - s.X - 4) + 0.5)
		local y = math.floor(pos.Y + 18 + 0.5)
		if y + s.Y > vp.Y - 4 then y = math.floor(pos.Y - s.Y - 8 + 0.5) end
		tip.Position = UDim2.fromOffset(math.max(x, 4), math.max(y, 4))
	end

	local lastPos = Vector2.zero
	local function update(pos)
		lastPos = pos
		local found
		for i = #CialloHub._tips, 1, -1 do
			local t = CialloHub._tips[i]
			if not t.label.Parent then
				table.remove(CialloHub._tips, i)
			elseif not found and t.text ~= "" and (not t.needVisible or t.label.Visible) and hoverable(t.label, pos) and (t.cut or not t.label.TextFits) then
				found = t
			end
		end
		if found then
			if txt.Text ~= found.text then txt.Text = found.text end
			tip.Visible = true
			place(pos)
		else
			tip.Visible = false
		end
	end

	connect(UIS.InputChanged, function(i)
		if i.UserInputType == Enum.UserInputType.MouseMovement then
			lastPos = Vector2.new(i.Position.X, i.Position.Y)
			local now = os.clock()
			if now - (CialloHub._tipMove or 0) >= 0.03 then
				CialloHub._tipMove = now
				update(lastPos)
			end
		end
	end)

	connect(RunService.RenderStepped, function()
		if tip.Visible then
			local now = os.clock()
			if now - (CialloHub._tipLast or 0) >= 0.08 then
				CialloHub._tipLast = now
				update(lastPos)
			end
		end
	end)
end

local function safe(fn, ...)
	if fn then
		local ok, err = pcall(fn, ...)
		if not ok then warn("[Ciallo Hub] " .. tostring(err)) end
	end
end

local ERROR_RED = Color3.fromRGB(255, 92, 92)
local function flashError(frame, rest)
	if not frame or not frame.Parent then return end
	frame.BackgroundColor3 = ERROR_RED
	Tween(frame, { BackgroundColor3 = rest() }, 0.5)
end

local function keyName(k)
	if not k then return "无" end
	if k.EnumType == Enum.UserInputType then
		local n = k.Name:match("MouseButton(%d)")
		if n == "2" then return "鼠标右键"
		elseif n == "3" then return "鼠标中键"
		elseif n then return "鼠标键 " .. n end
		return k.Name
	end
	return k.Name
end

local function bindMatch(i, key)
	if not key then return false end
	if key.EnumType == Enum.KeyCode then return i.KeyCode == key end
	return i.UserInputType == key
end

function CialloHub:SetMenuKey(k)
	local keys = { Enum.KeyCode.Insert }
	if typeof(k) == "EnumItem" and k.EnumType == Enum.KeyCode and k ~= Enum.KeyCode.Insert then
		table.insert(keys, k)
	end
	self.ToggleKeys = keys
end

local THEME_TWEEN = TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

local function isShown(inst, cache)
	local c = cache[inst]
	if c ~= nil then return c end
	local r
	if inst == game then
		r = true
	elseif not inst.Parent then
		r = false
	elseif inst:IsA("ScreenGui") then
		r = inst.Enabled
	elseif inst:IsA("GuiObject") and not inst.Visible then
		r = false
	else
		r = isShown(inst.Parent, cache)
	end
	cache[inst] = r
	return r
end

function CialloHub:_ApplyGlass()
	T.Glass = glassOn and GLASS or nil

	local gl, gw = self._glass, 0
	for i = 1, #gl do
		local g = gl[i]
		if g[1]:IsDescendantOf(game) then
			gw = gw + 1
			gl[gw] = g
			local to = glassAlpha(g[3], g[2])
			if g[1].BackgroundTransparency ~= to then
				TS:Create(g[1], THEME_TWEEN, { BackgroundTransparency = to }):Play()
			end
		end
	end
	for i = #gl, gw + 1, -1 do gl[i] = nil end

	local ly, lw = self._glassLayers, 0
	for i = 1, #ly do
		local l = ly[i]
		if l[1]:IsDescendantOf(game) then
			lw = lw + 1
			ly[lw] = l
			l[1].Visible = glassOn
			if glassOn then TS:Create(l[1], THEME_TWEEN, { GroupTransparency = GLASS[l[2]] }):Play() end
		end
	end
	for i = #ly, lw + 1, -1 do ly[i] = nil end

	updateBlur()
	local gt = self.Options.glass
	if gt and gt.Set and gt.Get and gt:Get() ~= glassOn then pcall(gt.Set, gt, glassOn, true) end
	if self._glassPaint then pcall(self._glassPaint) end
end

function CialloHub:SetGlass(v)
	glassOn = v and true or false
	self:_ApplyGlass()
	return glassOn
end

function CialloHub:GetGlass() return glassOn end

local THEME_SWEEP = 0.9
local THEME_SNAP = 0.14
local THEME_STEPS = 120
local WHITE = Color3.new(1, 1, 1)

local function guiAnchor(inst)
	local g = inst
	while g and not g:IsA("GuiBase2d") do g = g.Parent end
	return g
end

local function sweepTime(f)
	f = math.clamp(f, 0, 1)
	return THEME_SWEEP * math.acos(1 - 2 * f) / math.pi
end

local function wipeable(inst, prop, wd)
	if wd < 16 or inst:FindFirstChildOfClass("UIGradient") then return false end
	if prop == "BackgroundColor3" then
		if not inst:IsA("GuiObject") or inst.BackgroundTransparency >= 0.99 then return false end
		if inst:IsA("ImageLabel") or inst:IsA("ImageButton") or inst:IsA("ViewportFrame") or inst:IsA("CanvasGroup") or inst:IsA("ScrollingFrame") or inst:IsA("TextBox") then return false end
		if (inst:IsA("TextLabel") or inst:IsA("TextButton")) and inst.Text ~= "" then return false end
		return true
	end
	return prop == "Color" and inst:IsA("UIStroke")
end

function CialloHub:_CancelWipes()
	local wipes = self._wipes
	for grad, w in pairs(wipes) do
		wipes[grad] = nil
		if grad.Parent then
			grad:Destroy()
			if w[1].Parent then w[1][w[2]] = w[3] end
		end
	end
end

function CialloHub:_RunSweep(gen, list, minX, span)
	if #list == 0 then return end
	local t0 = os.clock()
	local conn
	conn = RunService.Heartbeat:Connect(function()
		if CialloHub._themeGen ~= gen then
			conn:Disconnect()
			return
		end
		local t = os.clock() - t0
		local f = t >= THEME_SWEEP and 1 or (1 - math.cos(math.pi * t / THEME_SWEEP)) / 2
		local bx = minX + span * f
		local pending = 0
		for i = 1, #list do
			local w = list[i]
			if not w.done then
				local inst = w.inst
				if not inst.Parent then
					w.done = true
				elseif bx >= w.x or f >= 1 then
					if not w.grad then
						local from = inst[w.prop]
						local grad = Instance.new("UIGradient")
						grad.Color = ColorSequence.new({
							ColorSequenceKeypoint.new(0, w.to),
							ColorSequenceKeypoint.new(0.499, w.to),
							ColorSequenceKeypoint.new(0.501, from),
							ColorSequenceKeypoint.new(1, from),
						})
						grad.Offset = Vector2.new(-0.5, 0)
						inst[w.prop] = WHITE
						grad.Parent = inst
						w.grad = grad
						CialloHub._wipes[grad] = { inst, w.prop, from }
					end
					local o = math.clamp((bx - w.x) / w.wd, 0, 1)
					if o >= 1 or f >= 1 then
						CialloHub._wipes[w.grad] = nil
						w.grad:Destroy()
						inst[w.prop] = w.to
						w.done = true
					else
						w.grad.Offset = Vector2.new(o - 0.5, 0)
						pending = pending + 1
					end
				else
					pending = pending + 1
				end
			end
		end
		if pending == 0 then conn:Disconnect() end
	end)
end

local function spawnBand(win)
	local main = win._main
	if win._band then win._band:Destroy() win._band = nil end
	local group = New("CanvasGroup", {
		Name = "ThemeBand", Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0,
		GroupTransparency = 1, ZIndex = 500, Parent = main,
	}, { New("UICorner", { CornerRadius = UDim.new(0, 12) }) })
	local glow = New("Frame", {
		AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.fromScale(0, 0), Size = UDim2.new(0, 200, 1, 0),
		BackgroundColor3 = T.Accent, BackgroundTransparency = 0, BorderSizePixel = 0, Parent = group,
	}, {
		New("UIGradient", {
			Transparency = NumberSequence.new({
				NumberSequenceKeypoint.new(0, 1),
				NumberSequenceKeypoint.new(0.3, 0.94),
				NumberSequenceKeypoint.new(0.5, 0.78),
				NumberSequenceKeypoint.new(0.7, 0.97),
				NumberSequenceKeypoint.new(1, 1),
			}),
		}),
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.fromScale(0.5, 0), Size = UDim2.new(0, 2, 1, 0),
		BackgroundColor3 = T.Accent, BackgroundTransparency = 0, BorderSizePixel = 0, Parent = glow,
	}, {
		New("UIGradient", {
			Rotation = 90,
			Transparency = NumberSequence.new({
				NumberSequenceKeypoint.new(0, 1),
				NumberSequenceKeypoint.new(0.5, 0.15),
				NumberSequenceKeypoint.new(1, 1),
			}),
		}),
	})
	win._band = group
	TS:Create(group, TweenInfo.new(0.15, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), { GroupTransparency = 0 }):Play()
	TS:Create(glow, TweenInfo.new(THEME_SWEEP, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), { Position = UDim2.fromScale(1, 0) }):Play()
	task.delay(math.max(0, THEME_SWEEP - 0.1), function()
		if group.Parent then
			TS:Create(group, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.In), { GroupTransparency = 1 }):Play()
		end
	end)
	task.delay(THEME_SWEEP + 0.45, function()
		if win._band == group then win._band = nil end
		if group.Parent then group:Destroy() end
	end)
end

function CialloHub:_AddPaint(fn, inst)
	table.insert(self._paints, { fn, inst })
end

function CialloHub:SetTheme(name, instant)
	local th = type(name) == "string" and Themes[name]
	if not th then return false end
	self.ThemeName = name
	for k, v in pairs(th) do T[k] = v end
	T.OnAccent = th.OnAccent or Color3.new(1, 1, 1)
	self:_ApplyGlass()

	self._themeGen = (self._themeGen or 0) + 1
	local gen = self._themeGen
	self._wipes = self._wipes or {}
	self:_CancelWipes()
	local animate = not instant and self.ThemeSweep ~= false

	local minX, maxX, fixed = math.huge, -math.huge, false
	if animate then
		for _, win in ipairs(self.Windows) do
			local m = win._main
			if win.Visible and m and m.Parent and m.Visible then
				minX, maxX, fixed = m.AbsolutePosition.X, m.AbsolutePosition.X + m.AbsoluteSize.X, true
				break
			end
		end
	end

	local binds, cache, w = self._binds, {}, 0
	local jobs, paintJobs = {}, {}

	local function place(inst)
		local g = guiAnchor(inst)
		if not g then return nil, 0 end
		local x, wd = g.AbsolutePosition.X, g.AbsoluteSize.X
		if not fixed then
			if x < minX then minX = x end
			if x + wd > maxX then maxX = x + wd end
		end
		return x, wd
	end

	for i = 1, #binds do
		local b = binds[i]
		local inst = b[1]
		if inst:IsDescendantOf(game) then
			w = w + 1
			binds[w] = b
			local prop, to = b[2], T[b[3]]
			if inst[prop] ~= to then
				if animate and isShown(inst, cache) then
					local x, wd = place(inst)
					jobs[#jobs + 1] = { inst, prop, to, x, wd }
				elseif isShown(inst, cache) then
					TS:Create(inst, THEME_TWEEN, { [prop] = to }):Play()
				else
					inst[prop] = to
				end
			end
		end
	end
	for i = #binds, w + 1, -1 do binds[i] = nil end

	local paints, pw = self._paints, 0
	for i = 1, #paints do
		local p = paints[i]
		if not p[2] or p[2]:IsDescendantOf(game) then
			pw = pw + 1
			paints[pw] = p
			if animate and p[2] and isShown(p[2], cache) then
				local x, wd = place(p[2])
				paintJobs[#paintJobs + 1] = { p[1], (x or 0) + wd * 0.5 }
			else
				pcall(p[1])
			end
		end
	end
	for i = #paints, pw + 1, -1 do paints[i] = nil end

	if animate and (#jobs > 0 or #paintJobs > 0) then
		local span = maxX - minX
		if span < 1 then span = 1 end
		local buckets, order = {}, {}

		local function frac(x)
			if not x or x ~= x then return 0 end
			return math.clamp((x - minX) / span, 0, 1)
		end

		local function bucket(f)
			local step = math.floor(f * THEME_STEPS + 0.5)
			local bk = buckets[step]
			if not bk then
				bk = { jobs = {}, paints = {} }
				buckets[step] = bk
				order[#order + 1] = step
			end
			return bk
		end

		local wipeList = {}
		for i = 1, #jobs do
			local j = jobs[i]
			local x, wd = j[4], j[5]
			local wiped = false
			if x and wipeable(j[1], j[2], wd) and sweepTime(frac(x + wd)) - sweepTime(frac(x)) >= 0.06 then
				wiped = true
				wipeList[#wipeList + 1] = { inst = j[1], prop = j[2], to = j[3], x = x, wd = wd }
			end
			if not wiped then
				local bk = bucket(frac((x or minX) + wd * 0.5))
				bk.jobs[#bk.jobs + 1] = j
			end
		end
		for i = 1, #paintJobs do
			local bk = bucket(frac(paintJobs[i][2]))
			bk.paints[#bk.paints + 1] = paintJobs[i][1]
		end

		local snap = TweenInfo.new(THEME_SNAP, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
		for _, step in ipairs(order) do
			local bk = buckets[step]
			local function run()
				if CialloHub._themeGen ~= gen then return end
				for _, j in ipairs(bk.jobs) do
					if j[1].Parent then
						TS:Create(j[1], snap, { [j[2]] = j[3] }):Play()
					end
				end
				for _, fn in ipairs(bk.paints) do pcall(fn) end
			end
			if step <= 0 then run() else task.delay(sweepTime(step / THEME_STEPS), run) end
		end

		self:_RunSweep(gen, wipeList, minX, span)

		for _, win in ipairs(self.Windows) do
			if win.Visible and win._main and win._main.Parent and win._main.Visible then
				pcall(spawnBand, win)
			end
		end
	end

	local dd = self.Options.theme
	if dd and dd.Set then pcall(dd.Set, dd, name, true) end
	return true
end

do
	local KEYS = { "Window", "Side", "Panel", "Card", "Element", "Hover", "Accent", "Text", "Sub", "Off" }
	CialloHub.CustomOrder = {}

	function CialloHub:AddTheme(name, def)
		if type(name) ~= "string" or name == "" or type(def) ~= "table" then return false end
		local order = self.CustomOrder
		local known = table.find(order, name) ~= nil
		if Themes[name] and not known then
			warn("[Ciallo Hub] \"" .. name .. "\" is a built-in theme, pick another name.")
			return false
		end
		if not known and #order >= 7 then
			warn("[Ciallo Hub] You can add a maximum of 7 custom themes. \"" .. name .. "\" was skipped.")
			return false
		end
		local th = {}
		for _, k in ipairs(KEYS) do
			local v = def[k]
			th[k] = typeof(v) == "Color3" and v or Themes.Dark[k]
		end
		if typeof(def.OnAccent) == "Color3" then th.OnAccent = def.OnAccent end
		if typeof(def.Border) == "Color3" then
			th.Border = def.Border
		else
			local lum = 0.299 * th.Card.R + 0.587 * th.Card.G + 0.114 * th.Card.B
			th.Border = th.Card:Lerp(th.Text, lum > 0.5 and 0.3 or 0.38)
		end
		Themes[name] = th
		if not known then order[#order + 1] = name end
		if not table.find(self.ThemeOrder, name) then table.insert(self.ThemeOrder, name) end
		if self.ThemeName == name or self._wantTheme == name then self:SetTheme(name, true) end
		return true
	end

	function CialloHub:AddThemes(map)
		if type(map) ~= "table" then return end
		local names = {}
		for n in pairs(map) do names[#names + 1] = tostring(n) end
		table.sort(names)
		for _, n in ipairs(names) do self:AddTheme(n, map[n]) end
	end

	function CialloHub:_CustomPanel(ov, closeFn, items)
		local order = self.CustomOrder
		if #order == 0 then return end
		local panel = New("Frame", {
			Name = "CustomPopup", AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -372, 0, 58), Size = UDim2.fromOffset(200, 0),
			AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", GlassKey = "Popup", ZIndex = 31, Parent = ov,
		}, { Corner(8), Stroke("Border", 0), Pad(6, 6, 6, 6), List(2) })
		local sc = New("UIScale", { Scale = 0.9, Parent = panel })
		Tween(sc, { Scale = 1 }, 0.14)
		local title = Text(panel, "自定义主题", { Size = UDim2.new(1, 0, 0, 24), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub", ZIndex = 32 })
		Pad(8, 0, 0, 0).Parent = title
		for i, name in ipairs(order) do
			local th = Themes[name]
			if th then
				local active = CialloHub.ThemeName == name
				local item = New("TextButton", {
					Size = UDim2.new(1, 0, 0, 34), BackgroundColor3 = "$Hover", BackgroundTransparency = active and 0.35 or 1,
					AutoButtonColor = false, Text = "", ZIndex = 32, LayoutOrder = i, Parent = panel,
				}, { Corner(6) })
				local chips = New("Frame", {
					AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 10, 0.5, 0), Size = UDim2.fromOffset(36, 16),
					BackgroundTransparency = 1, ZIndex = 33, Parent = item,
				}, {
					New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 2), SortOrder = Enum.SortOrder.LayoutOrder }),
				})
				for _, c in ipairs({ th.Window, th.Hover, th.Accent }) do
					New("Frame", {
						Size = UDim2.fromOffset(10, 16), BackgroundColor3 = c, BorderSizePixel = 0, ZIndex = 33, Parent = chips,
					}, { Corner(3), New("UIStroke", { Color = th.Border, Thickness = 1 }) })
				end
				Text(item, ThemeLabels[name] or name, { Position = UDim2.fromOffset(58, 0), Size = UDim2.new(1, -80, 1, 0), ZIndex = 33 })
				local check = Icon(item, "check", 14, "Accent", {
					AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0), ZIndex = 33,
					ImageTransparency = active and 0 or 1,
				})
				if items then items[name] = { btn = item, check = check } end
				item.MouseEnter:Connect(function() if CialloHub.ThemeName ~= name then Tween(item, { BackgroundTransparency = 0.6 }) end end)
				item.MouseLeave:Connect(function() if CialloHub.ThemeName ~= name then Tween(item, { BackgroundTransparency = 1 }) end end)
				item.MouseButton1Click:Connect(function()
					CialloHub:SetTheme(name)
					if closeFn then closeFn() end
				end)
			end
		end
	end
end

local Notify
do
	local notifyGui, notifyHolder
	local notifyOrder = 0
	local TypeColors = {
		Info = function() return T.Accent end,
		Success = function() return Color3.fromRGB(96, 214, 140) end,
		Warning = function() return Color3.fromRGB(255, 184, 76) end,
		Error = function() return Color3.fromRGB(255, 92, 92) end,
	}
	local TypeIcons = {
		Info = "info",
		Success = "check-circle",
		Warning = "alert-triangle",
		Error = "x-circle",
	}

	local function getNotifyHolder()
		if notifyGui and notifyGui.Parent and notifyHolder and notifyHolder.Parent then
			return notifyHolder
		end
		local parent = (gethui and gethui()) or (pcall(function() return CoreGui.Name end) and CoreGui) or Players.LocalPlayer:WaitForChild("PlayerGui")
		notifyGui = New("ScreenGui", {
			Name = "CialloHubNotifications", ResetOnSpawn = false, DisplayOrder = 2147483647,
			ZIndexBehavior = Enum.ZIndexBehavior.Sibling, IgnoreGuiInset = true,
		})
		pcall(function() notifyGui.OnTopOfCoreBlur = true end)
		if syn and syn.protect_gui then pcall(syn.protect_gui, notifyGui) end
		notifyGui.Parent = parent
		notifyHolder = New("Frame", {
			Name = "Holder", AnchorPoint = Vector2.new(1, 1), Position = UDim2.new(1, -18, 1, -18),
			Size = UDim2.new(0, 310, 1, -36), BackgroundTransparency = 1, Parent = notifyGui,
		}, {
			New("UIListLayout", {
				FillDirection = Enum.FillDirection.Vertical,
				VerticalAlignment = Enum.VerticalAlignment.Bottom,
				HorizontalAlignment = Enum.HorizontalAlignment.Right,
				Padding = UDim.new(0, 8),
				SortOrder = Enum.SortOrder.LayoutOrder,
			}),
		})
		return notifyHolder
	end

	Notify = {}
	function Notify:Send(o)
		if type(o) ~= "table" then o = { Title = tostring(o or "提示") } end
		local holder = getNotifyHolder()
		notifyOrder = notifyOrder + 1
		local kind = tostring(o.Type or "Info")
		local colFn = TypeColors[kind] or TypeColors.Info
		local accentCol = colFn()
		local dur = tonumber(o.Duration) or 4
		local titleText = TC(o.Title or kind)
		local bodyText = tostring(o.Content or o.Text or "")
		if o.SubContent and o.SubContent ~= "" then
			bodyText = (bodyText ~= "" and (bodyText .. "\n") or "") .. tostring(o.SubContent)
		end

		local card = New("Frame", {
			Name = "Toast", Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y,
			BackgroundColor3 = "$Card", GlassKey = "Popup", ClipsDescendants = true,
			LayoutOrder = notifyOrder, Parent = holder,
		}, { Corner(8), Stroke("Border", 0.1) })
		local sc = New("UIScale", { Scale = 0.92, Parent = card })
		Tween(sc, { Scale = 1 }, 0.16)

		New("Frame", {
			Size = UDim2.new(0, 3, 1, 0), BackgroundColor3 = accentCol, BorderSizePixel = 0, ZIndex = 2, Parent = card,
		}, { Corner(2) })

		local inner = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y,
			BackgroundTransparency = 1, Parent = card,
		}, { Pad(14, 10, 12, 12), List(6) })

		local headRow = New("Frame", {
			Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, LayoutOrder = 1, Parent = inner,
		})
		local ic = Icon(headRow, o.Icon or TypeIcons[kind] or "bell", 15, "Text", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0),
		})
		if ic.Visible then ic.ImageColor3 = accentCol end
		local tx = ic.Visible and 22 or 0
		Text(headRow, titleText, {
			Position = UDim2.fromOffset(tx, 0), Size = UDim2.new(1, -(tx + 22), 1, 0),
			Font = T.FontBold, TextSize = 13, TextTruncate = Enum.TextTruncate.AtEnd,
		})

		local closed = false
		local function dismiss()
			if closed then return end
			closed = true
			Tween(sc, { Scale = 0.92 }, 0.14)
			Tween(card, { BackgroundTransparency = 1 }, 0.14)
			task.delay(0.15, function()
				if card and card.Parent then card:Destroy() end
			end)
		end

		local closeBtn = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0), Size = UDim2.fromOffset(18, 18),
			BackgroundTransparency = 1, Text = "×", Font = T.FontBold, TextSize = 14, TextColor3 = "$Sub",
			AutoButtonColor = false, Parent = headRow,
		})
		closeBtn.MouseButton1Click:Connect(dismiss)

		if bodyText ~= "" then
			Text(inner, bodyText, {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y,
				TextWrapped = true, TextColor3 = "$Sub", TextSize = 12,
				TextYAlignment = Enum.TextYAlignment.Top, LayoutOrder = 2,
			})
		end

		if type(o.Buttons) == "table" and #o.Buttons > 0 then
			local btnRow = New("Frame", {
				Size = UDim2.new(1, 0, 0, 26), BackgroundTransparency = 1, LayoutOrder = 3, Parent = inner,
			}, {
				New("UIListLayout", {
					FillDirection = Enum.FillDirection.Horizontal, HorizontalAlignment = Enum.HorizontalAlignment.Right,
					Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder,
				}),
			})
			for idx, b in ipairs(o.Buttons) do
				local bTitle = TC(b.Text or b.Title or "确定")
				local bw = math.max(64, math.ceil(textWidth(bTitle, fs(11), T.FontBold)) + 20)
				local btn = New("TextButton", {
					Size = UDim2.fromOffset(bw, 24),
					BackgroundColor3 = b.Primary and accentCol or "$Element",
					AutoButtonColor = false, Text = bTitle, Font = T.FontBold, TextSize = 11,
					TextColor3 = b.Primary and "$OnAccent" or "$Text", LayoutOrder = idx, Parent = btnRow,
				}, { Corner(4), Stroke("Border", b.Primary and 1 or 0.3) })
				btn.MouseButton1Click:Connect(function()
					dismiss()
					if b.Callback then pcall(b.Callback) end
				end)
			end
		end

		local prog = New("Frame", {
			AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0),
			Size = UDim2.new(1, 0, 0, 2), BackgroundColor3 = accentCol, BorderSizePixel = 0, ZIndex = 3, Parent = card,
		})
		TS:Create(prog, TweenInfo.new(dur, Enum.EasingStyle.Linear), { Size = UDim2.new(0, 0, 0, 2) }):Play()
		task.delay(dur, dismiss)
		return { Dismiss = dismiss, Destroy = dismiss }
	end

	for _, kind in ipairs({ "Info", "Success", "Warning", "Error" }) do
		Notify[kind] = function(self, title, content, duration)
			return self:Send({ Type = kind, Title = title, Content = content, Duration = duration })
		end
	end

	function Notify:Destroy()
		if notifyGui then
			pcall(function() notifyGui:Destroy() end)
			notifyGui, notifyHolder = nil, nil
		end
	end
end

function CialloHub:Notify(o)
	return Notify:Send(o)
end

for _, name in ipairs({ "Info", "Success", "Warning", "Error" }) do
	CialloHub[name] = function(self, title, content, duration)
		return Notify[name](Notify, title, content, duration)
	end
end

local function uiScaleOf(o)
	local s = 1
	while o do
		local u = o:FindFirstChildOfClass("UIScale")
		if u then s = s * u.Scale end
		o = o.Parent
	end
	return s
end

local function ensureScrollHook()
	if CialloHub._scrollHooked then return end
	CialloHub._scrollHooked = true
	connect(UIS.InputChanged, function(i)
		if not isMove(i) then return end
		local d = CialloHub._scrollDrag
		if d then
			d.dragTo(i.Position.Y)
			return
		end
		if i.UserInputType ~= Enum.UserInputType.MouseMovement then return end
		local pos = Vector2.new(i.Position.X, i.Position.Y)
		for idx = #CialloHub._scrollers, 1, -1 do
			local s = CialloHub._scrollers[idx]
			if not s.holder.Parent then
				table.remove(CialloHub._scrollers, idx)
			else
				local over = s.thumb.Visible and hoverable(s.sf, pos, s.ignoreCovered) or false
				if over ~= s.hover then
					s.hover = over
					s.refresh()
				end
			end
		end
	end)
	connect(UIS.InputEnded, function(i)
		local d = CialloHub._scrollDrag
		if d and isPress(i) then
			CialloHub._scrollDrag = nil
			d.drag = false
			d.refresh()
		end
	end)
end

local function Scroller(sf, ignoreCovered)
	sf.ScrollBarThickness = 0
	local z = sf.ZIndex + 5
	local holder = New("Frame", {
		Name = "Scroller", BackgroundTransparency = 1, BorderSizePixel = 0,
		AnchorPoint = sf.AnchorPoint, Position = sf.Position, Size = sf.Size, Visible = sf.Visible,
		ZIndex = z, Parent = sf.Parent,
	})
	local thumb = New("TextButton", {
		AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -3, 0, 0), Size = UDim2.new(0, 4, 0.2, 0),
		BackgroundColor3 = "$Accent", BackgroundTransparency = 1, Text = "", AutoButtonColor = false,
		BorderSizePixel = 0, Visible = false, ZIndex = z, Parent = holder,
	}, { Corner(2) })
	local s = {
		sf = sf, holder = holder, thumb = thumb, hover = false, thumbHover = false, drag = false,
		last = 0, token = 0, cur = 1, ignoreCovered = ignoreCovered,
	}

	local function metrics()
		local k = uiScaleOf(sf)
		return sf.AbsoluteWindowSize.Y / k, sf.AbsoluteCanvasSize.Y / k
	end

	function s.refresh()
		local win, canvas = metrics()
		local can = win > 0 and canvas > win + 1
		thumb.Visible = can
		if not can then return end
		local ratio = math.clamp(win / canvas, 0.08, 1)
		local frac = math.clamp(sf.CanvasPosition.Y / (canvas - win), 0, 1)
		thumb.Size = UDim2.new(0, 4, ratio, 0)
		thumb.Position = UDim2.new(1, -3, frac * (1 - ratio), 0)
		local shown = s.hover or s.drag or s.thumbHover or (os.clock() - s.last) < 0.9
		local target = 1
		if shown then target = (s.drag or s.thumbHover) and 0.1 or 0.45 end
		if s.cur ~= target then
			s.cur = target
			Tween(thumb, { BackgroundTransparency = target }, 0.15)
		end
	end

	function s.dragTo(y)
		local win, canvas = metrics()
		local ratio = math.clamp(win / canvas, 0.08, 1)
		local free = holder.AbsoluteSize.Y * (1 - ratio)
		if free <= 0 or canvas <= win then return end
		local v = s.startPos + (y - s.startY) / free * (canvas - win)
		sf.CanvasPosition = Vector2.new(sf.CanvasPosition.X, math.clamp(v, 0, canvas - win))
	end

	thumb.InputBegan:Connect(function(i)
		if isPress(i) then
			s.drag, s.startY, s.startPos = true, i.Position.Y, sf.CanvasPosition.Y
			CialloHub._scrollDrag = s
			s.refresh()
		end
	end)
	thumb.MouseEnter:Connect(function() s.thumbHover = true s.refresh() end)
	thumb.MouseLeave:Connect(function() s.thumbHover = false s.refresh() end)

	sf:GetPropertyChangedSignal("CanvasPosition"):Connect(function()
		s.last = os.clock()
		s.token = s.token + 1
		local mine = s.token
		s.refresh()
		task.delay(1, function() if s.token == mine then s.refresh() end end)
	end)
	sf:GetPropertyChangedSignal("AbsoluteCanvasSize"):Connect(s.refresh)
	sf:GetPropertyChangedSignal("AbsoluteWindowSize"):Connect(s.refresh)
	for _, prop in ipairs({ "Position", "Size", "AnchorPoint", "Visible" }) do
		sf:GetPropertyChangedSignal(prop):Connect(function() holder[prop] = sf[prop] end)
	end

	table.insert(CialloHub._scrollers, s)
	ensureScrollHook()
	task.defer(s.refresh)
	return s
end

local function Spinner(parent, size, props)
	local ring = New("Frame", {
		AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(size, size), BackgroundTransparency = 1,
		Visible = false, Parent = parent,
	}, { Corner(math.ceil(size / 2)) })
	for k, v in pairs(props or {}) do ring[k] = v end
	local st = New("UIStroke", { Color = "$Accent", Thickness = 2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = ring })
	local grad = New("UIGradient", {
		Transparency = NumberSequence.new({
			NumberSequenceKeypoint.new(0, 0), NumberSequenceKeypoint.new(0.5, 0.4), NumberSequenceKeypoint.new(1, 1),
		}),
		Parent = st,
	})
	local conn
	local function spin(on)
		ring.Visible = on and true or false
		if on and not conn then
			conn = RunService.RenderStepped:Connect(function(dt) grad.Rotation = (grad.Rotation + 420 * dt) % 360 end)
			table.insert(CialloHub._conns, conn)
		elseif not on and conn then
			conn:Disconnect()
			conn = nil
		end
	end
	return ring, spin
end

local function Keycap(parent, props)
	local st = Stroke("Border", 0.2)
	local frame = New("Frame", {
		AnchorPoint = Vector2.new(1, 0.5), Size = UDim2.fromOffset(28, 22), BackgroundColor3 = "$Element", Parent = parent,
	}, { Corner(5), st })
	for k, v in pairs(props or {}) do frame[k] = v end
	local label = Text(frame, "", {
		Size = UDim2.fromScale(1, 1), Font = T.FontBold, TextSize = 11, TextColor3 = "$Sub",
		TextXAlignment = Enum.TextXAlignment.Center,
	})
	local pulse = 0
	local api = { Frame = frame }
	function api.set(text, listening)
		pulse = pulse + 1
		local mine = pulse
		label.Text = text
		local w = math.max(28, math.ceil(textWidth(text, fs(11), T.FontBold)) + 18)
		Tween(frame, { Size = UDim2.fromOffset(w, 22) }, 0.12)
		Tween(st, { Color = listening and T.Accent or T.Border, Transparency = listening and 0 or 0.2 })
		Tween(label, { TextColor3 = listening and T.Accent or T.Sub, TextTransparency = 0 }, 0.1)
		if listening then
			task.spawn(function()
				local dim = true
				while pulse == mine and frame.Parent do
					Tween(label, { TextTransparency = dim and 0.6 or 0 }, 0.5)
					dim = not dim
					task.wait(0.5)
				end
			end)
		end
	end
	return api
end

local function lockable(host, o, obj, dropKey, onChange)
	local locked = false
	local shield = New("TextButton", {
		Name = "Locked", Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Card", BackgroundTransparency = 1,
		Text = "", AutoButtonColor = false, Active = true, Visible = false, ZIndex = 9, Parent = host,
	}, { Corner(5) })

	local reason = { label = shield, text = tostring(o.LockedReason or ""), cut = true, needVisible = true }
	table.insert(CialloHub._tips, reason)
	function obj:SetLockedReason(text) reason.text = text ~= nil and tostring(text) or "" end

	function obj:SetLocked(v, why)
		if why ~= nil then reason.text = tostring(why) end
		locked = v and true or false
		if locked then
			local d = CialloHub._drops[dropKey or host]
			if d then pcall(d, false) end
			shield.Visible = true
			Tween(shield, { BackgroundTransparency = 0.45 }, 0.15)
		else
			shield.Visible = false
			shield.BackgroundTransparency = 1
		end
		if onChange then onChange(locked) end
	end
	function obj:IsLocked() return locked end
	if o.Locked == true then obj:SetLocked(true) end
end

function CialloHub._toggleDrop(frame)
	for f, fn in pairs(CialloHub._drops) do
		if f == frame then fn() else fn(false) end
	end
end

local function makeDrop(f, h, bodyH)
	local open = false
	local function set(force)
		if force == nil then open = not open else open = force end
		Tween(f, { Size = UDim2.new(1, 0, 0, open and (h + bodyH()) or h) }, 0.18)
		return open
	end
	CialloHub._drops[f] = set
	return set
end

local function Switch(props)
	local st = Stroke("Border", 0.4)
	local sw = New("Frame", props, { Corner(10), st })
	local knob = New("Frame", {
		AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 3, 0.5, 0), Size = UDim2.fromOffset(14, 14),
		BackgroundColor3 = Color3.new(1, 1, 1), Parent = sw,
	}, { Corner(7) })
	return function(on)
		Tween(sw, { BackgroundColor3 = on and T.Accent or T.Off })
		Tween(knob, { BackgroundColor3 = on and T.OnAccent or Color3.new(1, 1, 1) })
		Tween(st, { Color = on and T.Accent or T.Border })
		Tween(knob, { Position = on and UDim2.new(1, -17, 0.5, 0) or UDim2.new(0, 3, 0.5, 0) })
	end
end

local function Checkbox(props)
	local st = Stroke("Sub", 0.5)
	local box = New("Frame", props, { Corner(4), st })
	local mark = New("ImageLabel", {
		AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromOffset(14, 14),
		BackgroundTransparency = 1, Image = resolveIcon("check") or "", ImageColor3 = "$OnAccent",
		ImageTransparency = 1, Parent = box,
	})
	return function(on)
		Tween(box, { BackgroundColor3 = on and T.Accent or T.Card })
		Tween(st, { Transparency = on and 1 or 0.5 })
		Tween(mark, { ImageTransparency = on and 0 or 1 })
	end
end

local function Elements(self, getParent)
	local function hgt(o) return (o.Description and o.Description ~= "") and 58 or 40 end

	local function row(h)
		return New("Frame", {
			Size = UDim2.new(1, 0, 0, h), BackgroundTransparency = 1, ClipsDescendants = true, Parent = getParent(),
		})
	end

	local function index(title, desc, frame)
		local ctx = self._ctx
		if ctx and title and title ~= "" then
			table.insert(CialloHub._index, { title = TC(title), desc = desc, frame = frame, ctx = ctx })
		end
	end

	local function head(f, o, rpad, h, static)
		local d = h > 40
		local badge, bw = Badge(f, o.Badge)
		local label = Text(f, TC(o.Title or o.Name or ""), {
			Position = UDim2.fromOffset(0, d and 6 or 0), Size = UDim2.new(1, -(rpad + (bw and (bw + 6) or 0)), 0, d and 28 or 40),
			TextColor3 = static and T.Sub or "$Sub", TextTruncate = Enum.TextTruncate.AtEnd,
		})
		attachTip(label, TC(o.Title or o.Name or ""))
		attachBadge(label, badge, 0, 20)
		index(o.Title or o.Name, o.Description, f)
		if d then
			attachTip(Text(f, o.Description, {
				Position = UDim2.fromOffset(0, 36), Size = UDim2.new(1, -rpad, 0, 14), TextSize = 11,
				TextColor3 = "$Sub", TextTransparency = 0.35, TextTruncate = Enum.TextTruncate.AtEnd,
			}), o.Description, true)
		end
		return label
	end

	local function right(f, w, ht, props)
		local p = { AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -4, 0, 20), Size = UDim2.fromOffset(w, ht), Parent = f }
		for k, v in pairs(props or {}) do p[k] = v end
		return p
	end

	local function register(flag, obj, value)
		if flag then
			CialloHub.Flags[flag] = value
			CialloHub.Options[flag] = obj
		end
	end

	local function inlinePopup(f, width)
		local api = { open = false }
		local menu = {}
		local holder = New("Frame", {
			Name = "InlinePopup", Size = UDim2.fromOffset(width, 0), AutomaticSize = Enum.AutomaticSize.Y,
			BackgroundColor3 = "$Card", GlassKey = "Popup", Visible = false, Active = true, ZIndex = 31, Parent = f,
		}, { Corner(6), Stroke("Border", 0), Pad(8, 8, 8, 8), List(6) })
		local pscale = New("UIScale", { Scale = 1, Parent = holder })
		menu._inline = true
		Elements(menu, function() return holder end)
		api.Menu, api.Holder = menu, holder

		local conns, catcher = {}, nil
		local function close()
			if not api.open then return end
			api.open = false
			for _, c in ipairs(conns) do c:Disconnect() end
			conns = {}
			CialloHub._toggleDrop(nil)
			if catcher then catcher:Destroy() catcher = nil end
			Tween(pscale, { Scale = 0.96 }, 0.1)
			task.delay(0.1, function()
				if not api.open then
					holder.Visible = false
					holder.Parent = f
				end
			end)
			if api.onToggle then api.onToggle(false) end
		end
		api.close = close

		function api.toggle(anchor)
			if api.open then close() return end
			local root = f
			while root.Parent and not root.Parent:IsA("ScreenGui") do root = root.Parent end
			local sc = root:FindFirstChildOfClass("UIScale")
			if not (root.Parent and sc) then return end
			CialloHub._toggleDrop(nil)
			api.open = true
			holder.Parent = root
			pscale.Scale = 0.96
			holder.Visible = true
			catcher = New("TextButton", {
				Name = "InlineCatcher", Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(0, 0, 0),
				BackgroundTransparency = 1, BorderSizePixel = 0, Text = "", AutoButtonColor = false, ZIndex = 30, Parent = root,
			})
			Tween(catcher, { BackgroundTransparency = 0.4 }, 0.15)
			catcher.InputBegan:Connect(function(i)
				if not isPress(i) then return end
				if inside(holder, Vector2.new(i.Position.X, i.Position.Y)) then return end
				close()
			end)

			local function place()
				if not api.open then return end
				holder.AnchorPoint = Vector2.new(0.5, 0.5)
				holder.Position = UDim2.fromScale(0.5, 0.5)
			end
			table.insert(conns, root:GetPropertyChangedSignal("Visible"):Connect(function()
				if not root.Visible then close() end
			end))
			place()
			task.defer(place)
			Tween(pscale, { Scale = 1 }, 0.14)
			if api.onToggle then api.onToggle(true) end
		end
		return api
	end

	local function inlineExtras(f, o, obj, boxW)
		local slot = 0
		local function button(bg)
			local rightX = 4 + boxW + 8 + slot * 28
			slot = slot + 1
			return New("TextButton", {
				AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -rightX, 0, 20), Size = UDim2.fromOffset(22, 22),
				BackgroundColor3 = bg or "$Element", Text = "", AutoButtonColor = false, ZIndex = 2, Parent = f,
			}, { Corner(5), Stroke("Border", 0.3) })
		end

		if o.Options ~= nil then
			local pop = inlinePopup(f, tonumber(o.OptionsWidth) or 340)
			Text(pop.Holder, TC(o.OptionsTitle or ((o.Title or o.Name or "选项") .. " · 详细设置")), {
				Size = UDim2.new(1, 0, 0, 20), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub",
				TextTruncate = Enum.TextTruncate.AtEnd,
			})
			if type(o.Options) == "function" then
				safe(o.Options, pop.Menu)
			elseif type(o.Options) == "table" then
				for _, e in ipairs(o.Options) do
					local kind = e.Type or "Toggle"
					local fn = pop.Menu["Add" .. kind]
					if fn then fn(pop.Menu, e) else warn("[Ciallo Hub] Unknown options element type \"" .. tostring(kind) .. "\"") end
				end
			end

			local b = button()
			local mark = Icon(b, "settings", 14, "Sub", { AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), ZIndex = 3 })
			if not mark.Visible then
				mark:Destroy()
				mark = Text(b, "...", {
					Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, Font = T.FontBold,
					TextColor3 = "$Sub", ZIndex = 3,
				})
			end
			local prop = mark:IsA("ImageLabel") and "ImageColor3" or "TextColor3"
			local isOpen, hover = false, false
			local function paint()
				Tween(b, { BackgroundColor3 = (hover or isOpen) and T.Hover or T.Element }, 0.1)
				Tween(mark, { [prop] = isOpen and T.Accent or (hover and T.Text or T.Sub) }, 0.1)
			end
			b.MouseEnter:Connect(function() hover = true paint() end)
			b.MouseLeave:Connect(function() hover = false paint() end)
			pop.onToggle = function(v) isOpen = v hover = false paint() end
			b.MouseButton1Click:Connect(function() pop.toggle(b) end)
			CialloHub:_AddPaint(paint, f)
			obj.Menu = pop.Menu
		end

		if o.Color ~= nil then
			local cs = type(o.Color) == "table" and o.Color or { Default = typeof(o.Color) == "Color3" and o.Color or nil }
			local pop = inlinePopup(f, tonumber(cs.Width) or 260)
			local sw = button(cs.Default or T.Accent)
			local picker = pop.Menu:AddColorPicker({
				Title = cs.Title or "颜色", Flag = cs.Flag, Default = cs.Default, DefaultAlpha = cs.DefaultAlpha, Popup = false,
				Rainbow = cs.Rainbow, RainbowSpeed = cs.RainbowSpeed, RainbowCallback = cs.RainbowCallback,
				Callback = function(c, a)
					sw.BackgroundColor3 = c
					safe(cs.Callback, c, a)
				end,
			})
			sw.BackgroundColor3 = picker:Get()
			sw.MouseButton1Click:Connect(function()
				pop.toggle(sw)
				if pop.open then picker:Expand(true) end
			end)
			obj.ColorPicker = picker
		end
	end

	function self:AddTitle(text)
		local l = Text(getParent(), TC(text), { Size = UDim2.new(1, 0, 0, 26), Font = T.FontBold, TextSize = 15 })
		return { Set = function(_, s) l.Text = TC(s) end }
	end

	function self:AddDivider()
		New("Frame", { Size = UDim2.new(1, 0, 0, 1), BackgroundColor3 = "$Border", BackgroundTransparency = 0.3, BorderSizePixel = 0, Parent = getParent() })
	end

	function self:AddLabel(text)
		local l = Text(getParent(), text, { Size = UDim2.new(1, 0, 0, 20), TextColor3 = "$Sub", TextSize = 12 })
		return { Set = function(_, s) l.Text = s end }
	end

	function self:AddParagraph(o)
		local f = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Element", Parent = getParent(),
		}, { Corner(5), Stroke("Border", 0.3), Pad(12, 10, 12, 12), List(4) })
		local title = Text(f, TC(o.Title or ""), { Size = UDim2.new(1, 0, 0, 18), Font = T.FontBold })
		local body = Text(f, o.Content or "", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, TextWrapped = true,
			TextColor3 = "$Sub", TextSize = 12, TextYAlignment = Enum.TextYAlignment.Top,
		})
		return { Set = function(_, t, c) title.Text = t and TC(t) or title.Text body.Text = c or body.Text end }
	end

	function self:AddButton(o)
		local d = o.Description and o.Description ~= ""
		local h = d and 60 or 38
		local holdTime = o.HoldTime or (type(o.Hold) == "number" and o.Hold) or (o.Hold and 1.2) or nil
		if holdTime then holdTime = math.max(holdTime, 0.2) end
		local f = New("Frame", {
			Size = UDim2.new(1, 0, 0, h), BackgroundColor3 = "$Element", ClipsDescendants = true, Parent = getParent(),
		}, { Corner(5), Stroke("Border", 0.3) })
		local fill
		if holdTime then
			fill = New("Frame", {
				Name = "HoldFill", Size = UDim2.new(0, 0, 1, 0), BackgroundColor3 = "$Accent", BackgroundTransparency = 0.72,
				BorderSizePixel = 0, ZIndex = 0, Parent = f,
			}, { Corner(5) })
		end
		local badge, bw = Badge(f, o.Badge)
		local btnLabel = Text(f, TC(o.Title or o.Name or ""), {
			Position = UDim2.fromOffset(12, d and 10 or 0), Size = UDim2.new(1, -(40 + (bw and (bw + 6) or 0)), 0, d and 18 or h),
			TextTruncate = Enum.TextTruncate.AtEnd,
		})
		attachTip(btnLabel, TC(o.Title or o.Name or ""), false, CialloHub.ButtonTipLength)
		attachBadge(btnLabel, badge, 12, d and 19 or h / 2)
		index(o.Title or o.Name, o.Description, f)
		if d then
			attachTip(Text(f, o.Description, {
				Position = UDim2.fromOffset(12, 32), Size = UDim2.new(1, -44, 0, 16), TextSize = 12,
				TextColor3 = "$Sub", TextTruncate = Enum.TextTruncate.AtEnd,
			}), o.Description, true)
		end
		local chevron = Icon(f, holdTime and "fingerprint" or "chevron-right", 14, "Sub", { AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0) })
		local ring, spin = Spinner(f, 14, { Position = UDim2.new(1, -17, 0.5, 0) })
		local b = New("TextButton", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", Parent = f })

		local obj, loading, auto = {}, false, false
		function obj:SetLoading(v)
			loading = v and true or false
			spin(loading)
			Tween(chevron, { ImageTransparency = loading and 1 or 0 }, 0.12)
			Tween(btnLabel, { TextTransparency = loading and 0.45 or 0 }, 0.12)
			Tween(f, { BackgroundColor3 = T.Element }, 0.12)
		end
		function obj:IsLoading() return loading end

		local hovered = false
		b.MouseEnter:Connect(function() hovered = true if not loading then Tween(f, { BackgroundColor3 = T.Hover }) end end)
		b.MouseLeave:Connect(function() hovered = false Tween(f, { BackgroundColor3 = T.Element }) end)
		local function activate()
			f.BackgroundColor3 = T.Accent
			Tween(f, { BackgroundColor3 = T.Hover }, 0.35)
			if not o.Callback then return end
			local done = false
			task.spawn(function()
				local ok, err = pcall(o.Callback)
				if not ok then warn("[Ciallo Hub] " .. tostring(err)) end
				done = true
				if auto then
					auto = false
					obj:SetLoading(false)
				end
				if not ok then flashError(f, function() return hovered and T.Hover or T.Element end) end
			end)
			task.delay(0.15, function()
				if not done and not loading then
					auto = true
					obj:SetLoading(true)
				end
			end)
		end

		if holdTime then
			local holding, holdId, holdTween = false, 0, nil
			local function cancelHold()
				if not holding then return end
				holding = false
				holdId = holdId + 1
				if holdTween then holdTween:Cancel() holdTween = nil end
				Tween(fill, { Size = UDim2.new(0, 0, 1, 0) }, 0.25)
			end
			b.InputBegan:Connect(function(i)
				if not isPress(i) or holding or loading or (obj.IsLocked and obj:IsLocked()) then return end
				holding = true
				holdId = holdId + 1
				local id = holdId
				fill.BackgroundTransparency = 0.72
				fill.Size = UDim2.new(0, 0, 1, 0)
				local tw = TS:Create(fill, TweenInfo.new(holdTime, Enum.EasingStyle.Linear), { Size = UDim2.new(1, 0, 1, 0) })
				holdTween = tw
				tw.Completed:Connect(function(state)
					if state ~= Enum.PlaybackState.Completed or not holding or holdId ~= id then return end
					holding = false
					holdTween = nil
					Tween(fill, { BackgroundTransparency = 1 }, 0.3)
					task.delay(0.35, function()
						if holdId == id and not holding then
							fill.Size = UDim2.new(0, 0, 1, 0)
							fill.BackgroundTransparency = 0.72
						end
					end)
					activate()
				end)
				tw:Play()
			end)
			b.InputEnded:Connect(function(i) if isPress(i) then cancelHold() end end)
			b.MouseLeave:Connect(cancelHold)
		else
			b.MouseButton1Click:Connect(function()
				if loading or (obj.IsLocked and obj:IsLocked()) then return end
				activate()
			end)
		end
		if o.Loading then obj:SetLoading(true) end
		lockable(f, o, obj)
		return obj
	end

	function self:AddToggle(o)
		local h = hgt(o)
		local f = row(h)
		local hasBind = o.Keybind ~= nil or o.Bind == true
		local boxW = o.Style == "Switch" and 34 or 20
		local extraW = (o.Options ~= nil and 28 or 0) + (o.Color ~= nil and 28 or 0)
		local label = head(f, o, boxW + 14 + extraW + (hasBind and 100 or 0), h, true)
		local paintBox
		if o.Style == "Switch" then
			paintBox = Switch(right(f, 34, 18, { BackgroundColor3 = "$Off" }))
		else
			paintBox = Checkbox(right(f, 20, 20, { BackgroundColor3 = "$Card" }))
		end
		local obj, state = {}, false
		local function paint()
			paintBox(state)
			Tween(label, { TextColor3 = state and T.Text or T.Sub })
		end
		function obj:Set(v, silent)
			state = v and true or false
			paint()
			if o.Flag then CialloHub.Flags[o.Flag] = state end
			if not silent then safe(o.Callback, state) end
		end
		function obj:Get() return state end
		New("TextButton", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", Parent = f }).MouseButton1Click:Connect(function()
			obj:Set(not state)
		end)

		if hasBind then
			local key, listening = o.Keybind, false
			local mode = o.KeyMode or "Toggle"
			local cap = Keycap(f, { Position = UDim2.new(1, -(4 + boxW + 10 + extraW), 0, 20) })
			local btn = New("TextButton", {
				AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -(4 + boxW + 10 + extraW), 0, 20), Size = UDim2.fromOffset(92, 26),
				BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 2, Parent = f,
			})
			local function paintKey()
				cap.set(listening and "请按键..." or keyName(key), listening)
			end
			local function setKey(k)
				key, listening = k, false
				CialloHub._listening = false
				paintKey()
				if o.Flag then CialloHub.Flags[o.Flag .. "_key"] = k end
			end
			function obj:SetKey(k) setKey(k) end
			function obj:GetKey() return key end
			btn.MouseButton1Click:Connect(function() listening = not listening CialloHub._listening = listening paintKey() end)
			connect(UIS.InputBegan, function(i, gp)
				if listening then
					local t = i.UserInputType
					if t == Enum.UserInputType.Keyboard then
						setKey(i.KeyCode ~= Enum.KeyCode.Escape and i.KeyCode or nil)
					elseif t == Enum.UserInputType.MouseButton2 or t == Enum.UserInputType.MouseButton3 then
						setKey(t)
					end
					return
				end
				if gp or obj:IsLocked() or not bindMatch(i, key) then return end
				if mode == "Hold" then obj:Set(true) else obj:Set(not state) end
			end)
			connect(UIS.InputEnded, function(i)
				if mode == "Hold" and state and bindMatch(i, key) then obj:Set(false) end
			end)
			CialloHub._keybinds = CialloHub._keybinds or {}
			table.insert(CialloHub._keybinds, { name = TC(o.Title or o.Name or ""), frame = f, key = function() return key end, mode = function() return mode end, state = function() return state end })
			CialloHub:_AddPaint(paintKey, f)
			if o.Flag then
				register(o.Flag .. "_key", { Set = function(_, k) setKey(k) end, Get = function() return key end }, key)
			end
			paintKey()
		end

		CialloHub:_AddPaint(paint, f)
		register(o.Flag, obj, false)
		obj:Set(o.Default or false, true)
		if o.Color ~= nil or o.Options ~= nil then inlineExtras(f, o, obj, boxW) end
		lockable(f, o, obj)
		return obj
	end

	function self:AddSlider(o)
		local min, max, inc = o.Min or 0, o.Max or 100, o.Increment or 1
		local h = hgt(o)
		local f = row(h)
		local hasToggle = o.Toggle == true

		local barW = math.max(30, tonumber(o.BarWidth) or (hasToggle and 52 or 96))
		local barX = hasToggle and 48 or 4
		head(f, o, barX + barW + 14 + 44 + 8, h)

		local val = New("TextBox", right(f, 44, 20, {
			Position = UDim2.new(1, -(barX + barW + 14), 0, 20), BackgroundTransparency = 1, Text = "", Font = T.Font, TextSize = 11,
			TextColor3 = "$Sub", TextXAlignment = Enum.TextXAlignment.Right, ClearTextOnFocus = false, ClipsDescendants = true,
		}))
		local bar = New("Frame", right(f, barW, 8, { BackgroundColor3 = "$Off", Position = UDim2.new(1, -barX, 0, 20) }), { Corner(4), Stroke("Border", 0.3) })
		local fill = New("Frame", { Size = UDim2.fromScale(0, 1), BackgroundColor3 = "$Accent", BorderSizePixel = 0, Parent = bar }, { Corner(4) })
		local obj, value, dragging = {}, min, false
		function obj:Set(v, silent)
			v = math.clamp(math.floor(v / inc + 0.5) * inc, min, max)
			v = math.floor(v * 10000 + 0.5) / 10000
			value = v
			local a = (max == min) and 0 or (v - min) / (max - min)
			Tween(fill, { Size = UDim2.fromScale(a, 1) }, 0.06)
			val.Text = tostring(v) .. (o.Suffix or "")
			if o.Flag then CialloHub.Flags[o.Flag] = v end
			if not silent then safe(o.Callback, v) end
		end
		function obj:Get() return value end

		local toggled = o.ToggleDefault == true
		local paintSw
		local function paintToggle()
			if not hasToggle then return end
			paintSw(toggled)
			Tween(fill, { BackgroundTransparency = toggled and 0 or 0.65 })
			Tween(val, { TextTransparency = toggled and 0 or 0.4 })
		end
		local tflag = o.ToggleFlag or (o.Flag and (o.Flag .. "_enabled"))
		function obj:SetToggle(v, silent)
			toggled = v and true or false
			paintToggle()
			if tflag then CialloHub.Flags[tflag] = toggled end
			if not silent then safe(o.ToggleCallback, toggled) end
		end
		function obj:GetToggle() return toggled end
		if hasToggle then
			paintSw = Switch(right(f, 34, 18, { BackgroundColor3 = "$Off" }))
			New("TextButton", {
				AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -2, 0, 20), Size = UDim2.fromOffset(42, 28),
				BackgroundTransparency = 1, Text = "", Parent = f,
			}).MouseButton1Click:Connect(function() obj:SetToggle(not toggled) end)
			CialloHub:_AddPaint(paintToggle, f)
			if tflag then
				register(tflag, { Set = function(_, v, silent) obj:SetToggle(v, silent) end, Get = function() return toggled end }, toggled)
			end
		end

		local fineF = o.FineFactor or 0.1
		local fine, anchorX, anchorV = false, 0, 0
		local function fromMouse(x)
			if UIS:IsKeyDown(Enum.KeyCode.LeftControl) or UIS:IsKeyDown(Enum.KeyCode.RightControl) then
				if not fine then fine, anchorX, anchorV = true, x, value end
				obj:Set(anchorV + (x - anchorX) / bar.AbsoluteSize.X * (max - min) * fineF)
			else
				fine = false
				obj:Set(min + (max - min) * math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1))
			end
		end
		local resetTo = o.Default or min
		local hit = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -(barX - 2), 0, 20), Size = UDim2.fromOffset(barW + 12, 28),
			BackgroundTransparency = 1, Text = "", Parent = f,
		})
		hit.InputBegan:Connect(function(i) if isPress(i) then dragging, fine = true, false fromMouse(i.Position.X) end end)

		hit.MouseButton2Click:Connect(function() obj:Set(resetTo) end)
		val.InputBegan:Connect(function(i)
			if i.UserInputType == Enum.UserInputType.MouseButton2 then val:ReleaseFocus() obj:Set(resetTo) end
		end)
		val.Focused:Connect(function()
			val.Text = tostring(value)
			Tween(val, { TextColor3 = T.Text }, 0.1)
			task.defer(function() val.SelectionStart = 1 val.CursorPosition = #val.Text + 1 end)
		end)
		val.FocusLost:Connect(function()
			Tween(val, { TextColor3 = T.Sub }, 0.1)
			local n = tonumber(val.Text:gsub(",", "."):match("-?%d+%.?%d*"))
			if n then obj:Set(n) else obj:Set(value, true) end
		end)
		connect(UIS.InputChanged, function(i) if dragging and isMove(i) then fromMouse(i.Position.X) end end)
		connect(UIS.InputEnded, function(i) if isPress(i) then dragging = false end end)
		register(o.Flag, obj, min)
		obj:Set(o.Default or min, true)
		obj:SetToggle(toggled, true)
		lockable(f, o, obj, nil, function(l) val.TextEditable = not l end)
		return obj
	end

	function self:AddNumberInput(o)
		local min, max, inc = o.Min or 0, o.Max or 100, o.Increment or 1
		local h = hgt(o)
		local f = row(h)
		head(f, o, 150, h)
		local frame = New("Frame", right(f, 132, 26, { BackgroundColor3 = "$Element" }), { Corner(4), Stroke("Border", 0.3) })
		local function stepper(text, pos)
			local b = New("TextButton", {
				Position = pos, Size = UDim2.fromOffset(26, 26), BackgroundTransparency = 1, Text = text,
				Font = T.FontBold, TextSize = 15, TextColor3 = "$Sub", AutoButtonColor = false, Parent = frame,
			})
			b.MouseEnter:Connect(function() Tween(b, { TextColor3 = T.Accent }, 0.1) end)
			b.MouseLeave:Connect(function() Tween(b, { TextColor3 = T.Sub }, 0.1) end)
			return b
		end
		local minus = stepper("-", UDim2.fromOffset(0, 0))
		local plus = stepper("+", UDim2.new(1, -26, 0, 0))
		local box = New("TextBox", {
			Position = UDim2.fromOffset(26, 0), Size = UDim2.new(1, -52, 1, 0), BackgroundTransparency = 1, Text = "",
			Font = T.Font, TextSize = 11, TextColor3 = "$Text", ClearTextOnFocus = false, ClipsDescendants = true, Parent = frame,
		})
		local obj, value, gen = {}, min, 0
		function obj:Set(v, silent)
			v = math.clamp(math.floor((tonumber(v) or min) / inc + 0.5) * inc, min, max)
			v = math.floor(v * 10000 + 0.5) / 10000
			value = v
			box.Text = tostring(v) .. (o.Suffix or "")
			if o.Flag then CialloHub.Flags[o.Flag] = v end
			if not silent then safe(o.Callback, v) end
		end
		function obj:Get() return value end
		local function stepSize()
			return inc * (UIS:IsKeyDown(Enum.KeyCode.LeftShift) and 10 or 1)
		end
		local function press(dir)
			if obj:IsLocked() then return end
			gen = gen + 1
			local my = gen
			obj:Set(value + dir * stepSize())
			task.spawn(function()
				task.wait(0.4)
				while gen == my and f.Parent do
					obj:Set(value + dir * stepSize())
					task.wait(0.06)
				end
			end)
		end
		minus.InputBegan:Connect(function(i) if isPress(i) then press(-1) end end)
		plus.InputBegan:Connect(function(i) if isPress(i) then press(1) end end)
		connect(UIS.InputEnded, function(i) if isPress(i) then gen = gen + 1 end end)
		box.Focused:Connect(function() box.Text = tostring(value) end)
		box.FocusLost:Connect(function()
			local n = tonumber((box.Text:gsub(",", "."):match("-?%d+%.?%d*")))
			if n then obj:Set(n) else obj:Set(value, true) end
		end)
		register(o.Flag, obj, min)
		obj:Set(o.Default or min, true)
		lockable(f, o, obj, nil, function(l) box.TextEditable = not l end)
		return obj
	end

	function self:AddDropdown(o)
		local values, multi = o.Values or {}, o.Multi == true
		local h = hgt(o)
		local f = row(h)
		head(f, o, 126, h)
		local boxStroke = Stroke("Border", 0.3)
		local box = New("Frame", right(f, 112, 26, { BackgroundColor3 = "$Element" }), { Corner(4), boxStroke })
		local shown = Text(box, "", {
			Position = UDim2.fromOffset(8, 0), Size = UDim2.new(1, -26, 1, 0), TextSize = 11,
			TextColor3 = "$Sub", TextTruncate = Enum.TextTruncate.AtEnd,
		})
		local arrow = Icon(box, "chevron-down", 12, "Sub", { AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -6, 0.5, 0) })

		local win = (self._ctx and self._ctx.win) or CialloHub.Windows[#CialloHub.Windows]
		local obj, sel, cur, items = {}, {}, nil, {}
		local popup, open = nil, false
		local ROW, MAXV = 28, 6

		local function useSearch()
			if o.Search ~= nil then return o.Search == true end
			return #values >= 8
		end
		local function popHeight(n, withSearch)
			return 12 + (withSearch and 32 or 0) + math.max(1, math.min(n, MAXV)) * ROW
		end
		local function result()
			if not multi then return cur end
			local t = {}
			for _, v in ipairs(values) do if sel[v] then table.insert(t, v) end end
			return t
		end
		local function paint()
			for v, it in pairs(items) do
				local on = multi and sel[v] or (cur == v)
				Tween(it.btn, { BackgroundColor3 = on and T.Hover or T.Element })
				Tween(it.label, { TextColor3 = on and T.Accent or T.Text })
				Tween(it.mark, { ImageTransparency = on and 0 or 1 })
			end
			if multi then
				local r = result()
				shown.Text = #r > 0 and table.concat(r, ", ") or "未选择"
			else
				shown.Text = cur and tostring(cur) or "未选择"
			end
		end
		local function fire(silent)
			if o.Flag then CialloHub.Flags[o.Flag] = result() end
			if not silent then safe(o.Callback, result()) end
		end

		local function close()
			if not open then return end
			open = false
			Tween(arrow, { Rotation = 0 })
			Tween(boxStroke, { Color = T.Border, Transparency = 0.3 })
			local p = popup
			popup, items = nil, {}
			if p then
				p.catcher:Destroy()
				Tween(p.group, { GroupTransparency = 1 }, 0.1)
				Tween(p.scale, { Scale = 0.96 }, 0.1)
				Tween(p.shadow, { GroupTransparency = 1 }, 0.1)
				task.delay(0.12, function() p.holder:Destroy() end)
			end
			paint()
		end
		local function choose(v)
			if multi then
				sel[v] = not sel[v] or nil
			else
				cur = v
			end
			paint()
			fire()
			if not multi then close() end
		end

		local function openPop()
			local main, sc = win and win._main, win and win._scale
			if not main then return end
			open = true
			local k = sc.Scale
			local rel = (box.AbsolutePosition - main.AbsolutePosition) / k
			local bw, bh = box.AbsoluteSize.X / k, box.AbsoluteSize.Y / k
			local mainW, mainH = main.AbsoluteSize.X / k, main.AbsoluteSize.Y / k
			local withSearch = useSearch()
			local popW = math.max(bw, 170)
			local popH = popHeight(#values, withSearch)
			local below = rel.Y + bh + 4
			local flip = below + popH > mainH - 28 and rel.Y - 4 - popH > 8
			local px = math.clamp(rel.X + bw, math.min(popW + 8, mainW - 8), mainW - 8)
			local py = flip and (rel.Y - 4) or below

			local catcher = New("TextButton", {
				Name = "DropCatcher", Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "",
				AutoButtonColor = false, ZIndex = 39, Parent = main,
			})
			local holder = New("Frame", {
				Name = "DropPopup", AnchorPoint = Vector2.new(1, flip and 1 or 0), Position = UDim2.fromOffset(px, py),
				Size = UDim2.fromOffset(popW, popH), BackgroundTransparency = 1, ZIndex = 40, Parent = main,
			})
			local pscale = New("UIScale", { Scale = 0.96, Parent = holder })
			local shadow = New("CanvasGroup", {
				Name = "Shadow", Position = UDim2.fromOffset(-16, -10), Size = UDim2.new(1, 32, 1, 32),
				BackgroundTransparency = 1, GroupTransparency = 1, ZIndex = 40, Parent = holder,
			})
			for n = 1, 6 do
				local inset = (n - 1) * 3
				New("Frame", {
					AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
					Size = UDim2.new(1, -inset * 2, 1, -inset * 2), BackgroundColor3 = Color3.new(0, 0, 0),
					BackgroundTransparency = 0.92, BorderSizePixel = 0, ZIndex = 40, Parent = shadow,
				}, { New("UICorner", { CornerRadius = UDim.new(0, 6 + (7 - n) * 3) }) })
			end
			local group = New("CanvasGroup", {
				Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Card", GlassKey = "Popup", GroupTransparency = 1, ZIndex = 41, Parent = holder,
			}, { Corner(6), Stroke("Border", 0) })

			local searchBox
			if withSearch then
				local sbox = New("Frame", {
					Position = UDim2.fromOffset(6, 6), Size = UDim2.new(1, -12, 0, 28), BackgroundColor3 = "$Element", Parent = group,
				}, { Corner(4), Stroke("Border", 0.3) })
				Icon(sbox, "search", 12, "Sub", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 8, 0.5, 0) })
				searchBox = New("TextBox", {
					Position = UDim2.fromOffset(26, 0), Size = UDim2.new(1, -32, 1, 0), BackgroundTransparency = 1, Text = "",
					PlaceholderText = "搜索选项...", PlaceholderColor3 = "$Sub", Font = T.Font, TextSize = 11, TextColor3 = "$Text",
					TextXAlignment = Enum.TextXAlignment.Left, ClearTextOnFocus = false, Parent = sbox,
				})
			end
			local listY = 6 + (withSearch and 32 or 0)
			local list = New("ScrollingFrame", {
				Position = UDim2.fromOffset(6, listY), Size = UDim2.new(1, -12, 0, popH - listY - 6), BackgroundTransparency = 1,
				BorderSizePixel = 0, AutomaticCanvasSize = Enum.AutomaticSize.Y, CanvasSize = UDim2.new(), Parent = group,
			}, { List(2), Pad(0, 0, 8, 0) })
			Scroller(list)

			local newItems, order = {}, {}
			for idx, v in ipairs(values) do
				local b = New("TextButton", {
					Size = UDim2.new(1, 0, 0, 26), BackgroundColor3 = "$Element", AutoButtonColor = false, Text = "",
					LayoutOrder = idx, Parent = list,
				}, { Corner(4) })
				local lbl = Text(b, tostring(v), {
					Position = UDim2.fromOffset(10, 0), Size = UDim2.new(1, -34, 1, 0), TextSize = 12,
					TextTruncate = Enum.TextTruncate.AtEnd,
				})
				local mark = Icon(b, "check", 14, "Accent", {
					AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0), ImageTransparency = 1,
				})
				local it = { btn = b, label = lbl, mark = mark, value = v }
				newItems[v] = it
				table.insert(order, it)
				b.MouseEnter:Connect(function()
					local on = multi and sel[v] or (cur == v)
					if not on then Tween(b, { BackgroundColor3 = T.Element:Lerp(T.Hover, 0.6) }, 0.08) end
				end)
				b.MouseLeave:Connect(function()
					local on = multi and sel[v] or (cur == v)
					Tween(b, { BackgroundColor3 = on and T.Hover or T.Element }, 0.08)
				end)
				b.MouseButton1Click:Connect(function() choose(v) end)
			end
			local empty = Text(list, #values == 0 and "暂无选项" or "无匹配结果", {
				Size = UDim2.new(1, 0, 0, 26), TextColor3 = "$Sub", TextSize = 12, LayoutOrder = 99999, Visible = #values == 0,
			})
			Pad(10, 0, 0, 0).Parent = empty

			local function applyFilter(q)
				q = q:lower():match("^%s*(.-)%s*$")
				local n = 0
				for _, it in ipairs(order) do
					local show = q == "" or tostring(it.value):lower():find(q, 1, true) ~= nil
					it.btn.Visible = show
					if show then n = n + 1 end
				end
				empty.Visible = n == 0
				list.CanvasPosition = Vector2.zero
				local newH = popHeight(n, withSearch)
				Tween(holder, { Size = UDim2.fromOffset(popW, newH) }, 0.12)
				Tween(list, { Size = UDim2.new(1, -12, 0, newH - listY - 6) }, 0.12)
			end
			if searchBox then
				searchBox:GetPropertyChangedSignal("Text"):Connect(function() applyFilter(searchBox.Text) end)
				searchBox.FocusLost:Connect(function(enter)
					if not enter then return end
					for _, it in ipairs(order) do
						if it.btn.Visible then
							choose(it.value)
							break
						end
					end
				end)
			end

			popup = { holder = holder, catcher = catcher, group = group, scale = pscale, shadow = shadow }
			items = newItems
			paint()
			catcher.MouseButton1Click:Connect(close)
			Tween(group, { GroupTransparency = 0 }, 0.14)
			Tween(pscale, { Scale = 1 }, 0.14)
			Tween(shadow, { GroupTransparency = 0 }, 0.14)
			Tween(arrow, { Rotation = 180 })
			Tween(boxStroke, { Color = T.Accent, Transparency = 0 })
			if searchBox then
				task.defer(function() if searchBox.Parent then searchBox:CaptureFocus() end end)
			end
		end

		local function toggle(force)
			local want
			if force == nil then want = not open else want = force end
			if want == open then return open end
			if want then openPop() else close() end
			return open
		end
		CialloHub._drops[f] = toggle

		function obj:Set(v, silent)
			sel, cur = {}, nil
			if multi then
				for _, x in ipairs(type(v) == "table" and v or {}) do sel[x] = true end
			else
				cur = v
			end
			paint()
			fire(silent)
		end
		function obj:Get() return result() end
		function obj:Refresh(newValues)
			values, sel, cur = newValues or {}, {}, nil
			close()
			paint()
			fire(true)
		end

		New("TextButton", { Size = UDim2.new(1, 0, 0, h), BackgroundTransparency = 1, Text = "", Parent = f }).MouseButton1Click:Connect(function()
			CialloHub._toggleDrop(f)
		end)
		CialloHub:_AddPaint(paint, f)
		paint()
		register(o.Flag, obj, multi and {} or nil)
		if o.Default ~= nil then obj:Set(o.Default, true) end
		lockable(f, o, obj)
		return obj
	end

	function self:AddTextbox(o)
		local h = hgt(o)
		local f = row(h)
		head(f, o, 126, h)

		local st = Stroke("Border", 0.3)
		local frame = New("Frame", right(f, 112, 26, { BackgroundColor3 = "$Element" }), { Corner(4), st })
		local box = New("TextBox", {
			Position = UDim2.fromOffset(8, 0), Size = UDim2.new(1, -16, 1, 0), BackgroundTransparency = 1,
			Text = o.Default or "", PlaceholderText = o.Placeholder or "在此输入...", PlaceholderColor3 = "$Sub",
			TextColor3 = "$Text", Font = T.Font, TextSize = 11, TextXAlignment = Enum.TextXAlignment.Left,
			ClearTextOnFocus = false, ClipsDescendants = true, TextWrapped = false, Parent = frame,
		})
		box.Focused:Connect(function() Tween(st, { Color = T.Accent, Transparency = 0 }) end)
		box.FocusLost:Connect(function(enter)
			Tween(st, { Color = T.Border, Transparency = 0.3 })
			if o.Numeric then box.Text = tostring(tonumber(box.Text) or "") end
			if o.Flag then CialloHub.Flags[o.Flag] = box.Text end
			if o.Finished == false or enter then safe(o.Callback, box.Text, enter) end
		end)
		local obj = {}
		function obj:Set(s) box.Text = s if o.Flag then CialloHub.Flags[o.Flag] = s end end
		function obj:Get() return box.Text end
		register(o.Flag, obj, box.Text)
		lockable(f, o, obj, nil, function(l) box.TextEditable = not l end)
		return obj
	end
	self.AddInput = self.AddTextbox

	function self:AddKeybind(o)
		local h = hgt(o)
		local f = row(h)
		head(f, o, 108, h)
		local mode = o.Mode or "Press"
		local key, listening, state = o.Default, false, false
		local cap = Keycap(f, { Position = UDim2.new(1, -4, 0, 20) })
		local btn = New("TextButton", right(f, 100, 26, {
			BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 2,
		}))
		local obj = {}
		local function paint()
			cap.set(listening and "请按键..." or keyName(key), listening)
		end

		function obj:Set(k)
			key = k listening = false CialloHub._listening = false paint()
			if o.Flag then CialloHub.Flags[o.Flag] = k end
			if o.MenuKey then CialloHub:SetMenuKey(k) end
		end
		function obj:Get() return key end
		function obj:GetState() return state end
		btn.MouseButton1Click:Connect(function()
			listening = not listening
			CialloHub._listening = listening
			paint()
		end)
		connect(UIS.InputBegan, function(i, gp)
			if listening then
				local t = i.UserInputType
				if t == Enum.UserInputType.Keyboard then
					obj:Set(i.KeyCode ~= Enum.KeyCode.Escape and i.KeyCode or nil)
					safe(o.ChangedCallback, key)
				elseif o.MenuKey then

				elseif t == Enum.UserInputType.MouseButton2 or t == Enum.UserInputType.MouseButton3 then
					obj:Set(t)
					safe(o.ChangedCallback, key)
				end
				return
			end
			if gp or o.MenuKey or obj:IsLocked() or not bindMatch(i, key) then return end
			if mode == "Hold" then
				state = true safe(o.Callback, true)
			elseif mode == "Toggle" then
				state = not state safe(o.Callback, state)
			else
				safe(o.Callback, key)
			end
		end)
		connect(UIS.InputEnded, function(i)
			if mode == "Hold" and state and bindMatch(i, key) then state = false safe(o.Callback, false) end
		end)
		CialloHub:_AddPaint(paint, f)
		if not o.MenuKey then
			CialloHub._keybinds = CialloHub._keybinds or {}
			table.insert(CialloHub._keybinds, { name = TC(o.Title or o.Name or ""), frame = f, key = function() return key end, mode = function() return mode end, state = function() return state end })
		end
		register(o.Flag, obj, key)
		paint()
		if o.MenuKey then CialloHub:SetMenuKey(key) end
		lockable(f, o, obj)
		return obj
	end

	function self:AddColorPicker(o)
		local h = hgt(o)
		local f = row(h)
		head(f, o, 36, h)
		local preview = New("Frame", right(f, 20, 20, {}), { Corner(10), Stroke("Border", 0.2) })
		local hh, ss, vv = (o.Default or T.Accent):ToHSV()
		local aa = math.clamp(tonumber(o.DefaultAlpha) or 1, 0, 1)
		local rainbow, speed = false, o.RainbowSpeed or 0.25
		local sv = New("Frame", {
			Position = UDim2.fromOffset(0, h + 8), Size = UDim2.new(1, -34, 0, 96),
			BackgroundColor3 = Color3.fromHSV(hh, 1, 1), BorderSizePixel = 0, Parent = f,
		}, { Corner(5) })
		New("Frame", { Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, Parent = sv },
			{ Corner(5), New("UIGradient", { Transparency = NumberSequence.new(0, 1) }) })
		New("Frame", { Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(0, 0, 0), BorderSizePixel = 0, Parent = sv },
			{ Corner(5), New("UIGradient", { Rotation = 90, Transparency = NumberSequence.new(1, 0) }) })
		local cur = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(10, 10), BackgroundTransparency = 1, Parent = sv,
		}, { Corner(5), New("UIStroke", { Color = Color3.new(1, 1, 1), Thickness = 2 }) })
		local keys = {}
		for i = 0, 6 do table.insert(keys, ColorSequenceKeypoint.new(i / 6, Color3.fromHSV(i / 6, 1, 1))) end
		local hue = New("Frame", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -4, 0, h + 8), Size = UDim2.fromOffset(20, 96),
			BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, Parent = f,
		}, { Corner(5), New("UIGradient", { Rotation = 90, Color = ColorSequence.new(keys) }) })
		local hcur = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.new(1, 4, 0, 4), BackgroundColor3 = Color3.new(1, 1, 1), Parent = hue,
		}, { Corner(2), New("UIStroke", { Color = Color3.new(0, 0, 0), Transparency = 0.5 }) })

		local abar = New("Frame", {
			Position = UDim2.fromOffset(0, h + 112), Size = UDim2.new(1, -4, 0, 12), BackgroundColor3 = "$Off",
			BorderSizePixel = 0, ClipsDescendants = true, Parent = f,
		}, { Corner(6) })
		local afill = New("Frame", {
			Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, Parent = abar,
		}, { New("UIGradient", { Transparency = NumberSequence.new(1, 0) }) })
		local acur = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.new(0, 4, 1, 0), BackgroundColor3 = Color3.new(1, 1, 1),
			BorderSizePixel = 0, ZIndex = 2, Parent = abar,
		}, { Corner(2), New("UIStroke", { Color = Color3.new(0, 0, 0), Transparency = 0.5 }) })

		local hexFrame = New("Frame", {
			Position = UDim2.fromOffset(2, h + 132), Size = UDim2.fromOffset(100, 24), BackgroundColor3 = "$Element", Parent = f,
		}, { Corner(4), Stroke("Border", 0.3) })
		local hexBox = New("TextBox", {
			Position = UDim2.fromOffset(8, 0), Size = UDim2.new(1, -16, 1, 0), BackgroundTransparency = 1, Text = "",
			PlaceholderText = "#FFFFFF", PlaceholderColor3 = "$Sub", Font = T.Font, TextSize = 11, TextColor3 = "$Text",
			TextXAlignment = Enum.TextXAlignment.Left, ClearTextOnFocus = false, Parent = hexFrame,
		})
		Text(f, "彩虹流光", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -46, 0, h + 135), Size = UDim2.fromOffset(70, 18),
			TextSize = 12, TextColor3 = "$Sub", TextXAlignment = Enum.TextXAlignment.Right,
		})
		local paintRb = Switch({
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -4, 0, h + 135), Size = UDim2.fromOffset(34, 18),
			BackgroundColor3 = "$Off", Parent = f,
		})
		local rbHit = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, h + 132), Size = UDim2.fromOffset(120, 24),
			BackgroundTransparency = 1, Text = "", Parent = f,
		})

		CialloHub.SavedColors = CialloHub.SavedColors or {}
		local savedRow = New("Frame", {
			Position = UDim2.fromOffset(2, h + 168), Size = UDim2.new(1, -8, 0, 24), BackgroundTransparency = 1, Parent = f,
		}, {
			New("UIListLayout", {
				FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder,
				VerticalAlignment = Enum.VerticalAlignment.Center,
			}),
		})
		local addBtn = New("TextButton", {
			Size = UDim2.fromOffset(20, 20), BackgroundColor3 = "$Element", Text = "+", Font = T.FontBold, TextSize = 14,
			TextColor3 = "$Sub", AutoButtonColor = false, LayoutOrder = 0, Parent = savedRow,
		}, { Corner(5), Stroke("Border", 0.3) })

		local obj, color, mode, rbConn = {}, nil, nil, nil
		local saved = {}
		local apply

		local function refreshSaved()
			for _, c in ipairs(saved) do c:Destroy() end
			saved = {}
			for i, c in ipairs(CialloHub.SavedColors) do
				local b = New("TextButton", {
					Size = UDim2.fromOffset(20, 20), BackgroundColor3 = c, Text = "", AutoButtonColor = false,
					LayoutOrder = i, Parent = savedRow,
				}, { Corner(5), Stroke("Border", 0.3) })
				b.MouseButton1Click:Connect(function() hh, ss, vv = c:ToHSV() apply() end)
				b.MouseButton2Click:Connect(function()
					local at = table.find(CialloHub.SavedColors, c)
					if at then table.remove(CialloHub.SavedColors, at) end
					refreshSaved()
				end)
				table.insert(saved, b)
			end
		end
		addBtn.MouseButton1Click:Connect(function()
			table.insert(CialloHub.SavedColors, 1, color)
			while #CialloHub.SavedColors > 8 do table.remove(CialloHub.SavedColors) end
			refreshSaved()
		end)

		apply = function(silent)
			color = Color3.fromHSV(hh, ss, vv)
			sv.BackgroundColor3 = Color3.fromHSV(hh, 1, 1)
			afill.BackgroundColor3 = color
			cur.Position = UDim2.fromScale(ss, 1 - vv)
			hcur.Position = UDim2.new(0.5, 0, hh, 0)
			acur.Position = UDim2.new(aa, 0, 0.5, 0)
			preview.BackgroundColor3 = color
			preview.BackgroundTransparency = math.min(1 - aa, 0.85)
			if not hexBox:IsFocused() then
				local hx = "#" .. color:ToHex():upper()
				if hexBox.Text ~= hx then hexBox.Text = hx end
			end
			if o.Flag then
				CialloHub.Flags[o.Flag] = color
				CialloHub.Flags[o.Flag .. "_alpha"] = aa
			end
			if not silent then safe(o.Callback, color, aa) end
		end

		local function setRainbow(v, silent)
			rainbow = v and true or false
			paintRb(rainbow)
			if o.Flag then CialloHub.Flags[o.Flag .. "_rainbow"] = rainbow end
			if rbConn then rbConn:Disconnect() rbConn = nil end
			if rainbow then
				rbConn = connect(RunService.RenderStepped, function()
					if not f.Parent then return end
					hh = (os.clock() * speed) % 1
					local isOpen = obj._open or f.Size.Y.Offset > h + 20
					if not isOpen then
						if os.clock() - (obj._lastRb or 0) < 0.05 then return end
						obj._lastRb = os.clock()
					end
					apply()
				end)
			end
			if not silent then safe(o.RainbowCallback, rainbow) end
		end
		rbHit.MouseButton1Click:Connect(function()
			if obj:IsLocked() then return end
			setRainbow(not rainbow)
		end)
		CialloHub:_AddPaint(function() paintRb(rainbow) end, f)

		hexBox.FocusLost:Connect(function()
			local s = hexBox.Text:gsub("[^%x]", "")
			if #s == 3 then s = s:gsub("(%x)", "%1%1") end
			if #s == 6 then
				local ok, c = pcall(Color3.fromHex, s)
				if ok then
					hh, ss, vv = c:ToHSV()
					apply()
					return
				end
			end
			apply(true)
		end)

		local function drag(pos)
			if mode == "sv" then
				ss = math.clamp((pos.X - sv.AbsolutePosition.X) / sv.AbsoluteSize.X, 0, 1)
				vv = 1 - math.clamp((pos.Y - sv.AbsolutePosition.Y) / sv.AbsoluteSize.Y, 0, 1)
			elseif mode == "hue" then
				if rainbow then return end
				hh = math.clamp((pos.Y - hue.AbsolutePosition.Y) / hue.AbsoluteSize.Y, 0, 1)
			elseif mode == "alpha" then
				aa = math.clamp((pos.X - abar.AbsolutePosition.X) / abar.AbsoluteSize.X, 0, 1)
			end
			apply()
		end
		sv.InputBegan:Connect(function(i) if isPress(i) then mode = "sv" drag(i.Position) end end)
		hue.InputBegan:Connect(function(i) if isPress(i) then mode = "hue" drag(i.Position) end end)
		abar.InputBegan:Connect(function(i) if isPress(i) then mode = "alpha" drag(i.Position) end end)
		connect(UIS.InputChanged, function(i) if mode and isMove(i) then drag(i.Position) end end)
		connect(UIS.InputEnded, function(i) if isPress(i) then mode = nil end end)

		local asPopup = o.Popup == true
		if asPopup then
			local PW, PH = 244, 198
			local inner = New("Frame", {
				Name = "PickerBody", Position = UDim2.fromOffset(0, -h), Size = UDim2.new(1, 0, 0, h + PH),
				BackgroundTransparency = 1, Parent = f,
			})
			for _, c in ipairs(f:GetChildren()) do
				if c ~= inner and c:IsA("GuiObject") and c.Position.Y.Offset >= h then c.Parent = inner end
			end

			local win = (self._ctx and self._ctx.win) or CialloHub.Windows[#CialloHub.Windows]
			local holder, pscale, catcher
			local open = false

			local function build(main)
				holder = New("Frame", {
					Name = "ColorPopup", AnchorPoint = Vector2.new(1, 0), Size = UDim2.fromOffset(PW, PH + 16),
					BackgroundTransparency = 1, Visible = false, ZIndex = 45, Parent = main,
				})
				pscale = New("UIScale", { Scale = 0.96, Parent = holder })
				local card = New("Frame", {
					Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Card", GlassKey = "Popup", Active = true,
					ZIndex = 46, Parent = holder,
				}, { Corner(6), Stroke("Border", 0) })
				local pc = New("Frame", {
					Position = UDim2.fromOffset(8, 8), Size = UDim2.fromOffset(PW - 16, PH), BackgroundTransparency = 1,
					ClipsDescendants = true, ZIndex = 46, Parent = card,
				})
				inner.Parent = pc
			end

			local function close()
				if not open then return end
				open = false
				obj._open = false
				mode = nil
				if catcher then catcher:Destroy() catcher = nil end
				Tween(pscale, { Scale = 0.96 }, 0.1)
				task.delay(0.1, function()
					if not open and holder then holder.Visible = false end
				end)
			end

			local function openPop()
				local main, sc = win and win._main, win and win._scale
				if not main then return end
				if not holder then build(main) end
				open = true
				obj._open = true
				refreshSaved()
				local k = sc.Scale
				local rel = (f.AbsolutePosition - main.AbsolutePosition) / k
				local fw, fh = f.AbsoluteSize.X / k, f.AbsoluteSize.Y / k
				local mw, mh = main.AbsoluteSize.X / k, main.AbsoluteSize.Y / k
				local ph = PH + 16
				local below = rel.Y + fh + 4
				local py = below
				if below + ph > mh - 28 then py = rel.Y - 4 - ph end
				py = math.clamp(py, 8, math.max(8, mh - 32 - ph))
				local px = math.clamp(rel.X + fw, math.min(PW + 8, mw - 8), mw - 8)
				holder.Position = UDim2.fromOffset(math.floor(px + 0.5), math.floor(py + 0.5))
				catcher = New("TextButton", {
					Name = "ColorCatcher", Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "",
					AutoButtonColor = false, ZIndex = 44, Parent = main,
				})
				catcher.MouseButton1Click:Connect(close)
				pscale.Scale = 0.96
				holder.Visible = true
				Tween(pscale, { Scale = 1 }, 0.14)
			end

			CialloHub._drops[f] = function(force)
				local want
				if force == nil then want = not open else want = force end
				if want == open then return open end
				if want then openPop() else close() end
				return open
			end

			New("TextButton", { Size = UDim2.new(1, 0, 0, h), BackgroundTransparency = 1, Text = "", Parent = f }).MouseButton1Click:Connect(function()
				CialloHub._toggleDrop(f)
			end)
		else
			makeDrop(f, h, function() return 228 end)
			New("TextButton", { Size = UDim2.new(1, 0, 0, h), BackgroundTransparency = 1, Text = "", Parent = f }).MouseButton1Click:Connect(function()
				refreshSaved()
				CialloHub._toggleDrop(f)
			end)
		end

		function obj:Set(c, silent) hh, ss, vv = c:ToHSV() apply(silent) end
		function obj:Get() return color end
		function obj:SetAlpha(a, silent) aa = math.clamp(tonumber(a) or 1, 0, 1) apply(silent) end
		function obj:GetAlpha() return aa end
		function obj:SetRainbow(v, silent) setRainbow(v, silent) end
		function obj:GetRainbow() return rainbow end
		function obj:Expand(v)
			local d = CialloHub._drops[f]
			if not d then return end
			if v == false then
				d(false)
			else
				refreshSaved()
				d(true)
			end
		end

		apply(true)
		setRainbow(o.Rainbow == true, true)
		register(o.Flag, obj, color)
		if o.Flag then
			register(o.Flag .. "_alpha", { Set = function(_, v, s) obj:SetAlpha(v, s) end, Get = function() return aa end }, aa)
			register(o.Flag .. "_rainbow", { Set = function(_, v, s) setRainbow(v, s) end, Get = function() return rainbow end }, rainbow)
		end
		lockable(f, o, obj)
		return obj
	end

	function self:AddColorPickerOld(o)
		local h = hgt(o)
		local f = row(h)
		head(f, o, 36, h)
		local preview = New("Frame", right(f, 20, 20, {}), { Corner(10), Stroke("Border", 0.2) })
		local hh, ss, vv = (o.Default or T.Accent):ToHSV()
		local sv = New("Frame", {
			Position = UDim2.fromOffset(0, h + 8), Size = UDim2.new(1, -34, 0, 96),
			BackgroundColor3 = Color3.fromHSV(hh, 1, 1), BorderSizePixel = 0, Parent = f,
		}, { Corner(5) })
		New("Frame", { Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, Parent = sv },
			{ Corner(5), New("UIGradient", { Transparency = NumberSequence.new(0, 1) }) })
		New("Frame", { Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(0, 0, 0), BorderSizePixel = 0, Parent = sv },
			{ Corner(5), New("UIGradient", { Rotation = 90, Transparency = NumberSequence.new(1, 0) }) })
		local cur = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(10, 10), BackgroundTransparency = 1, Parent = sv,
		}, { Corner(5), New("UIStroke", { Color = Color3.new(1, 1, 1), Thickness = 2 }) })
		local keys = {}
		for i = 0, 6 do table.insert(keys, ColorSequenceKeypoint.new(i / 6, Color3.fromHSV(i / 6, 1, 1))) end
		local hue = New("Frame", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -4, 0, h + 8), Size = UDim2.fromOffset(20, 96),
			BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, Parent = f,
		}, { Corner(5), New("UIGradient", { Rotation = 90, Color = ColorSequence.new(keys) }) })
		local hcur = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.new(1, 4, 0, 4), BackgroundColor3 = Color3.new(1, 1, 1), Parent = hue,
		}, { Corner(2), New("UIStroke", { Color = Color3.new(0, 0, 0), Transparency = 0.5 }) })

		local obj, color, mode = {}, nil, nil
		local function apply(silent)
			color = Color3.fromHSV(hh, ss, vv)
			sv.BackgroundColor3 = Color3.fromHSV(hh, 1, 1)
			cur.Position = UDim2.fromScale(ss, 1 - vv)
			hcur.Position = UDim2.new(0.5, 0, hh, 0)
			preview.BackgroundColor3 = color
			if o.Flag then CialloHub.Flags[o.Flag] = color end
			if not silent then safe(o.Callback, color) end
		end
		local function drag(pos)
			if mode == "sv" then
				ss = math.clamp((pos.X - sv.AbsolutePosition.X) / sv.AbsoluteSize.X, 0, 1)
				vv = 1 - math.clamp((pos.Y - sv.AbsolutePosition.Y) / sv.AbsoluteSize.Y, 0, 1)
			elseif mode == "hue" then
				hh = math.clamp((pos.Y - hue.AbsolutePosition.Y) / hue.AbsoluteSize.Y, 0, 1)
			end
			apply()
		end
		sv.InputBegan:Connect(function(i) if isPress(i) then mode = "sv" drag(i.Position) end end)
		hue.InputBegan:Connect(function(i) if isPress(i) then mode = "hue" drag(i.Position) end end)
		connect(UIS.InputChanged, function(i) if mode and isMove(i) then drag(i.Position) end end)
		connect(UIS.InputEnded, function(i) if isPress(i) then mode = nil end end)

		makeDrop(f, h, function() return 112 end)
		New("TextButton", { Size = UDim2.new(1, 0, 0, h), BackgroundTransparency = 1, Text = "", Parent = f }).MouseButton1Click:Connect(function()
			CialloHub._toggleDrop(f)
		end)
		function obj:Set(c, silent) hh, ss, vv = c:ToHSV() apply(silent) end
		function obj:Get() return color end
		apply(true)
		register(o.Flag, obj, color)
		lockable(f, o, obj)
		return obj
	end

	function self:AddRangeSlider(o)
		local min, max, inc = o.Min or 0, o.Max or 100, o.Increment or 1
		local base = hgt(o)
		local f = row(base + 22)
		head(f, o, 124, base)
		local suffix = o.Suffix or ""
		local val = Text(f, "", right(f, 112, 20, {
			TextSize = 11, TextColor3 = "$Sub", TextXAlignment = Enum.TextXAlignment.Right, TextTruncate = Enum.TextTruncate.AtEnd,
		}))
		local bar = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.fromOffset(8, base + 8), Size = UDim2.new(1, -16, 0, 6),
			BackgroundColor3 = "$Off", Parent = f,
		}, { Corner(3), Stroke("Border", 0.3) })
		local fill = New("Frame", { BackgroundColor3 = "$Accent", BorderSizePixel = 0, Parent = bar }, { Corner(3) })
		local function knob()
			return New("Frame", {
				AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(14, 14), BackgroundColor3 = Color3.new(1, 1, 1),
				BorderSizePixel = 0, ZIndex = 3, Parent = bar,
			}, { Corner(7), New("UIStroke", { Color = "$Accent", Thickness = 2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }) })
		end
		local kLo, kHi = knob(), knob()
		local obj, lo, hi, dragging = {}, min, max, nil

		local function snap(v)
			v = math.clamp(math.floor(v / inc + 0.5) * inc, min, max)
			return math.floor(v * 10000 + 0.5) / 10000
		end
		local function paint()
			local span = (max == min) and 1 or (max - min)
			local a, b = (lo - min) / span, (hi - min) / span
			Tween(fill, { Position = UDim2.fromScale(a, 0), Size = UDim2.fromScale(b - a, 1) }, 0.06)
			Tween(kLo, { Position = UDim2.new(a, 0, 0.5, 0) }, 0.06)
			Tween(kHi, { Position = UDim2.new(b, 0, 0.5, 0) }, 0.06)
			val.Text = tostring(lo) .. suffix .. " ~ " .. tostring(hi) .. suffix
		end
		local function apply(a, b, silent)
			a, b = snap(tonumber(a) or min), snap(tonumber(b) or max)
			if a > b then a, b = b, a end
			lo, hi = a, b
			paint()
			if o.Flag then CialloHub.Flags[o.Flag] = { lo, hi } end
			if not silent then safe(o.Callback, lo, hi) end
		end
		function obj:Set(v, silent)
			if type(v) == "table" then apply(v[1] or v.Min, v[2] or v.Max, silent) end
		end
		function obj:SetRange(a, b, silent) apply(a, b, silent) end
		function obj:SetMin(v, silent) apply(math.min(tonumber(v) or min, hi), hi, silent) end
		function obj:SetMax(v, silent) apply(lo, math.max(tonumber(v) or max, lo), silent) end
		function obj:Get() return lo, hi end

		local function valueAt(x)
			local w = math.max(bar.AbsoluteSize.X, 1)
			return min + (max - min) * math.clamp((x - bar.AbsolutePosition.X) / w, 0, 1)
		end
		local function drag(x)
			local v = snap(valueAt(x))
			if dragging == "lo" then apply(math.min(v, hi), hi) else apply(lo, math.max(v, lo)) end
		end
		local function grow(k, big) Tween(k, { Size = UDim2.fromOffset(big and 17 or 14, big and 17 or 14) }, 0.08) end

		local hit = New("TextButton", {
			Position = UDim2.fromOffset(0, base - 4), Size = UDim2.new(1, 0, 0, 24), BackgroundTransparency = 1, Text = "", Parent = f,
		})
		hit.InputBegan:Connect(function(i)
			if not isPress(i) then return end
			local v = valueAt(i.Position.X)
			if lo == hi then
				dragging = (v < lo or (v <= lo and lo >= max)) and "lo" or "hi"
			else
				dragging = math.abs(v - lo) <= math.abs(v - hi) and "lo" or "hi"
			end
			grow(dragging == "lo" and kLo or kHi, true)
			drag(i.Position.X)
		end)
		connect(UIS.InputChanged, function(i) if dragging and isMove(i) then drag(i.Position.X) end end)
		connect(UIS.InputEnded, function(i)
			if dragging and isPress(i) then
				dragging = nil
				grow(kLo, false)
				grow(kHi, false)
			end
		end)

		local d = o.Default
		local defLo = (type(d) == "table" and (d[1] or d.Min)) or o.DefaultMin or min
		local defHi = (type(d) == "table" and (d[2] or d.Max)) or o.DefaultMax or max
		hit.MouseButton2Click:Connect(function() apply(defLo, defHi) end)
		register(o.Flag, obj, { defLo, defHi })
		apply(defLo, defHi, true)
		lockable(f, o, obj)
		return obj
	end

	function self:AddMultiSelect(o)
		local values = o.Values or {}
		local limit = o.Limit
		local base = hgt(o)
		local outer = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = getParent(),
		})
		local f = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = outer,
		}, { List(6) })
		local top = New("Frame", {
			Size = UDim2.new(1, 0, 0, base), BackgroundTransparency = 1, ClipsDescendants = true, LayoutOrder = 1, Parent = f,
		})
		head(top, o, 4, base)
		local box = New("Frame", {
			Size = UDim2.new(1, 0, 0, 36), AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Element",
			LayoutOrder = 2, Parent = f,
		}, { Corner(5), Stroke("Border", 0.3), Pad(8, 5, 8, 5) })
		local boxBtn = New("TextButton", {
			Position = UDim2.fromOffset(-8, -5), Size = UDim2.new(1, 16, 1, 10), BackgroundTransparency = 1, Text = "", ZIndex = 1, Parent = box,
		})
		local placeholder = Text(box, o.Placeholder or "请选择...", {
			Size = UDim2.new(1, -24, 0, 26), TextColor3 = "$Sub", TextSize = 12, ZIndex = 2,
		})
		local arrow = Icon(box, "chevron-down", 12, "Sub", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 7), ZIndex = 2,
		})
		local chipHost = New("Frame", {
			Size = UDim2.new(1, -22, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, ZIndex = 2, Parent = box,
		}, { New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Wraps = true, SortOrder = Enum.SortOrder.LayoutOrder }) })
		local listWrap = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), BackgroundTransparency = 1, ClipsDescendants = true, Visible = false, LayoutOrder = 3, Parent = f,
		})
		local list = New("ScrollingFrame", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarThickness = 2,
			ScrollBarImageColor3 = "$Accent", AutomaticCanvasSize = Enum.AutomaticSize.Y, CanvasSize = UDim2.new(), Parent = listWrap,
		}, { List(2) })
		Scroller(list)

		local sel, items, chips, paintAll, fire = {}, {}, {}, nil, nil
		local open = false
		local function listH() return math.min(#values * 28, 140) end
		local function toggle(force)
			if force == nil then open = not open else open = force end
			Tween(arrow, { Rotation = open and 180 or 0 })
			if open then
				listWrap.Visible = true
				Tween(listWrap, { Size = UDim2.new(1, 0, 0, listH()) }, 0.18)
			else
				Tween(listWrap, { Size = UDim2.new(1, 0, 0, 0) }, 0.18)
				task.delay(0.18, function() if not open then listWrap.Visible = false end end)
			end
			return open
		end
		CialloHub._drops[f] = toggle

		local function count()
			local n = 0
			for _ in pairs(sel) do n = n + 1 end
			return n
		end
		local function result()
			local t = {}
			for _, v in ipairs(values) do if sel[v] then table.insert(t, v) end end
			return t
		end
		local function chip(v)
			local label = tostring(v)
			local w = math.min(math.ceil(textWidth(label, fs(11), T.Font)) + 34, 200)
			local slot = New("Frame", { Size = UDim2.fromOffset(w + 6, 26), BackgroundTransparency = 1, Parent = chipHost })
			local c = New("Frame", {
				AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.new(1, -6, 0, 22),
				BackgroundColor3 = "$Accent", BackgroundTransparency = 0.82, Parent = slot,
			}, { Corner(11), New("UIStroke", { Color = "$Accent", Transparency = 0.6, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }) })
			Text(c, label, {
				Position = UDim2.fromOffset(10, 0), Size = UDim2.new(1, -30, 1, 0), TextSize = 11, TextColor3 = "$Accent",
				TextTruncate = Enum.TextTruncate.AtEnd,
			})
			Icon(c, "x", 10, "Accent", { AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0) })
			New("TextButton", {
				AnchorPoint = Vector2.new(1, 0), Position = UDim2.fromScale(1, 0), Size = UDim2.fromOffset(24, 22),
				BackgroundTransparency = 1, Text = "", Parent = c,
			}).MouseButton1Click:Connect(function()
				sel[v] = nil
				paintAll()
				fire()
			end)
			return slot
		end
		paintAll = function()
			for _, c in ipairs(chips) do c:Destroy() end
			chips = {}
			for i, v in ipairs(values) do
				if sel[v] then
					local c = chip(v)
					c.LayoutOrder = i
					table.insert(chips, c)
				end
			end
			placeholder.Visible = #chips == 0
			for v, it in pairs(items) do
				local on = sel[v] == true
				Tween(it.btn, { BackgroundColor3 = on and T.Hover or T.Element, TextColor3 = on and T.Accent or T.Text })
				Tween(it.mark, { ImageTransparency = on and 0 or 1 })
			end
		end
		fire = function(silent)
			local r = result()
			if o.Flag then CialloHub.Flags[o.Flag] = r end
			if not silent then safe(o.Callback, r) end
		end
		local function build()
			for _, it in pairs(items) do it.btn:Destroy() end
			items = {}
			for i, v in ipairs(values) do
				local b = New("TextButton", {
					Size = UDim2.new(1, 0, 0, 26), BackgroundColor3 = "$Element", AutoButtonColor = false, Text = "  " .. tostring(v),
					Font = T.Font, TextSize = 12, TextColor3 = "$Text", TextXAlignment = Enum.TextXAlignment.Left, LayoutOrder = i, Parent = list,
				}, { Corner(4) })
				local mark = Icon(b, "check", 14, "Accent", {
					AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0), ImageTransparency = 1,
				})
				b.MouseButton1Click:Connect(function()
					if sel[v] then
						sel[v] = nil
					else
						if limit and count() >= limit then return end
						sel[v] = true
					end
					paintAll()
					fire()
				end)
				items[v] = { btn = b, mark = mark }
			end
		end

		local obj = {}
		function obj:Set(v, silent)
			sel = {}
			for _, x in ipairs(type(v) == "table" and v or {}) do sel[x] = true end
			paintAll()
			fire(silent)
		end
		function obj:Get() return result() end
		function obj:Refresh(newValues)
			values, sel = newValues or {}, {}
			build()
			paintAll()
			toggle(false)
			fire(true)
		end

		boxBtn.MouseButton1Click:Connect(function() CialloHub._toggleDrop(f) end)
		CialloHub:_AddPaint(paintAll, f)
		register(o.Flag, obj, {})
		build()
		if o.Default ~= nil then obj:Set(o.Default, true) else paintAll() end
		lockable(outer, o, obj, f)
		return obj
	end

	function self:AddProgress(o)
		local min, max = o.Min or 0, o.Max or 100
		local base = hgt(o)
		local f = row(base + 20)
		head(f, o, 74, base)
		local val = Text(f, "", right(f, 66, 20, {
			TextSize = 11, TextColor3 = "$Sub", TextXAlignment = Enum.TextXAlignment.Right,
		}))
		local bar = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.fromOffset(4, base + 6), Size = UDim2.new(1, -8, 0, 8),
			BackgroundColor3 = "$Off", ClipsDescendants = true, Parent = f,
		}, { Corner(4), Stroke("Border", 0.3) })
		local fill = New("Frame", {
			Size = UDim2.fromScale(0, 1), BackgroundColor3 = o.Color or "$Accent", BorderSizePixel = 0, Parent = bar,
		}, { Corner(4) })
		local obj, value = {}, min
		function obj:Set(v, silent)
			value = math.clamp(tonumber(v) or min, min, max)
			local a = (max == min) and 0 or (value - min) / (max - min)
			Tween(fill, { Size = UDim2.fromScale(a, 1) }, o.Smooth == false and 0 or 0.3)
			if o.Suffix then
				val.Text = tostring(math.floor(value * 100 + 0.5) / 100) .. o.Suffix
			else
				val.Text = tostring(math.floor(a * 100 + 0.5)) .. "%"
			end
			if o.Flag then CialloHub.Flags[o.Flag] = value end
			if not silent then safe(o.Callback, value) end
		end
		function obj:Get() return value end
		register(o.Flag, obj, min)
		obj:Set(o.Default or o.Value or min, true)
		return obj
	end

	function self:AddImage(o)
		local h = o.Height or 130
		local title = TC(o.Title or o.Name or "")
		local f = New("Frame", {
			Size = UDim2.new(1, 0, 0, h), BackgroundColor3 = "$Element", ClipsDescendants = true, Parent = getParent(),
		}, { Corner(6), Stroke("Border", 0.3) })
		local scaleType = Enum.ScaleType.Crop
		if o.Fit == "Fit" then scaleType = Enum.ScaleType.Fit elseif o.Fit == "Stretch" then scaleType = Enum.ScaleType.Stretch end
		local bgImg = New("ImageLabel", {
			AnchorPoint = Vector2.new(0.5, 0.3), Position = UDim2.fromScale(0.5, 0.3),
			Size = UDim2.new(1, 0, 1.8, 0), BackgroundTransparency = 1, Image = "", ImageTransparency = 1,
			ScaleType = Enum.ScaleType.Crop, ZIndex = 1, Parent = f,
		}, { Corner(6) })
		local bgDim = New("Frame", {
			Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(0, 0, 0), BackgroundTransparency = 0.45,
			BorderSizePixel = 0, Visible = (o.Fit == "Fit"), ZIndex = 1, Parent = f,
		}, { Corner(6) })
		local img = New("ImageLabel", {
			AnchorPoint = Vector2.new(0.5, 0.32), Position = UDim2.fromScale(0.5, 0.32),
			Size = (o.Fit == "Crop" or o.Fit == nil) and UDim2.new(1, 0, 1.35, 0) or UDim2.fromScale(1, 1),
			BackgroundTransparency = 1, Image = "", ImageTransparency = 1, ScaleType = scaleType,
			ZIndex = 2, Parent = f,
		}, { Corner(6) })
		New("Frame", {
			AnchorPoint = Vector2.new(0, 1), Position = UDim2.fromScale(0, 1), Size = UDim2.new(1, 0, 0, 66),
			BackgroundColor3 = Color3.new(0, 0, 0), BorderSizePixel = 0, ZIndex = 3, Parent = f,
		}, {
			Corner(6),
			New("UIGradient", {
				Rotation = 90,
				Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(1, 0.2) }),
			}),
		})
		local titleL = Text(f, title, {
			Size = UDim2.new(1, -24, 0, 18), Font = T.FontBold, TextSize = 14, TextColor3 = Color3.new(1, 1, 1),
			TextTruncate = Enum.TextTruncate.AtEnd, ZIndex = 4,
		})
		local descL = Text(f, o.Description or "", {
			Size = UDim2.new(1, -24, 0, 14), TextSize = 11, TextColor3 = Color3.new(1, 1, 1), TextTransparency = 0.25,
			TextTruncate = Enum.TextTruncate.AtEnd, ZIndex = 4,
		})
		local function layout()
			local hasDesc = descL.Text ~= ""
			titleL.Position = UDim2.new(0, 12, 1, hasDesc and -40 or -28)
			descL.Position = UDim2.new(0, 12, 1, -22)
			titleL.Visible = titleL.Text ~= ""
		end
		layout()
		index(title, o.Description, f)
		local badge = Badge(f, o.Badge, true)
		if badge then
			badge.Position = UDim2.fromOffset(10, 18)
			badge.ZIndex = 5
		end
		if o.Callback then
			local b = New("TextButton", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", ZIndex = 6, Parent = f })
			b.MouseEnter:Connect(function() Tween(img, { ImageTransparency = 0.15 }) end)
			b.MouseLeave:Connect(function() Tween(img, { ImageTransparency = 0 }) end)
			b.MouseButton1Click:Connect(function() safe(o.Callback) end)
		end
		local obj = {}
		local function setImage(v)
			if v == nil or v == "" then return end
			task.spawn(function()
				img.ImageTransparency = 1
				bgImg.ImageTransparency = 1
				if loadIcon(img, v) then
					bgImg.Image = img.Image
					Tween(img, { ImageTransparency = 0 }, 0.25)
					Tween(bgImg, { ImageTransparency = 0.35 }, 0.25)
				else
					warn("[Ciallo Hub] 图片加载失败: " .. tostring(v))
				end
			end)
		end
		setImage(o.Image)
		function obj:SetImage(v) setImage(v) end
		function obj:SetFit(mode)
			if mode == "Fit" then
				img.ScaleType = Enum.ScaleType.Fit
				img.Size = UDim2.fromScale(1, 1)
				img.AnchorPoint = Vector2.new(0.5, 0.5)
				img.Position = UDim2.fromScale(0.5, 0.5)
				bgDim.Visible = true
			elseif mode == "Stretch" then
				img.ScaleType = Enum.ScaleType.Stretch
				img.Size = UDim2.fromScale(1, 1)
				img.AnchorPoint = Vector2.new(0.5, 0.5)
				img.Position = UDim2.fromScale(0.5, 0.5)
				bgDim.Visible = false
			else
				img.ScaleType = Enum.ScaleType.Crop
				img.Size = UDim2.new(1, 0, 1.35, 0)
				img.AnchorPoint = Vector2.new(0.5, 0.32)
				img.Position = UDim2.fromScale(0.5, 0.32)
				bgDim.Visible = false
			end
		end
		function obj:Set(t, d)
			if t ~= nil then titleL.Text = tostring(t) end
			if d ~= nil then descL.Text = tostring(d) end
			layout()
		end
		return obj
	end

	function self:AddTabs(o)
		local names, opts = {}, {}
		if type(o) == "table" then
			if o.Tabs then names, opts = o.Tabs, o elseif o[1] then names = o else opts = o end
		end
		local ctx = self._ctx
		local holder = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = getParent(),
		}, { List(8) })
		local bar = New("Frame", {
			Size = UDim2.new(1, 0, 0, 34), BackgroundColor3 = "$Element", LayoutOrder = 1, Parent = holder,
		}, { Corner(6), Stroke("Border", 0.3), Pad(3, 3, 3, 3) })
		local slider = New("Frame", { BackgroundColor3 = "$Hover", BorderSizePixel = 0, ZIndex = 1, Parent = bar }, { Corner(4) })
		local buttons = New("Frame", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, ZIndex = 2, Parent = bar,
		}, { New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, SortOrder = Enum.SortOrder.LayoutOrder }) })

		local tabs = { Pages = {}, _list = {}, _by = {} }
		local current
		local function choose(t, instant)
			if not t then return end
			CialloHub._toggleDrop(nil)
			current = t
			for _, x in ipairs(tabs._list) do
				x.page.Visible = x == t
				Tween(x.label, { TextColor3 = x == t and T.Accent or T.Sub })
			end
			Tween(slider, { Position = UDim2.new((t.index - 1) / #tabs._list, 0, 0, 0) }, instant and 0 or 0.18)
		end
		local function relayout()
			local n = #tabs._list
			for _, t in ipairs(tabs._list) do
				t.button.Size = UDim2.new(1 / n, 0, 1, 0)
			end
			slider.Size = UDim2.new(1 / n, 0, 1, 0)
			if current then slider.Position = UDim2.new((current.index - 1) / n, 0, 0, 0) end
		end
		CialloHub:_AddPaint(function()
			for _, t in ipairs(tabs._list) do t.label.TextColor3 = t == current and T.Accent or T.Sub end
		end, bar)

		function tabs:AddTab(name)
			name = tostring(name)
			local page = {}
			local btn = New("TextButton", {
				Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false,
				LayoutOrder = #tabs._list + 1, Parent = buttons,
			})
			local label = Text(btn, TC(name), {
				Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, TextSize = 12, Font = T.FontBold,
				TextColor3 = T.Sub, TextTruncate = Enum.TextTruncate.AtEnd,
			})
			local pf = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
				Visible = false, LayoutOrder = 2, Parent = holder,
			}, { List(6) })
			local t = { button = btn, label = label, page = pf, index = #tabs._list + 1, name = name }
			if ctx then
				page._ctx = {
					win = ctx.win, tab = ctx.tab, tabName = ctx.tabName,
					section = (ctx.section and (ctx.section .. " > ") or "") .. name,
					reveal = function()
						local changed = ctx.reveal and ctx.reveal()
						choose(t)
						return changed
					end,
				}
			end
			page.Frame = pf
			Elements(page, function() return pf end)
			table.insert(tabs._list, t)
			tabs._by[name] = t
			tabs.Pages[name] = page
			btn.MouseButton1Click:Connect(function() choose(t) end)
			relayout()
			if not current or opts.Default == name then choose(t, true) end
			return page
		end

		function tabs:Select(x)
			choose(type(x) == "number" and tabs._list[x] or tabs._by[x])
		end
		function tabs:Get() return current and current.name or nil end

		for _, n in ipairs(names) do tabs:AddTab(n) end
		return tabs
	end

	function self:AddConfigManager(o)
		o = o or {}
		local list = self:AddDropdown({
			Title = o.Title or "预设配置列表", Description = o.Description or "选择工作区中已保存的本地配置文件",
			Flag = o.Flag or "_config_select", Values = CialloHub:ListConfigs(),
		})
		table.insert(CialloHub._configLists, list)
		local nameBox = self:AddTextbox({
			Title = "新建配置名称", Description = "输入新配置名称，重名将自动添加序号",
			Flag = (o.Flag and (o.Flag .. "_name")) or "_config_name", Placeholder = "例如：默认配置",
		})
		local function selected()
			local name = list:Get()
			if not name then CialloHub:Warning("未选择配置", "请先在下拉列表中选择一个配置文件。") end
			return name
		end

		self:AddButton({
			Title = "保存为新配置", Description = "使用上方输入的名称创建并保存当前设置",
			Callback = function()
				local name = CialloHub:CreateConfig(nameBox:Get())
				if name then
					nameBox:Set("")
					CialloHub:Success("配置已保存", "成功创建配置：" .. name)
				else
					CialloHub:Error("保存失败", "当前执行器不支持本地文件写入 (writefile)。")
				end
			end,
		})
		self:AddButton({
			Title = "覆盖选中配置", Description = "将当前界面的所有参数覆盖保存至选中的配置",
			Callback = function()
				local name = selected()
				if not name then return end
				if CialloHub:UpdateConfig(name) then
					CialloHub:Success("配置已更新", "已覆盖保存至：" .. name)
				else
					CialloHub:Error("更新失败", "当前执行器不支持本地文件写入 (writefile)。")
				end
			end,
		})
		self:AddButton({
			Title = "加载选中配置", Description = "读取选中配置文件并同步应用所有设置",
			Callback = function()
				local name = selected()
				if not name then return end
				if CialloHub:LoadConfig(name) then
					CialloHub:Success("配置已加载", "当前已应用：" .. name)
				else
					CialloHub:Warning("文件不存在", "该配置文件已被移除或损坏。")
					CialloHub:RefreshConfigs()
				end
			end,
		})
		self:AddButton({
			Title = "删除选中配置", Description = "从本地工作区永久移除选中的配置文件",
			Callback = function()
				local name = selected()
				if not name then return end
				local win = CialloHub.Windows[#CialloHub.Windows]
				win:Dialog({
					Title = "确认删除配置？",
					Content = '即将从本地永久删除配置文件 "' .. name .. '"，该操作无法撤销。',
					Buttons = {
						{ Title = "确认删除", Primary = true, Callback = function()
							if CialloHub:DeleteConfig(name) then CialloHub:Info("配置已删除", "已移除：" .. name) end
						end },
						{ Title = "取消" },
					},
				})
			end,
		})
		return list
	end
end

local IntroFallbackIcons = { "lucide-sparkles", "lucide-zap", "lucide-star", "sparkles", "zap", "star" }

local function playIntro(gui)
	pcall(function()
		local edge, stageH = 12, 76
		local iconSize = 76
		local titleText = INTRO_TITLE
		local subText = INTRO_SUBTITLE
		local function fx(obj, props, t, style, dir)
			TS:Create(obj, TweenInfo.new(t, style or Enum.EasingStyle.Quint, dir or Enum.EasingDirection.Out), props):Play()
		end

		local dim = New("Frame", {
			Name = "Intro", Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.fromRGB(8, 8, 10),
			BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 600, Parent = gui,
		})
		local icon = New("ImageLabel", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(iconSize, iconSize), BackgroundTransparency = 1,
			ScaleType = Enum.ScaleType.Fit, ImageTransparency = 1, ZIndex = 3, Parent = dim,
		})

		local introSrc = CialloHub.IntroIcon or INTRO_ICON
		local hasIcon = (type(introSrc) == "string" and introSrc ~= "") and loadIcon(icon, introSrc) or false
		if not hasIcon then
			for _, key in ipairs(IntroFallbackIcons) do
				local id = IconAssets[key]
				if type(id) == "string" then
					icon.Image = id
					icon.ImageColor3 = T.Accent
					hasIcon = true
					break
				end
			end
		end
		if not hasIcon then
			icon:Destroy()
			iconSize, edge = 0, 0
		end

		local fullW = math.ceil(textWidth(titleText, 30, T.FontBlack)) + 6
		local totalW = iconSize + edge + fullW
		local startX = (totalW - iconSize) / 2
		local tx = iconSize + edge

		local stage = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromOffset(totalW, stageH),
			BackgroundTransparency = 1, Parent = dim,
		})
		local scale = New("UIScale", { Scale = 0.6 })
		if hasIcon then
			scale.Parent = icon
			icon.Position = UDim2.fromOffset(totalW / 2, stageH / 2)
			icon.Parent = stage
		end
		local titleClip = New("Frame", {
			Position = UDim2.fromOffset(startX + tx, 11), Size = UDim2.fromOffset(0, 34), BackgroundTransparency = 1,
			ClipsDescendants = true, ZIndex = 2, Parent = stage,
		})
		local titleLabel = Text(titleClip, titleText, {
			Position = UDim2.fromOffset(-fullW, 0), Size = UDim2.fromOffset(fullW, 34), Font = T.FontBlack,
			TextSize = 30, RawSize = true, TextColor3 = T.Text, TextXAlignment = Enum.TextXAlignment.Left,
		})
		local subClip = New("Frame", {
			Position = UDim2.fromOffset(tx, 47), Size = UDim2.fromOffset(fullW, 18), BackgroundTransparency = 1,
			ClipsDescendants = true, ZIndex = 2, Parent = stage,
		})
		local subLabel = Text(subClip, subText, {
			Position = UDim2.fromOffset(0, -18), Size = UDim2.fromOffset(fullW, 18), TextSize = 14, RawSize = true, TextColor3 = T.Accent,
			TextXAlignment = Enum.TextXAlignment.Center, TextTransparency = 1,
		})

		task.wait(0.15)

		fx(dim, { BackgroundTransparency = 0.18 }, 0.6, Enum.EasingStyle.Quad)
		if hasIcon then
			fx(icon, { ImageTransparency = 0 }, 0.6, Enum.EasingStyle.Quad)
			fx(scale, { Scale = 1 }, 0.9, Enum.EasingStyle.Back)
		end
		task.wait(hasIcon and 1.7 or 0.4)
		if hasIcon then
			fx(icon, { Position = UDim2.fromOffset(iconSize / 2, stageH / 2) }, 1.2)
		end
		fx(titleClip, { Position = UDim2.fromOffset(tx, 11), Size = UDim2.fromOffset(fullW, 34) }, 1.2)
		fx(titleLabel, { Position = UDim2.fromOffset(0, 0) }, 1.2)
		task.wait(0.9)
		fx(subLabel, { Position = UDim2.fromOffset(0, 0), TextTransparency = 0 }, 0.9)
		task.wait(2.4)
		fx(dim, { BackgroundTransparency = 1 }, 0.6, Enum.EasingStyle.Quad)
		if hasIcon then
			fx(icon, { ImageTransparency = 1 }, 0.6, Enum.EasingStyle.Quad)
		end
		fx(titleLabel, { TextTransparency = 1 }, 0.6, Enum.EasingStyle.Quad)
		fx(subLabel, { TextTransparency = 1 }, 0.6, Enum.EasingStyle.Quad)
		task.wait(0.7)
	end)
	local left = gui.Parent and gui:FindFirstChild("Intro")
	if left then left:Destroy() end
end

function CialloHub:_Prune()
	local function keep(list, get)
		local w = 0
		for i = 1, #list do
			local e = list[i]
			local inst = get(e)
			if inst and inst:IsDescendantOf(game) then
				w = w + 1
				list[w] = e
			end
		end
		for i = #list, w + 1, -1 do list[i] = nil end
	end
	keep(self._binds, function(e) return e[1] end)
	keep(self._glass, function(e) return e[1] end)
	keep(self._glassLayers, function(e) return e[1] end)
	keep(self._tips, function(e) return e.label end)
	keep(self._index, function(e) return e.frame end)
	keep(self._scrollers, function(e) return e.holder end)
	local ps, pw = self._paints, 0
	for i = 1, #ps do
		local p = ps[i]
		if not p[2] or p[2]:IsDescendantOf(game) then
			pw = pw + 1
			ps[pw] = p
		end
	end
	for i = #ps, pw + 1, -1 do ps[i] = nil end
end

function CialloHub:CreateWindow(o)
	o = o or {}
	if o.ToggleKey then self.ToggleKeys = { o.ToggleKey } end
	if o.ToggleKeys then self.ToggleKeys = o.ToggleKeys end
	if type(o.Themes) == "table" then self:AddThemes(o.Themes) end
	if o.Theme then
		self._wantTheme = o.Theme
		self:SetTheme(o.Theme)
	end
	if o.Glass ~= nil then self:SetGlass(o.Glass) end

	local parent = (gethui and gethui()) or (pcall(function() return CoreGui.Name end) and CoreGui) or Players.LocalPlayer:WaitForChild("PlayerGui")
	local gui = New("ScreenGui", {
		Name = "CialloHub", ResetOnSpawn = false, DisplayOrder = 2147483647,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling, IgnoreGuiInset = true,
	})
	pcall(function() gui.OnTopOfCoreBlur = true end)
	if syn and syn.protect_gui then pcall(syn.protect_gui, gui) end
	gui.Parent = parent
	self.Gui = gui
	task.spawn(function()
		while gui.Parent do
			task.wait(6)
			pcall(CialloHub._Prune, CialloHub)
		end
	end)
	setupTooltip(gui)

	local size = o.Size or Vector2.new(820, 520)
	local tabW = o.TabWidth or 200
	local fullW, miniW = tabW, o.CollapsedWidth or 60
	local collapsed = false
	local collapseHooks = {}
	local setCollapsed
	local main = New("Frame", {
		Size = UDim2.fromOffset(size.X, size.Y), Position = UDim2.new(0.5, -size.X / 2, 0.5, -size.Y / 2),
		BackgroundColor3 = "$Window", ClipsDescendants = false, Parent = gui,
	}, { Corner(8), Stroke("Border", 0.1) })
	local scale = New("UIScale", { Parent = main })
	local function maxFit()
		local vp = gui.AbsoluteSize
		if vp.X <= 0 or vp.Y <= 0 then
			local cam = workspace.CurrentCamera
			vp = cam and cam.ViewportSize or Vector2.new(1280, 720)
		end
		return Vector2.new(math.max(math.min(size.X, vp.X - 40), 320), math.max(math.min(size.Y, vp.Y - 40), 240))
	end
	local startFit = maxFit()
	main.Size = UDim2.fromOffset(startFit.X, startFit.Y)
	main.Position = UDim2.new(0.5, -startFit.X / 2, 0.5, -startFit.Y / 2)
	connect(gui:GetPropertyChangedSignal("AbsoluteSize"), function()
		local m = maxFit()
		local cur = main.Size
		if cur.X.Offset > m.X or cur.Y.Offset > m.Y then
			main.Size = UDim2.fromOffset(math.min(cur.X.Offset, m.X), math.min(cur.Y.Offset, m.Y))
		end
	end)
	local function snapMain()
		if scale.Scale ~= 1 then return end
		local a = main.AbsolutePosition
		local dx, dy = math.floor(a.X + 0.5) - a.X, math.floor(a.Y + 0.5) - a.Y
		if math.abs(dx) < 0.01 and math.abs(dy) < 0.01 then return end
		local p = main.Position
		main.Position = UDim2.new(p.X.Scale, p.X.Offset + dx, p.Y.Scale, p.Y.Offset + dy)
	end

	local sidebar = New("Frame", {
		Size = UDim2.new(0, tabW, 1, -24), BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = main,
	}, { Corner(8) })
	GlassLayer(sidebar, "Side", {
		{ AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 0), Size = UDim2.new(0, 12, 1, 0) },
		{ AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 12) },
	})
	New("Frame", {
		AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 0), Size = UDim2.new(0, 12, 1, 0),
		BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = sidebar,
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 12),
		BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = sidebar,
	})
	New("Frame", {
		AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, 0, 0, 0), Size = UDim2.new(0, 1, 1, 0),
		BackgroundColor3 = "$Border", BorderSizePixel = 0, Parent = sidebar,
	})
	local logoClip = New("Frame", {
		Size = UDim2.new(1, 0, 0, 64), BackgroundTransparency = 1, BorderSizePixel = 0, ClipsDescendants = true, Parent = sidebar,
	})
	local logo = New("Frame", {
		Size = UDim2.new(1, 12, 1, 0), BackgroundColor3 = "$Accent", BorderSizePixel = 0, Parent = logoClip,
	}, {
		Corner(8),
		New("UIGradient", {
			Rotation = 90,
			Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, 0.78), NumberSequenceKeypoint.new(1, 1) }),
		}),
	})
	local logoText = Text(logoClip, TC(o.Title or o.Name or "Ciallo Hub"), {
		Position = UDim2.fromOffset(18, 0), Size = UDim2.new(1, -18, 1, 0), Font = o.LogoFont or T.FontBold,
		TextSize = 22, TextColor3 = "$Accent",
	})
	New("UIStroke", { Color = "$Accent", Transparency = 0.8, Thickness = 1, Parent = logoText })
	local logoFull = TC(o.Title or o.Name or "Ciallo Hub")
	local logoImg = New("ImageLabel", {
		AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromOffset(36, 36),
		BackgroundTransparency = 1, ScaleType = Enum.ScaleType.Fit, Visible = false, Parent = logoClip,
	})
	local logoImgOk = false
	local function applyLogo()
		local showImg = collapsed and logoImgOk
		logoImg.Visible = showImg
		logoText.Visible = not showImg
	end
	task.spawn(function()
		local logoSrc = o.Logo or o.Icon or CialloHub.IntroIcon or INTRO_ICON
		logoImgOk = loadIcon(logoImg, logoSrc)
		applyLogo()
	end)

	local profileH = o.Profile ~= false and 56 or 0
	local profileInfo
	local side = New("ScrollingFrame", {
		Position = UDim2.fromOffset(0, 70), Size = UDim2.new(1, 0, 1, -(70 + profileH)), BackgroundTransparency = 1, BorderSizePixel = 0,
		ScrollBarThickness = 0, AutomaticCanvasSize = Enum.AutomaticSize.Y, CanvasSize = UDim2.new(), Parent = sidebar,
	}, { List(4), Pad(12, 6, 12, 12) })
	if profileH > 0 then
		local lp = Players.LocalPlayer
		local pf = New("Frame", {
			AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, profileH),
			BackgroundTransparency = 1, Parent = sidebar,
		})
		New("Frame", {
			Position = UDim2.fromOffset(12, 0), Size = UDim2.new(1, -24, 0, 1), BackgroundColor3 = "$Border",
			BackgroundTransparency = 0.3, BorderSizePixel = 0, Parent = pf,
		})
		local av = New("ImageLabel", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 14, 0.5, 0), Size = UDim2.fromOffset(32, 32),
			BackgroundColor3 = "$Element", Image = "", Parent = pf,
		}, { Corner(16), Stroke("Border", 0.3) })
		if lp then av.Image = "rbxthumb://type=AvatarHeadShot&id=" .. lp.UserId .. "&w=150&h=150" end
		profileInfo = New("Frame", {
			Position = UDim2.fromOffset(54, 0), Size = UDim2.new(1, -62, 1, 0), BackgroundTransparency = 1,
			ClipsDescendants = true, Parent = pf,
		})
		local nameL = Text(profileInfo, lp and lp.DisplayName or "Player", {
			Position = UDim2.fromOffset(0, 10), Size = UDim2.new(1, 0, 0, 18), Font = T.FontBold, TextSize = 12,
			TextTruncate = Enum.TextTruncate.AtEnd,
		})
		local handleL = Text(profileInfo, "@" .. (lp and lp.Name or "player"), {
			Position = UDim2.fromOffset(0, 28), Size = UDim2.new(1, 0, 0, 16), TextSize = 11, TextColor3 = "$Sub",
			TextTruncate = Enum.TextTruncate.AtEnd,
		})
		attachTip(nameL, lp and lp.DisplayName or "Player", true)
		attachTip(handleL, "@" .. (lp and lp.Name or "player"), true)
	end

	local header = New("Frame", {
		Position = UDim2.fromOffset(tabW + 1, 0), Size = UDim2.new(1, -(tabW + 1), 0, 60),
		BackgroundColor3 = "$Panel", GlassKey = "Hide", BorderSizePixel = 0, Parent = main,
	}, { Corner(8) })
	GlassLayer(header, "Panel", {
		{ Size = UDim2.new(0, 12, 1, 0) },
		{ AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 12) },
	})
	New("Frame", {
		Size = UDim2.new(0, 12, 1, 0), BackgroundColor3 = "$Panel", GlassKey = "Hide", BorderSizePixel = 0, Parent = header,
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 12),
		BackgroundColor3 = "$Panel", GlassKey = "Hide", BorderSizePixel = 0, Parent = header,
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 1),
		BackgroundColor3 = "$Border", BorderSizePixel = 0, Parent = header,
	})
	local searchW = o.Search ~= false and (o.SearchWidth or 180) or 0
	local headerTitle = Text(header, "", {
		Position = UDim2.fromOffset(52, 0), Size = UDim2.new(1, -(148 + searchW), 1, 0), TextColor3 = "$Sub", TextSize = 15,
		RichText = true, TextTruncate = Enum.TextTruncate.AtEnd,
	})
	if o.Collapsible ~= false then
		local cbtn = New("TextButton", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 14, 0.5, 0), Size = UDim2.fromOffset(28, 28),
			BackgroundColor3 = "$Hover", BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Parent = header,
		}, { Corner(6) })
		local cname
		for _, n in ipairs({ "panel-left", "sidebar", "menu", "align-justify" }) do
			if IconAssets["lucide-" .. n] or IconAssets[n] then
				cname = n
				break
			end
		end
		local cic
		if cname then
			cic = Icon(cbtn, cname, 16, "Sub", { AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5) })
		else
			cic = Text(cbtn, "=", {
				Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, Font = T.FontBold,
				TextSize = 18, TextColor3 = "$Sub",
			})
		end
		local prop = cic:IsA("ImageLabel") and "ImageColor3" or "TextColor3"
		cbtn.MouseEnter:Connect(function() Tween(cbtn, { BackgroundTransparency = 0 }) Tween(cic, { [prop] = T.Text }) end)
		cbtn.MouseLeave:Connect(function() Tween(cbtn, { BackgroundTransparency = 1 }) Tween(cic, { [prop] = T.Sub }) end)
		cbtn.MouseButton1Click:Connect(function() setCollapsed(not collapsed) end)
		table.insert(CialloHub._tips, { label = cbtn, text = "收起 / 展开侧边栏", cut = true })
	end

	local content = New("Frame", {
		Position = UDim2.fromOffset(tabW + 1, 60), Size = UDim2.new(1, -(tabW + 1), 1, -84),
		BackgroundColor3 = "$Panel", BorderSizePixel = 0, ClipsDescendants = true, Parent = main,
	})

	local starsOn = o.Stars ~= false
	local starLayer = New("Frame", {
		Name = "Stars", Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0,
		ClipsDescendants = true, Active = false, Visible = starsOn, Parent = content,
	})
	local stars = {}
	for i = 1, math.max(1, math.floor(o.StarCount or 3)) do
		local f = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(80, 2), BackgroundColor3 = o.StarColor or "$Accent",
			BorderSizePixel = 0, BackgroundTransparency = 1, Visible = false, Parent = starLayer,
		}, {
			Corner(1),
			New("UIGradient", {
				Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(1, 0) }),
			}),
		})
		stars[i] = { frame = f, wait = 2 + math.random() * 4 + i, active = false }
	end

	local function spawnStar(st)
		local sc = scale.Scale
		local w, h = content.AbsoluteSize.X / sc, content.AbsoluteSize.Y / sc
		if w < 10 or h < 10 then
			st.wait = 0.5
			return
		end
		local ang = math.rad(math.random(22, 52))
		local len = math.random(70, 130)
		local x, y
		if math.random() < 0.7 then
			x, y = math.random() * w * 1.1 - w * 0.1, -len * 0.5
		else
			x, y = -len * 0.5, math.random() * h * 0.6
		end
		st.dx, st.dy = math.cos(ang), math.sin(ang)
		st.x, st.y, st.dist, st.speed = x, y, 0, math.random(420, 700)
		st.total = math.min((w + len - x) / st.dx, (h + len - y) / st.dy)
		st.frame.Size = UDim2.fromOffset(len, 2)
		st.frame.Rotation = math.deg(ang)
		st.frame.BackgroundTransparency = 1
		st.frame.Position = UDim2.fromOffset(x, y)
		st.frame.Visible = true
		st.active = true
	end

	connect(RunService.RenderStepped, function(dt)
		if not starsOn or not main.Visible then return end
		dt = math.min(dt, 0.05)
		for _, st in ipairs(stars) do
			if st.active then
				st.dist = st.dist + st.speed * dt
				local p = st.dist / st.total
				if p >= 1 then
					st.active = false
					st.frame.Visible = false
					st.wait = 4 + math.random() * 7
				else
					st.frame.Position = UDim2.fromOffset(st.x + st.dx * st.dist, st.y + st.dy * st.dist)
					st.frame.BackgroundTransparency = 1 - math.min(1, p * 8, (1 - p) * 5) * 0.9
				end
			else
				st.wait = st.wait - dt
				if st.wait <= 0 then spawnStar(st) end
			end
		end
	end)

	local footer = New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 24),
		BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = main,
	}, { Corner(8) })
	GlassLayer(footer, "Side", {
		{ Size = UDim2.new(1, 0, 0, 12) },
	})
	New("Frame", {
		Size = UDim2.new(1, 0, 0, 12), BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = footer,
	})
	New("Frame", { Size = UDim2.new(1, 0, 0, 1), BackgroundColor3 = "$Border", BorderSizePixel = 0, Parent = footer })
	Text(footer, o.SubTitle or "", {
		Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, TextColor3 = "$Sub", TextSize = 12,
	})

	New("Frame", {
		Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Accent", BorderSizePixel = 0, ZIndex = 20, Active = false, Parent = main,
	}, {
		Corner(8),
		New("UIGradient", {
			Rotation = 45,
			Transparency = NumberSequence.new({
				NumberSequenceKeypoint.new(0, 1),
				NumberSequenceKeypoint.new(0.4, 1),
				NumberSequenceKeypoint.new(0.7, 0.95),
				NumberSequenceKeypoint.new(1, 0.82),
			}),
		}),
	})

	setCollapsed = function(v, instant)
		collapsed = v and true or false
		local w = collapsed and miniW or fullW
		local t = instant and 0 or 0.2
		Tween(sidebar, { Size = UDim2.new(0, w, 1, -24) }, t)
		Tween(header, { Position = UDim2.fromOffset(w + 1, 0), Size = UDim2.new(1, -(w + 1), 0, 60) }, t)
		Tween(content, { Position = UDim2.fromOffset(w + 1, 60), Size = UDim2.new(1, -(w + 1), 1, -84) }, t)
		if collapsed then
			logoText.Text = logoFull:match("^" .. utf8.charpattern) or "C"
			logoText.TextXAlignment = Enum.TextXAlignment.Center
			logoText.Position = UDim2.fromOffset(0, 0)
			logoText.Size = UDim2.fromScale(1, 1)
		else
			logoText.Text = logoFull
			logoText.TextXAlignment = Enum.TextXAlignment.Left
			logoText.Position = UDim2.fromOffset(18, 0)
			logoText.Size = UDim2.new(1, -18, 1, 0)
		end
		applyLogo()
		if profileInfo then profileInfo.Visible = not collapsed end
		for _, fn in ipairs(collapseHooks) do fn(collapsed) end
	end
	setCollapsed(o.Collapsed == true, true)

	if o.Resizable ~= false then
		local baseMin = o.MinSize or Vector2.new(math.max(520, math.floor(size.X * 0.72)), math.max(340, math.floor(size.Y * 0.72)))
		local grip = New("TextButton", {
			AnchorPoint = Vector2.new(1, 1), Position = UDim2.new(1, -3, 1, -3), Size = UDim2.fromOffset(18, 18),
			BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 25, Parent = main,
		})
		local dots = {}
		for _, p in ipairs({ { 12, 4 }, { 8, 8 }, { 12, 8 }, { 4, 12 }, { 8, 12 }, { 12, 12 } }) do
			table.insert(dots, New("Frame", {
				Position = UDim2.fromOffset(p[1], p[2]), Size = UDim2.fromOffset(2, 2), BackgroundColor3 = "$Sub",
				BorderSizePixel = 0, ZIndex = 26, Parent = grip,
			}, { Corner(1) }))
		end
		local resizing, startMouse, startSize = false, Vector2.zero, size
		local function dotsColor(c) for _, d in ipairs(dots) do Tween(d, { BackgroundColor3 = c }) end end
		grip.MouseEnter:Connect(function() dotsColor(T.Accent) end)
		grip.MouseLeave:Connect(function() if not resizing then dotsColor(T.Sub) end end)
		grip.InputBegan:Connect(function(i)
			if isPress(i) then
				resizing = true
				startMouse = Vector2.new(i.Position.X, i.Position.Y)
				startSize = Vector2.new(main.Size.X.Offset, main.Size.Y.Offset)
			end
		end)
		connect(UIS.InputChanged, function(i)
			if resizing and isMove(i) then
				local d = (Vector2.new(i.Position.X, i.Position.Y) - startMouse) / scale.Scale
				local maxSize = maxFit()
				local minSize = Vector2.new(math.min(baseMin.X, maxSize.X), math.min(baseMin.Y, maxSize.Y))
				main.Size = UDim2.fromOffset(
					math.clamp(startSize.X + d.X, minSize.X, maxSize.X),
					math.clamp(startSize.Y + d.Y, minSize.Y, maxSize.Y)
				)
			end
		end)
		connect(UIS.InputEnded, function(i)
			if resizing and isPress(i) then
				resizing = false
				dotsColor(T.Sub)
			end
		end)
	end

	local dragging, dragStart, startPos
	local function dragZone(frame)
		frame.InputBegan:Connect(function(i)
			if isPress(i) then dragging, dragStart, startPos = true, i.Position, main.Position end
		end)
	end
	dragZone(header)
	dragZone(logo)
	connect(UIS.InputChanged, function(i)
		if dragging and isMove(i) then
			local d = (i.Position - dragStart) / scale.Scale
			main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
		end
	end)
	connect(UIS.InputEnded, function(i)
		if dragging and isPress(i) then
			dragging = false
			snapMain()
		end
	end)

	local win = { Tabs = {}, Visible = true, Gui = gui }
	win._main, win._scale = main, scale
	table.insert(self.Windows, win)
	main.Visible = false
	win.Visible = false
	win._intro = true

	local function control(iconName, x, cb)
		local b = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, x, 0.5, 0), Size = UDim2.fromOffset(28, 28),
			BackgroundColor3 = "$Hover", BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Parent = header,
		}, { Corner(6) })
		local ic = Icon(b, iconName, 16, "Sub", { AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5) })
		b.MouseEnter:Connect(function() Tween(b, { BackgroundTransparency = 0 }) Tween(ic, { ImageColor3 = T.Text }) end)
		b.MouseLeave:Connect(function() Tween(b, { BackgroundTransparency = 1 }) Tween(ic, { ImageColor3 = T.Sub }) end)
		b.MouseButton1Click:Connect(cb)
	end
	local pickerOverlay
	local function closePicker()
		local ov = pickerOverlay
		if not ov then return end
		pickerOverlay = nil
		CialloHub._glassPaint = nil
		local pop = ov:FindFirstChild("Popup")
		local sc = pop and pop:FindFirstChildOfClass("UIScale")
		if sc then Tween(sc, { Scale = 0.9 }, 0.1) end
		task.delay(0.1, function() ov:Destroy() end)
	end

	local function openPicker()
		if pickerOverlay then
			closePicker()
			return
		end
		local ov = New("TextButton", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 30, Parent = main,
		})
		pickerOverlay = ov
		ov.MouseButton1Click:Connect(closePicker)
		local pop = New("Frame", {
			Name = "Popup", AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -14, 0, 58), Size = UDim2.fromOffset(350, 0),
			AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", GlassKey = "Popup", ZIndex = 31, Parent = ov,
		}, { Corner(8), Stroke("Border", 0), Pad(6, 6, 6, 6), List(2) })
		local sc = New("UIScale", { Scale = 0.9, Parent = pop })
		Tween(sc, { Scale = 1 }, 0.14)
		local title = Text(pop, "界面主题", { Size = UDim2.new(1, 0, 0, 24), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub", ZIndex = 32 })
		Pad(8, 0, 0, 0).Parent = title
		local grid = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, LayoutOrder = 1, ZIndex = 32, Parent = pop,
		}, {
			New("UIGridLayout", { CellSize = UDim2.new(0.5, -2, 0, 34), CellPadding = UDim2.fromOffset(4, 4), SortOrder = Enum.SortOrder.LayoutOrder }),
		})
		local slot = 0
		for _, name in ipairs(ThemeOrder) do
			local th = Themes[name]
			if th then
				local active = CialloHub.ThemeName == name
				slot = slot + 1
				local item = New("TextButton", {
					BackgroundColor3 = "$Hover", BackgroundTransparency = active and 0.35 or 1,
					AutoButtonColor = false, Text = "", ZIndex = 32, LayoutOrder = slot, Parent = grid,
				}, { Corner(6) })
				local chips = New("Frame", {
					AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 10, 0.5, 0), Size = UDim2.fromOffset(36, 16),
					BackgroundTransparency = 1, ZIndex = 33, Parent = item,
				}, {
					New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 2), SortOrder = Enum.SortOrder.LayoutOrder }),
				})
				for _, c in ipairs({ th.Window, th.Hover, th.Accent }) do
					New("Frame", {
						Size = UDim2.fromOffset(10, 16), BackgroundColor3 = c, BorderSizePixel = 0, ZIndex = 33, Parent = chips,
					}, { Corner(3), New("UIStroke", { Color = th.Border, Thickness = 1 }) })
				end
				Text(item, ThemeLabels[name] or name, { Position = UDim2.fromOffset(58, 0), Size = UDim2.new(1, -86, 1, 0), ZIndex = 33 })
				if active then
					Icon(item, "check", 14, "Accent", { AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0), ZIndex = 33 })
				end
				item.MouseEnter:Connect(function() if not active then Tween(item, { BackgroundTransparency = 0.6 }) end end)
				item.MouseLeave:Connect(function() if not active then Tween(item, { BackgroundTransparency = 1 }) end end)
				item.MouseButton1Click:Connect(function()
					CialloHub:SetTheme(name)
					closePicker()
				end)
			end
		end

		CialloHub:_CustomPanel(ov, closePicker, {})

		local glassRow = New("TextButton", {
			Size = UDim2.new(1, 0, 0, 34), BackgroundColor3 = "$Hover", BackgroundTransparency = 0.6,
			AutoButtonColor = false, Text = "", ZIndex = 32, LayoutOrder = 2, Parent = pop,
		}, { Corner(6) })
		Icon(glassRow, "layers", 14, "Accent", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 12, 0.5, 0), ZIndex = 33 })
		Text(glassRow, "毛玻璃特效", { Position = UDim2.fromOffset(36, 0), Size = UDim2.new(1, -90, 1, 0), ZIndex = 33 })
		local gTrack = New("Frame", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0), Size = UDim2.fromOffset(34, 18),
			BackgroundColor3 = glassOn and T.Accent or T.Off, BorderSizePixel = 0, ZIndex = 33, Parent = glassRow,
		}, { Corner(9) })
		local gKnob = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = glassOn and UDim2.new(1, -16, 0.5, 0) or UDim2.new(0, 2, 0.5, 0),
			Size = UDim2.fromOffset(14, 14), BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, ZIndex = 34, Parent = gTrack,
		}, { Corner(7) })
		CialloHub._glassPaint = function()
			if not gTrack.Parent then return end
			Tween(gTrack, { BackgroundColor3 = glassOn and T.Accent or T.Off })
			Tween(gKnob, { Position = glassOn and UDim2.new(1, -16, 0.5, 0) or UDim2.new(0, 2, 0.5, 0) })
		end
		glassRow.MouseButton1Click:Connect(function() CialloHub:SetGlass(not glassOn) end)
	end

	local function openPanel(width, build)
		if pickerOverlay then
			closePicker()
			return
		end
		local ov = New("TextButton", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 30, Parent = main,
		})
		pickerOverlay = ov
		ov.MouseButton1Click:Connect(closePicker)
		local pop = New("Frame", {
			Name = "Popup", AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -14, 0, 58), Size = UDim2.fromOffset(width, 0),
			AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", GlassKey = "Popup", ZIndex = 31, Parent = ov,
		}, { Corner(8), Stroke("Border", 0), Pad(6, 6, 6, 6), List(2) })
		local sc = New("UIScale", { Scale = 0.9, Parent = pop })
		Tween(sc, { Scale = 1 }, 0.14)
		build(pop)
	end

	local function switchRow(parent, iconName, label, order, get, click)
		local btn = New("TextButton", {
			Size = UDim2.new(1, 0, 0, 34), BackgroundColor3 = "$Hover", BackgroundTransparency = 0.6,
			AutoButtonColor = false, Text = "", ZIndex = 32, LayoutOrder = order, Parent = parent,
		}, { Corner(6) })
		Icon(btn, iconName, 14, "Accent", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 12, 0.5, 0), ZIndex = 33 })
		Text(btn, label, { Position = UDim2.fromOffset(36, 0), Size = UDim2.new(1, -90, 1, 0), ZIndex = 33 })
		local on0 = get()
		local track = New("Frame", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0), Size = UDim2.fromOffset(34, 18),
			BackgroundColor3 = on0 and T.Accent or T.Off, BorderSizePixel = 0, ZIndex = 33, Parent = btn,
		}, { Corner(9) })
		local knob = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = on0 and UDim2.new(1, -16, 0.5, 0) or UDim2.new(0, 2, 0.5, 0),
			Size = UDim2.fromOffset(14, 14), BackgroundColor3 = Color3.new(1, 1, 1), BorderSizePixel = 0, ZIndex = 34, Parent = track,
		}, { Corner(7) })
		btn.MouseButton1Click:Connect(click)
		return function()
			if not track.Parent then return end
			local on = get()
			Tween(track, { BackgroundColor3 = on and T.Accent or T.Off })
			Tween(knob, { Position = on and UDim2.new(1, -16, 0.5, 0) or UDim2.new(0, 2, 0.5, 0) })
		end
	end

	local function openSettings()
		openPanel(350, function(pop)
			local title = Text(pop, "界面设置", { Size = UDim2.new(1, 0, 0, 24), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub", ZIndex = 32 })
			Pad(8, 0, 0, 0).Parent = title
			local grid = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, LayoutOrder = 1, ZIndex = 32, Parent = pop,
			}, {
				New("UIGridLayout", { CellSize = UDim2.new(0.5, -2, 0, 34), CellPadding = UDim2.fromOffset(4, 4), SortOrder = Enum.SortOrder.LayoutOrder }),
			})
			local items, slot = {}, 0
			for _, name in ipairs(ThemeOrder) do
				local th = Themes[name]
				if th then
					slot = slot + 1
					local item = New("TextButton", {
						BackgroundColor3 = "$Hover", BackgroundTransparency = CialloHub.ThemeName == name and 0.35 or 1,
						AutoButtonColor = false, Text = "", ZIndex = 32, LayoutOrder = slot, Parent = grid,
					}, { Corner(6) })
					local chips = New("Frame", {
						AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 10, 0.5, 0), Size = UDim2.fromOffset(36, 16),
						BackgroundTransparency = 1, ZIndex = 33, Parent = item,
					}, {
						New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 2), SortOrder = Enum.SortOrder.LayoutOrder }),
					})
					for _, c in ipairs({ th.Window, th.Hover, th.Accent }) do
						New("Frame", {
							Size = UDim2.fromOffset(10, 16), BackgroundColor3 = c, BorderSizePixel = 0, ZIndex = 33, Parent = chips,
						}, { Corner(3), New("UIStroke", { Color = th.Border, Thickness = 1 }) })
					end
					Text(item, ThemeLabels[name] or name, { Position = UDim2.fromOffset(58, 0), Size = UDim2.new(1, -86, 1, 0), ZIndex = 33 })
					local check = Icon(item, "check", 14, "Accent", {
						AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -10, 0.5, 0), ZIndex = 33,
						ImageTransparency = CialloHub.ThemeName == name and 0 or 1,
					})
					items[name] = { btn = item, check = check }
					item.MouseEnter:Connect(function() if CialloHub.ThemeName ~= name then Tween(item, { BackgroundTransparency = 0.6 }) end end)
					item.MouseLeave:Connect(function() if CialloHub.ThemeName ~= name then Tween(item, { BackgroundTransparency = 1 }) end end)
					item.MouseButton1Click:Connect(function() CialloHub:SetTheme(name) end)
				end
			end

			CialloHub:_CustomPanel(pop.Parent, nil, items)

			local function paintThemes()
				for name, it in pairs(items) do
					local on = CialloHub.ThemeName == name
					Tween(it.btn, { BackgroundTransparency = on and 0.35 or 1 })
					Tween(it.check, { ImageTransparency = on and 0 or 1 })
				end
			end

			local paintAll
			local pGlass = switchRow(pop, "layers", "毛玻璃特效", 2, function() return glassOn end, function()
				CialloHub:SetGlass(not glassOn)
			end)
			local pBlur = switchRow(pop, "droplet", "毛玻璃背景模糊", 3, function() return CialloHub.GlassBlur ~= false end, function()
				CialloHub:SetGlassBlur(not (CialloHub.GlassBlur ~= false))
				paintAll()
			end)
			local pKeys = switchRow(pop, "keyboard", "快捷键状态栏", 4, function()
				local k = CialloHub.KeybindList
				return k ~= nil and not k._dead and k.Visible
			end, function()
				local k = CialloHub.KeybindList
				if k and not k._dead then k:Toggle() else CialloHub:CreateKeybindList() end
				paintAll()
			end)
			paintAll = function()
				paintThemes()
				pGlass()
				pBlur()
				pKeys()
			end
			CialloHub._glassPaint = paintAll

		end)
	end

	local function favKey(e)
		return tostring(e.ctx.tabName) .. "|" .. tostring(e.ctx.section or "") .. "|" .. tostring(e.title)
	end

	local function openFavorites()
		openPanel(380, function(pop)
			local title = Text(pop, "收藏夹", { Size = UDim2.new(1, 0, 0, 24), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub", ZIndex = 32 })
			Pad(8, 0, 0, 0).Parent = title
			local sbox = New("Frame", {
				Size = UDim2.new(1, 0, 0, 30), BackgroundColor3 = "$Element", LayoutOrder = 1, ZIndex = 32, Parent = pop,
			}, { Corner(6), Stroke("Border", 0.3) })
			Icon(sbox, "search", 14, "Sub", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 9, 0.5, 0), ZIndex = 33 })
			local input = New("TextBox", {
				Position = UDim2.fromOffset(30, 0), Size = UDim2.new(1, -38, 1, 0), BackgroundTransparency = 1, Text = "",
				PlaceholderText = "搜索功能并点击星标收藏...", PlaceholderColor3 = "$Sub", Font = T.Font, TextSize = 12,
				TextColor3 = "$Text", TextXAlignment = Enum.TextXAlignment.Left, ClearTextOnFocus = false, ZIndex = 33, Parent = sbox,
			})
			local wrap = New("Frame", {
				Size = UDim2.new(1, 0, 0, 250), BackgroundTransparency = 1, LayoutOrder = 2, ZIndex = 32, Parent = pop,
			})
			local list = New("ScrollingFrame", {
				Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 32,
				AutomaticCanvasSize = Enum.AutomaticSize.Y, CanvasSize = UDim2.new(), Parent = wrap,
			}, { List(4), Pad(0, 0, 8, 0) })
			Scroller(list)

			local function jumpTo(e)
				closePicker()
				e.ctx.tab:Select()
				local changed = e.ctx.reveal and e.ctx.reveal()
				task.delay(changed and 0.28 or 0.08, function()
					local page = e.ctx.tab._page
					if not page or not e.frame.Parent then return end
					local y = (e.frame.AbsolutePosition.Y - page.AbsolutePosition.Y) / scale.Scale + page.CanvasPosition.Y - 24
					page.CanvasPosition = Vector2.new(0, math.max(0, y))
					local fl = New("Frame", {
						Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Accent", BackgroundTransparency = 0.7,
						BorderSizePixel = 0, ZIndex = 3, Parent = e.frame,
					}, { Corner(5) })
					Tween(fl, { BackgroundTransparency = 1 }, 0.9)
					task.delay(0.95, function() fl:Destroy() end)
				end)
			end

			local function rebuild()
				for _, c in ipairs(list:GetChildren()) do
					if c:IsA("GuiObject") then c:Destroy() end
				end
				local q = input.Text:lower():match("^%s*(.-)%s*$")
				local n = 0
				for _, e in ipairs(CialloHub._index) do
					if e.ctx.win == win and e.frame.Parent then
						local key = favKey(e)
						local fav = CialloHub.Favorites[key] == true
						local hay = (e.title .. " " .. tostring(e.desc or "")):lower()
						local match = (q == "" and fav) or (q ~= "" and hay:find(q, 1, true) ~= nil)
						if match and n < 60 then
							n = n + 1
							local b = New("TextButton", {
								Size = UDim2.new(1, 0, 0, 40), BackgroundColor3 = "$Element", AutoButtonColor = false, Text = "",
								ZIndex = 33, LayoutOrder = n, Parent = list,
							}, { Corner(5), Stroke("Border", 0.3) })
							Text(b, e.title, {
								Position = UDim2.fromOffset(12, 4), Size = UDim2.new(1, -50, 0, 18), TextTruncate = Enum.TextTruncate.AtEnd, ZIndex = 34,
							})
							Text(b, TC(e.ctx.tabName) .. (e.ctx.section and (" > " .. TC(e.ctx.section)) or ""), {
								Position = UDim2.fromOffset(12, 22), Size = UDim2.new(1, -50, 0, 14), TextSize = 11,
								TextColor3 = "$Sub", TextTruncate = Enum.TextTruncate.AtEnd, ZIndex = 34,
							})
							local star = New("TextButton", {
								AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -6, 0.5, 0), Size = UDim2.fromOffset(28, 28),
								BackgroundTransparency = 1, Text = "", ZIndex = 35, Parent = b,
							})
							Icon(star, "star", 16, fav and "Accent" or "Sub", {
								AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), ZIndex = 36,
								ImageTransparency = fav and 0 or 0.4,
							})
							star.MouseButton1Click:Connect(function()
								CialloHub:ToggleFavorite(key)
								rebuild()
							end)
							b.MouseButton1Click:Connect(function() jumpTo(e) end)
						end
					end
				end
				if n == 0 then
					Text(list, q == "" and "暂无收藏功能，可在上方搜索并点击星标添加。" or "未找到匹配功能", {
						Size = UDim2.new(1, 0, 0, 40), TextWrapped = true, TextColor3 = "$Sub", TextSize = 12, ZIndex = 33,
					})
				end
			end
			input:GetPropertyChangedSignal("Text"):Connect(rebuild)
			rebuild()
		end)
	end

	local nextX = -78
	if o.ThemePicker ~= false then
		control("palette", nextX, openPicker)
		nextX = nextX - 32
	end
	local unusedSettings = nil
	nextX = nextX - 32
	local unused = nil
	control("minus", -46, function() win:Toggle(false) end)
	control("x", -14, function()
		if CialloHub:GetQuitSkip() then
			CialloHub:Destroy()
			return
		end
		win:Dialog({
			Title = "确认关闭界面？",
			Content = "关闭后需要重新运行脚本才能再次呼出 Ciallo Hub，确定要退出吗？",
			Check = { Title = "下次不再提示" },
			Buttons = {
				{ Title = "确认关闭", Primary = true, Callback = function(skip)
					if skip then CialloHub:SetQuitSkip(true) end
					CialloHub:Destroy()
				end },
				{ Title = "取消" },
			},
		})
	end)

	function win:Toggle(state)
		if self._intro then
			if state ~= nil then self._queued = state end
			return
		end
		if state == nil then state = not self.Visible end
		self.Visible = state
		updateBlur()
		if state then
			main.Visible = true
			scale.Scale = 0.94
			Tween(scale, { Scale = 1 }, 0.18)
			task.delay(0.2, snapMain)
		else
			CialloHub._toggleDrop(nil)
			closePicker()
			Tween(scale, { Scale = 0.94 }, 0.12)
			task.delay(0.12, function() if not win.Visible then main.Visible = false end end)
		end
	end

	function win:SetStars(v)
		starsOn = v and true or false
		starLayer.Visible = starsOn
		if not starsOn then
			for _, st in ipairs(stars) do
				st.active = false
				st.frame.Visible = false
				st.wait = 0.5 + math.random() * 2
			end
		end
	end

	function win:GetStars() return starsOn end

	function win:SelectTab(i)
		if self.Tabs[i] then self.Tabs[i]:Select() end
	end

	function win:Dialog(d)
		local ov = New("TextButton", {
			Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.new(0, 0, 0), BackgroundTransparency = 1,
			Text = "", AutoButtonColor = false, ZIndex = 50, Parent = main,
		})
		Tween(ov, { BackgroundTransparency = 0.4 }, 0.15)
		local card = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromOffset(340, 0),
			AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", GlassKey = "Popup", Parent = ov,
		}, { Corner(6), Stroke("Border", 0), Pad(18, 16, 18, 16), List(10) })
		Text(card, TC(d.Title or "提示"), { Size = UDim2.new(1, 0, 0, 22), Font = T.FontBold, TextSize = 16 })
		Text(card, d.Content or "", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, TextWrapped = true,
			TextColor3 = "$Sub", TextYAlignment = Enum.TextYAlignment.Top,
		})
		local checked = false
		if d.Check then
			checked = d.Check.Value and true or false
			local crow = New("Frame", { Size = UDim2.new(1, 0, 0, 22), BackgroundTransparency = 1, Parent = card })
			local paintCheck = Checkbox({
				AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.fromOffset(18, 18),
				BackgroundColor3 = "$Card", Parent = crow,
			})
			Text(crow, TC(d.Check.Title or "下次不再提示"), {
				Position = UDim2.fromOffset(28, 0), Size = UDim2.new(1, -28, 1, 0), TextColor3 = "$Sub",
			})
			paintCheck(checked)
			New("TextButton", { Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", Parent = crow }).MouseButton1Click:Connect(function()
				checked = not checked
				paintCheck(checked)
			end)
		end
		local btns = New("Frame", { Size = UDim2.new(1, 0, 0, 32), BackgroundTransparency = 1, Parent = card }, {
			New("UIListLayout", {
				FillDirection = Enum.FillDirection.Horizontal, HorizontalAlignment = Enum.HorizontalAlignment.Right,
				Padding = UDim.new(0, 8), SortOrder = Enum.SortOrder.LayoutOrder,
			}),
		})
		local function close()
			Tween(ov, { BackgroundTransparency = 1 }, 0.12)
			task.delay(0.12, function() ov:Destroy() end)
		end
		for _, b in ipairs(d.Buttons or { { Title = "确定", Primary = true } }) do
			local btn = New("TextButton", {
				Size = UDim2.fromOffset(92, 32), BackgroundColor3 = b.Primary and "$Accent" or "$Element", AutoButtonColor = false,
				Text = TC(b.Title or "确定"), Font = T.FontBold, TextSize = 12,
				TextColor3 = b.Primary and "$OnAccent" or "$Text", Parent = btns,
			}, { Corner(5), Stroke("Border", b.Primary and 1 or 0.3) })
			btn.MouseButton1Click:Connect(function() close() safe(b.Callback, checked) end)
		end
	end

	local searchBox
	if o.Search ~= false then
		local boxStroke = Stroke("Border", 0.3)
		local collapsedW = 30
		local expanded, hovering = false, false
		local box = New("Frame", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, o.ThemePicker ~= false and -114 or -82, 0.5, 0),
			Size = UDim2.fromOffset(collapsedW, 30), BackgroundColor3 = "$Element", ClipsDescendants = true, Parent = header,
		}, { Corner(6), boxStroke })
		Icon(box, "search", 14, "Sub", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 8, 0.5, 0) })
		searchBox = New("TextBox", {
			Position = UDim2.fromOffset(30, 0), Size = UDim2.new(1, -58, 1, 0), BackgroundTransparency = 1, Text = "",
			PlaceholderText = "搜索功能...", PlaceholderColor3 = "$Sub", Font = T.Font, TextSize = 12, TextColor3 = "$Text",
			TextXAlignment = Enum.TextXAlignment.Left, ClearTextOnFocus = false, ClipsDescendants = true, Parent = box,
		})
		local clear = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -6, 0.5, 0), Size = UDim2.fromOffset(20, 20),
			BackgroundColor3 = "$Hover", BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Visible = false,
			ZIndex = 2, Parent = box,
		}, { Corner(5) })
		local clearIc = Icon(clear, "x", 12, "Sub", { AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), ZIndex = 3 })
		if not clearIc.Visible then
			Text(clear, "x", { Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, TextColor3 = "$Sub", ZIndex = 3 })
		end
		clear.MouseEnter:Connect(function() Tween(clear, { BackgroundTransparency = 0 }) end)
		clear.MouseLeave:Connect(function() Tween(clear, { BackgroundTransparency = 1 }) end)
		local hint = New("Frame", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0), Size = UDim2.fromOffset(18, 18),
			BackgroundColor3 = "$Card", ZIndex = 2, Parent = box,
		}, { Corner(4), Stroke("Border", 0.2) })
		Text(hint, "/", {
			Size = UDim2.fromScale(1, 1), TextXAlignment = Enum.TextXAlignment.Center, TextSize = 11, TextColor3 = "$Sub",
			Font = T.FontBold, ZIndex = 3,
		})
		local function updateAux()
			local want = hovering or searchBox:IsFocused() or searchBox.Text ~= ""
			if want ~= expanded then
				expanded = want
				Tween(box, { Size = UDim2.fromOffset(expanded and searchW or collapsedW, 30) }, 0.22)
			end
			searchBox.PlaceholderText = expanded and "搜索功能..." or ""
			clear.Visible = expanded and searchBox.Text ~= ""
			hint.Visible = expanded and searchBox.Text == "" and not searchBox:IsFocused()
		end
		updateAux()

		connect(UIS.InputChanged, function(i)
			if i.UserInputType ~= Enum.UserInputType.MouseMovement then return end
			local over = win.Visible and inside(box, Vector2.new(i.Position.X, i.Position.Y)) or false
			if over ~= hovering then
				hovering = over
				updateAux()
			end
		end)

		local results = New("ScrollingFrame", {
			Name = "SearchResults", Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Panel", GlassKey = "Popup", BorderSizePixel = 0,
			Visible = false, ZIndex = 10, AutomaticCanvasSize = Enum.AutomaticSize.Y, CanvasSize = UDim2.new(), Parent = content,
		}, { List(6), Pad(20, 18, 20, 20) })
		Scroller(results, true)

		local function flash(frame)
			local fl = New("Frame", {
				Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Accent", BackgroundTransparency = 0.7,
				BorderSizePixel = 0, ZIndex = 3, Parent = frame,
			}, { Corner(5) })
			Tween(fl, { BackgroundTransparency = 1 }, 0.9)
			task.delay(0.95, function() fl:Destroy() end)
		end

		local function jump(e)
			local page = e.ctx.tab._page
			if not page or not e.frame.Parent then return end
			local y = (e.frame.AbsolutePosition.Y - page.AbsolutePosition.Y) / scale.Scale + page.CanvasPosition.Y - 24
			page.CanvasPosition = Vector2.new(0, math.max(0, y))
			flash(e.frame)
		end

		local function esc(s) return (s:gsub("&", "&amp;"):gsub("<", "&lt;"):gsub(">", "&gt;"):gsub('"', "&quot;")) end
		local function hl(text, q)
			text = tostring(text)
			local low, out, pos = text:lower(), {}, 1
			local col = "#" .. T.Accent:ToHex()
			while true do
				local a, b = low:find(q, pos, true)
				if not a then break end
				table.insert(out, esc(text:sub(pos, a - 1)))
				table.insert(out, '<font color="' .. col .. '"><b>' .. esc(text:sub(a, b)) .. "</b></font>")
				pos = b + 1
			end
			table.insert(out, esc(text:sub(pos)))
			return table.concat(out)
		end

		local rows, selIdx = {}, 0
		local function setSel(i, scrollTo)
			selIdx = i
			for n, r in ipairs(rows) do
				local on = n == i
				Tween(r.btn, { BackgroundColor3 = on and T.Hover or T.Element }, 0.08)
				Tween(r.bar, { BackgroundTransparency = on and 0 or 0.6 }, 0.08)
			end
			if scrollTo and rows[i] then
				local k = scale.Scale
				local top = (rows[i].btn.AbsolutePosition.Y - results.AbsolutePosition.Y) / k
				local vh = results.AbsoluteWindowSize.Y / k
				if top < 8 then
					results.CanvasPosition = Vector2.new(0, math.max(0, results.CanvasPosition.Y + top - 8))
				elseif top + 46 > vh - 8 then
					results.CanvasPosition = Vector2.new(0, results.CanvasPosition.Y + top + 46 - vh + 8)
				end
			end
		end

		local function go(e)
			searchBox:ReleaseFocus()
			searchBox.Text = ""
			e.ctx.tab:Select()
			local changed = e.ctx.reveal and e.ctx.reveal()
			task.delay(changed and 0.28 or 0.08, function() jump(e) end)
		end

		local function refresh()
			for _, c in ipairs(results:GetChildren()) do
				if c:IsA("GuiObject") then c:Destroy() end
			end
			rows, selIdx = {}, 0
			local raw = searchBox.Text or ""
			local q = raw:lower():match("^%s*(.-)%s*$")
			results.Visible = q ~= ""
			content:SetAttribute("Covered", results.Visible)
			if q == "" then return end
			CialloHub._toggleDrop(nil)
			local n = 0
			for _, e in ipairs(CialloHub._index) do
				if e.ctx.win == win and e.frame.Parent then
					local hay = (e.title .. " " .. tostring(e.desc or "")):lower()
					if hay:find(q, 1, true) then
						n = n + 1
						local idx = n
						local b = New("TextButton", {
							Size = UDim2.new(1, 0, 0, 46), BackgroundColor3 = "$Element", AutoButtonColor = false, Text = "",
							LayoutOrder = n, Parent = results,
						}, { Corner(5), Stroke("Border", 0.3) })
						local bar = New("Frame", {
							AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.fromOffset(3, 24),
							BackgroundColor3 = "$Accent", BackgroundTransparency = 0.6, BorderSizePixel = 0, Parent = b,
						}, { Corner(2) })
						Text(b, hl(e.title, q), {
							Position = UDim2.fromOffset(14, 5), Size = UDim2.new(1, -28, 0, 18), TextTruncate = Enum.TextTruncate.AtEnd,
							RichText = true,
						})
						local path = TC(e.ctx.tabName) .. (e.ctx.section and (" > " .. TC(e.ctx.section)) or "")
						local descHit = e.desc and tostring(e.desc):lower():find(q, 1, true)
						Text(b, esc(path) .. (descHit and ("  -  " .. hl(e.desc, q)) or ""), {
							Position = UDim2.fromOffset(14, 25), Size = UDim2.new(1, -28, 0, 14), TextSize = 11,
							TextColor3 = "$Sub", TextTruncate = Enum.TextTruncate.AtEnd, RichText = true,
						})
						rows[n] = { btn = b, bar = bar, entry = e }
						b.MouseEnter:Connect(function() setSel(idx) end)
						b.MouseButton1Click:Connect(function() go(e) end)
						if n >= 50 then break end
					end
				end
			end
			if n == 0 then
				Text(results, "未找到与 \"" .. raw .. "\" 相关的功能", {
					Size = UDim2.new(1, 0, 0, 30), TextColor3 = "$Sub", TextTruncate = Enum.TextTruncate.AtEnd,
				})
			else
				setSel(1)
			end
		end

		local swallowUntil = 0
		searchBox:GetPropertyChangedSignal("Text"):Connect(function()
			if os.clock() < swallowUntil and searchBox.Text == "/" then
				swallowUntil = 0
				searchBox.Text = ""
				return
			end
			updateAux()
			refresh()
		end)
		searchBox.Focused:Connect(function()
			Tween(boxStroke, { Color = T.Accent, Transparency = 0 })
			updateAux()
		end)
		searchBox.FocusLost:Connect(function(enter, input)
			Tween(boxStroke, { Color = T.Border, Transparency = 0.3 })
			updateAux()
			if enter then
				if results.Visible and rows[selIdx] then go(rows[selIdx].entry) end
			elseif input and input.KeyCode == Enum.KeyCode.Escape then
				searchBox.Text = ""
			end
		end)
		clear.MouseButton1Click:Connect(function()
			searchBox.Text = ""
			searchBox:CaptureFocus()
		end)

		connect(UIS.InputBegan, function(i, gp)
			if not win.Visible or win._intro then return end
			if searchBox:IsFocused() then
				if #rows > 0 then
					if i.KeyCode == Enum.KeyCode.Down then
						setSel(math.min(#rows, selIdx + 1), true)
					elseif i.KeyCode == Enum.KeyCode.Up then
						setSel(math.max(1, selIdx - 1), true)
					end
				end
				return
			end
			if i.KeyCode == Enum.KeyCode.Slash and not gp and not UIS:GetFocusedTextBox() and not CialloHub._listening then
				swallowUntil = os.clock() + 0.25
				searchBox:CaptureFocus()
			end
		end)
		win._clearSearch = function() if searchBox.Text ~= "" then searchBox.Text = "" end end
		win.FocusSearch = function() searchBox:CaptureFocus() end
	end

	local order = 0
	local function nextOrder()
		order = order + 1
		return order
	end

	local function richEsc(x) return (tostring(x):gsub("&", "&amp;"):gsub("<", "&lt;"):gsub(">", "&gt;")) end
	local crumbTab, crumbSec = "", nil
	local function renderCrumb()
		if crumbSec then
			local sub, txt = "#" .. T.Sub:ToHex(), "#" .. T.Text:ToHex()
			headerTitle.Text = string.format('<font color="%s">%s  &gt;</font>  <font color="%s">%s</font>', sub, richEsc(TC(crumbTab)), txt, richEsc(TC(crumbSec)))
		else
			headerTitle.Text = richEsc(TC(crumbTab))
		end
	end
	CialloHub:_AddPaint(renderCrumb, headerTitle)
	local function setCrumb(tabName, secName)
		crumbTab, crumbSec = tabName, secName
		renderCrumb()
	end
	local function updateCrumb(tab)
		if win._current ~= tab or not tab._sections then return end
		local top = tab._page.AbsolutePosition.Y + 6 * scale.Scale
		local best, bestY
		for _, sct in ipairs(tab._sections) do
			local fr = sct.frame
			if fr.Parent then
				local y = fr.AbsolutePosition.Y
				if y <= top and y + fr.AbsoluteSize.Y > top and (not bestY or y > bestY) then
					best, bestY = sct, y
				end
			end
		end
		local name = best and best.title or nil
		if name ~= crumbSec then setCrumb(crumbTab, name) end
	end

	function win:SetSidebar(v) setCollapsed(v) end
	function win:ToggleSidebar() setCollapsed(not collapsed) end

	function win:AddWindow(a) return CialloHub:AddWindow(a) end
	function win:IsSidebarCollapsed() return collapsed end

	function win:AddCategory(name)
		local holder = New("Frame", { Size = UDim2.new(1, 0, 0, 36), BackgroundTransparency = 1, LayoutOrder = nextOrder(), Parent = side })
		local lbl = Text(holder, TC(name), {
			Size = UDim2.fromScale(1, 1), Font = T.FontBold, TextSize = 12, TextColor3 = "$Sub",
			TextTransparency = 0.25, TextTruncate = Enum.TextTruncate.AtEnd,
		})
		Pad(6, 0, 0, 0).Parent = lbl
		local line = New("Frame", {
			AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.6), Size = UDim2.fromOffset(18, 2),
			BackgroundColor3 = "$Border", BorderSizePixel = 0, Visible = false, Parent = holder,
		}, { Corner(1) })
		local function apply(c)
			lbl.Visible = not c
			line.Visible = c
		end
		table.insert(collapseHooks, apply)
		apply(collapsed)
	end

	function win:AddTab(a, icon, parent)
		local name = type(a) == "table" and (a.Title or a.Name) or a
		if type(a) == "table" then icon = a.Icon end
		local tab = {}
		tab._name, tab._icon, tab._parent = name, icon, parent
		tab._badge = type(a) == "table" and a.Badge or nil
		tab._ctx = { win = win, tab = tab, tabName = parent and (tostring(parent._name) .. " > " .. tostring(name)) or name }
		local btn = New("TextButton", {
			Size = UDim2.new(1, 0, 0, 40), BackgroundColor3 = "$Element", BackgroundTransparency = 1,
			Text = "", AutoButtonColor = false, LayoutOrder = nextOrder(), Visible = parent == nil, Parent = side,
		}, { Corner(5) })
		local pill = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.fromOffset(3, 0),
			BackgroundColor3 = "$Accent", BorderSizePixel = 0, Parent = btn,
		}, { Corner(2) })
		local label = Text(btn, TC(name), {
			Position = UDim2.fromOffset(icon and 44 or 16, 0), Size = UDim2.new(1, icon and -52 or -24, 1, 0),
			TextColor3 = T.Sub, TextTruncate = Enum.TextTruncate.AtEnd,
		})
		attachTip(label, TC(name))
		local img
		if icon then
			img = Icon(btn, icon, 18, "Sub", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 16, 0.5, 0) })
		end
		local tabBadge, tabBw = Badge(btn, type(a) == "table" and a.Badge or nil)
		if tabBadge then
			tabBadge.AnchorPoint = Vector2.new(1, 8 / 15)
			tabBadge.Position = UDim2.new(1, -10, 0.5, 0)
			label.Size = UDim2.new(1, (icon and -52 or -24) - (tabBw + 6), 1, 0)
		end

		local expandedPos, expandedSize = label.Position, label.Size
		local tipEntry = { label = btn, text = TC(name), cut = false }
		table.insert(CialloHub._tips, tipEntry)
		local plainText = label.Text
		local firstChar = (TC(name):match("^" .. utf8.charpattern) or "?"):upper()
		local function applyCollapse(c)
			tipEntry.cut = c
			if tabBadge then tabBadge.Visible = not c end
			if icon then
				label.Visible = not c
				Tween(img, { Position = UDim2.new(0, c and 9 or 16, 0.5, 0) }, 0.2)
			else
				label.Text = c and firstChar or plainText
				label.TextXAlignment = c and Enum.TextXAlignment.Center or Enum.TextXAlignment.Left
				label.Position = c and UDim2.fromOffset(0, 0) or expandedPos
				label.Size = c and UDim2.fromScale(1, 1) or expandedSize
			end
		end
		table.insert(collapseHooks, applyCollapse)
		applyCollapse(collapsed)

		local page = New("ScrollingFrame", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, BorderSizePixel = 0, Visible = false,
			ScrollBarThickness = 2, ScrollBarImageColor3 = "$Accent", AutomaticCanvasSize = Enum.AutomaticSize.Y,
			CanvasSize = UDim2.new(), Parent = content,
		}, { List(14), Pad(20, 18, 20, 20) })
		tab._page = page
		tab._setPage = function(v) page.Visible = v end
		local SUBH = 42
		if parent then
			page.Position = UDim2.fromOffset(0, SUBH)
			page.Size = UDim2.new(1, 0, 1, -SUBH)
		end
		tab._sections = {}
		Scroller(page)
		page:GetPropertyChangedSignal("CanvasPosition"):Connect(function() updateCrumb(tab) end)
		page:GetPropertyChangedSignal("AbsoluteCanvasSize"):Connect(function() updateCrumb(tab) end)
		local full = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
			Visible = false, LayoutOrder = 1, Parent = page,
		}, { List(14) })
		local cols = New("Frame", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
			LayoutOrder = 2, Parent = page,
		}, {
			New("UIListLayout", {
				FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 14), SortOrder = Enum.SortOrder.LayoutOrder,
				VerticalAlignment = Enum.VerticalAlignment.Top,
			}),
		})
		local function column(idx)
			return New("Frame", {
				Size = UDim2.new(0.5, -7, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
				LayoutOrder = idx, Parent = cols,
			}, { List(14) })
		end
		local colL, colR = column(1), column(2)

		local selected = false
		local function paint()
			Tween(btn, { BackgroundTransparency = selected and glassAlpha("Element", 0) or 1 })
			Tween(pill, { Size = UDim2.fromOffset(3, selected and 22 or 0) })
			Tween(label, { TextColor3 = selected and T.Text or T.Sub })
			if img then Tween(img, { ImageColor3 = selected and T.Accent or T.Sub }) end
		end
		local subs, bar, strip, barItems = {}, nil, nil, {}
		local noMain = type(a) == "table" and a.MainTab == false
		tab._subs = subs

		local function paintBar()
			for _, it in ipairs(barItems) do
				local on = it.view == tab._view
				Tween(it.label, { TextColor3 = on and T.Text or T.Sub })
				if it.icon then Tween(it.icon, { ImageColor3 = on and T.Accent or T.Sub }) end
				Tween(it.line, { BackgroundTransparency = on and 0 or 1 })
			end
		end

		local function showView(view)
			if view ~= tab and not (view and view._parent == tab) then view = nil end
			view = view or tab._view or tab
			if view == tab and noMain and subs[1] then view = subs[1] end
			tab._view = view
			page.Visible = view == tab
			for _, s in ipairs(subs) do s._setPage(s == view) end
			win._current = view
			setCrumb(view == tab and name or (tostring(name) .. " > " .. tostring(view._name)), nil)
			task.defer(function() updateCrumb(view) end)
			paintBar()
		end

		if not parent then
			local function addBarItem(view, text, iconName, badge)
				local tw = math.ceil(textWidth(TC(text), fs(13), T.FontBold))
				local b = New("TextButton", {
					Size = UDim2.fromOffset(tw + 30, SUBH - 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false,
					LayoutOrder = #barItems + 1, Parent = strip,
				})
				local ic
				if iconName then
					ic = Icon(b, iconName, 16, "Sub", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 14, 0.5, 0) })
					if not ic.Visible then ic:Destroy() ic = nil end
				end
				local x0 = ic and 38 or 14
				local bd, bw = Badge(b, badge)
				if bd then bd.Position = UDim2.fromOffset(x0 + tw + 6, (SUBH - 1) / 2) end
				b.Size = UDim2.fromOffset(x0 + tw + 16 + (bw and (bw + 6) or 0), SUBH - 1)
				local lbl = Text(b, TC(text), {
					Position = UDim2.fromOffset(x0, 0), Size = UDim2.new(0, tw + 8, 1, 0),
					Font = T.FontBold, TextSize = 13, TextColor3 = T.Sub,
				})
				local line = New("Frame", {
					AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 8, 1, 0), Size = UDim2.new(1, -16, 0, 2),
					BackgroundColor3 = "$Accent", BackgroundTransparency = 1, BorderSizePixel = 0, Parent = b,
				}, { Corner(1) })
				b.MouseEnter:Connect(function() if tab._view ~= view then Tween(lbl, { TextColor3 = T.Text }, 0.1) end end)
				b.MouseLeave:Connect(function() if tab._view ~= view then Tween(lbl, { TextColor3 = T.Sub }, 0.1) end end)
				b.MouseButton1Click:Connect(function()
					CialloHub._toggleDrop(nil)
					tab:Select(view)
				end)
				table.insert(barItems, { view = view, btn = b, label = lbl, icon = ic, line = line })
			end

			function tab:AddSubTab(sa, sicon)
				if not bar then
					bar = New("Frame", {
						Name = "SubTabs", Size = UDim2.new(1, 0, 0, SUBH), BackgroundTransparency = 1, BorderSizePixel = 0,
						Visible = selected, ZIndex = 2, Parent = content,
					})
					New("Frame", {
						AnchorPoint = Vector2.new(0, 1), Position = UDim2.fromScale(0, 1), Size = UDim2.new(1, 0, 0, 1),
						BackgroundColor3 = "$Border", BackgroundTransparency = 0.3, BorderSizePixel = 0, Parent = bar,
					})
					strip = New("ScrollingFrame", {
						Size = UDim2.new(1, 0, 1, -1), BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarThickness = 0,
						ScrollingDirection = Enum.ScrollingDirection.X, AutomaticCanvasSize = Enum.AutomaticSize.X,
						CanvasSize = UDim2.new(), Parent = bar,
					}, {
						New("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 2), SortOrder = Enum.SortOrder.LayoutOrder }),
						Pad(12, 0, 12, 0),
					})
					page.Position = UDim2.fromOffset(0, SUBH)
					page.Size = UDim2.new(1, 0, 1, -SUBH)
					CialloHub:_AddPaint(paintBar, bar)
					if not noMain then
						addBarItem(tab, (type(a) == "table" and a.MainName) or "主面板", type(a) == "table" and a.MainIcon or nil)
					end
				end
				local sub = win:AddTab(sa, sicon, tab)
				table.insert(subs, sub)
				addBarItem(sub, sub._name, sub._icon, sub._badge)
				if selected then showView(tab._view) else paintBar() end
				return sub
			end

			function tab:Select(view)
				if win._clearSearch then win._clearSearch() end
				for _, t in ipairs(win.Tabs) do
					if t ~= tab then t._set(false) end
				end
				selected = true
				if bar then bar.Visible = true end
				showView(view)
				paint()
			end
			function tab:SelectMain() tab:Select(tab) end
			tab._set = function(v)
				selected = v and true or false
				if bar then bar.Visible = selected end
				if not selected then
					page.Visible = false
					for _, s in ipairs(subs) do s._setPage(false) end
				end
				paint()
			end
		else
			function tab:Select() parent:Select(tab) end
			tab._set = function() end
		end

		btn.MouseButton1Click:Connect(function()
			CialloHub._toggleDrop(nil)
			tab:Select()
		end)
		CialloHub:_AddPaint(paint, btn)

		local counter = 0
		local function createCard(co)
			local target
			if co.Side == "Full" then
				target = full
				full.Visible = true
			elseif co.Side == "Left" then
				target = colL
			elseif co.Side == "Right" then
				target = colR
			else
				counter = counter + 1
				target = counter % 2 == 1 and colL or colR
			end
			local card = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", Parent = target,
			}, { Corner(6), Stroke("Border", 0), List(0) })
			local sec = {}
			sec._ctx = { win = win, tab = tab, tabName = tab._ctx.tabName, section = co.Title }
			if co.Title then table.insert(tab._sections, { frame = card, title = co.Title }) end
			local header2, divider
			local collapsible = (co.Title and co.Collapsible) and true or false
			local collapseKey = collapsible and (co.CollapseFlag or ("section:" .. tostring(tab._ctx.tabName) .. ":" .. tostring(co.Title))) or nil
			local arrow, setCollapsed
			if co.Title then
				header2 = New("Frame", { Size = UDim2.new(1, 0, 0, 46), BackgroundTransparency = 1, LayoutOrder = 1, Parent = card })
				local hasIcon = resolveIcon(co.Icon) ~= nil
				if hasIcon then
					Icon(header2, co.Icon, 16, "Accent", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 14, 0.5, 0) })
				end
				local secBadge, secBw = Badge(header2, co.Badge)
				local secLabel = Text(header2, TC(co.Title), {
					Position = UDim2.fromOffset(hasIcon and 40 or 14, 0),
					Size = UDim2.new(1, -((hasIcon and 100 or 76) + (collapsible and 26 or 0) + (secBw and (secBw + 6) or 0)), 1, 0),
					Font = T.FontBold, TextTruncate = Enum.TextTruncate.AtEnd,
				})
				attachBadge(secLabel, secBadge, hasIcon and 40 or 14, 23)
				divider = New("Frame", {
					Size = UDim2.new(1, 0, 0, 1), BackgroundColor3 = "$Border", BackgroundTransparency = 0.2,
					BorderSizePixel = 0, LayoutOrder = 2, Parent = card,
				})
				if collapsible then
					arrow = Icon(header2, "chevron-down", 14, "Sub", {
						AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, co.Toggle and -66 or -14, 0.5, 0), ZIndex = 2,
					})
					local hit = New("TextButton", {
						Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Parent = header2,
					})
					hit.MouseEnter:Connect(function() Tween(arrow, { ImageColor3 = T.Text }) end)
					hit.MouseLeave:Connect(function() Tween(arrow, { ImageColor3 = T.Sub }) end)
					hit.MouseButton1Click:Connect(function() setCollapsed(not CialloHub.Flags[collapseKey]) end)
				end
			end
			local collapsed = false
			local wrap = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), BackgroundTransparency = 1, ClipsDescendants = collapsible, LayoutOrder = 3, Parent = card,
			})
			local body = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = wrap,
			}, { List(6), Pad(16, 12, 16, 14) })
			local function sync() wrap.Size = UDim2.new(1, 0, 0, collapsed and 0 or body.AbsoluteSize.Y) end
			body:GetPropertyChangedSignal("AbsoluteSize"):Connect(sync)
			sync()
			local overlay = New("Frame", {
				Size = UDim2.fromScale(1, 1), BackgroundColor3 = "$Card", BackgroundTransparency = 0.4,
				BorderSizePixel = 0, Active = true, Visible = false, ZIndex = 5, Parent = wrap,
			})
			sec.Body = body
			if collapsible then
				function setCollapsed(v, instant)
					v = v and true or false
					local changed = v ~= collapsed
					collapsed = v
					CialloHub.Flags[collapseKey] = v
					local h = v and 0 or body.AbsoluteSize.Y
					local rot = v and -90 or 0
					if instant then
						wrap.Size = UDim2.new(1, 0, 0, h)
						arrow.Rotation = rot
						divider.Visible = not v
					else
						if not v then divider.Visible = true end
						Tween(wrap, { Size = UDim2.new(1, 0, 0, h) }, 0.2)
						Tween(arrow, { Rotation = rot }, 0.2)
						if v then
							task.delay(0.2, function()
								if collapsed then divider.Visible = false end
							end)
						end
						if changed then safe(co.OnCollapse, v) end
					end
					return changed
				end
				function sec:SetCollapsed(v) setCollapsed(v) end
				function sec:Collapse() setCollapsed(true) end
				function sec:Expand() setCollapsed(false) end
				function sec:ToggleCollapsed() setCollapsed(not collapsed) end
				function sec:IsCollapsed() return collapsed end
				CialloHub.Options[collapseKey] = {
					Set = function(_, v) setCollapsed(v) end,
					Get = function() return collapsed end,
				}
				sec._ctx.reveal = function()
					if collapsed then
						setCollapsed(false)
						return true
					end
				end
				setCollapsed(co.Collapsed == true, true)
			end
			if co.Title and co.Toggle then
				local paintSw = Switch({
					AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -14, 0.5, 0), Size = UDim2.fromOffset(38, 20),
					BackgroundColor3 = "$Off", Parent = header2,
				})
				local state = false
				local function paint2()
					paintSw(state)
					overlay.Visible = co.Dim == true and not state
				end
				function sec:Set(v, silent)
					state = v and true or false
					paint2()
					if co.Flag then CialloHub.Flags[co.Flag] = state end
					if not silent then safe(co.Callback, state) end
				end
				function sec:Get() return state end
				New("TextButton", {
					AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0), Size = UDim2.fromOffset(collapsible and 58 or 70, 46),
					BackgroundTransparency = 1, Text = "", ZIndex = 3, Parent = header2,
				}).MouseButton1Click:Connect(function() sec:Set(not state) end)
				CialloHub:_AddPaint(paint2, header2)
				if co.Flag then
					CialloHub.Flags[co.Flag] = false
					CialloHub.Options[co.Flag] = sec
				end
				sec:Set(co.Default or false, true)
			end
			Elements(sec, function() return body end)
			return sec
		end

		local default
		Elements(tab, function()
			if not default then default = createCard({ Side = "Full" }) end
			return default.Body
		end)

		function tab:AddSection(a2, ic)
			local co = type(a2) == "table" and a2 or { Title = a2, Icon = ic }
			return createCard(co)
		end

		if parent then return tab end
		table.insert(win.Tabs, tab)
		if #win.Tabs == 1 then tab:Select() end
		return tab
	end

	connect(UIS.InputBegan, function(i)
		if CialloHub._listening or UIS:GetFocusedTextBox() then return end
		for _, k in ipairs(CialloHub.ToggleKeys) do
			if i.KeyCode == k then
				win:Toggle()
				return
			end
		end
	end)

	do
		local mb = o.MobileButton
		local want = mb == true or type(mb) == "table"
		if mb == "Auto" then want = UIS.TouchEnabled and not UIS.KeyboardEnabled end
		if want then
			local cfg = type(mb) == "table" and mb or {}
			local bsize = cfg.Size or 48
			local half = bsize / 2
			local draggable = cfg.Draggable ~= false
			local mbtn = New("TextButton", {
				Name = "MobileButton", AnchorPoint = Vector2.new(0.5, 0.5), Size = UDim2.fromOffset(bsize, bsize),
				Position = cfg.Position or UDim2.new(1, -(half + 14), 0.35, 0),
				BackgroundColor3 = "$Accent", BackgroundTransparency = 0.05, Text = "", AutoButtonColor = false,
				Active = true, Visible = false, ZIndex = 100, Parent = gui,
			}, { New("UICorner", { CornerRadius = UDim.new(1, 0) }), Stroke("Border", 0.3) })
			local mic = Icon(mbtn, cfg.Icon or "menu", math.floor(bsize * 0.5), "OnAccent", {
				AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), ZIndex = 101,
			})
			if not mic.Visible then
				mic:Destroy()
				mbtn.Text, mbtn.Font, mbtn.TextSize, mbtn.TextColor3 = "UI", T.FontBold, fs(14), T.OnAccent
			end
			win.MobileButton = mbtn

			local pressing, dragged, pressPos, startPos = false, false, nil, nil
			mbtn.InputBegan:Connect(function(i)
				if not isPress(i) then return end
				pressing, dragged = true, false
				pressPos = i.Position
				startPos = Vector2.new(mbtn.AbsolutePosition.X + half, mbtn.AbsolutePosition.Y + half)
				Tween(mbtn, { BackgroundTransparency = 0.3 }, 0.08)
			end)
			connect(UIS.InputChanged, function(i)
				if not (pressing and draggable and isMove(i)) then return end
				local d = i.Position - pressPos
				if dragged or d.Magnitude > 8 then
					dragged = true
					local s = gui.AbsoluteSize
					mbtn.Position = UDim2.fromOffset(
						math.clamp(startPos.X + d.X, half, math.max(half, s.X - half)),
						math.clamp(startPos.Y + d.Y, half, math.max(half, s.Y - half))
					)
				end
			end)
			connect(UIS.InputEnded, function(i)
				if not (pressing and isPress(i)) then return end
				pressing = false
				Tween(mbtn, { BackgroundTransparency = 0.05 }, 0.12)
				if not dragged then win:Toggle() end
			end)
		end
	end

	task.spawn(function()
		playIntro(gui)
		local waited = 0
		while not iconsReady and waited < 1.5 do
			task.wait(0.1)
			waited = waited + 0.1
		end
		win._intro = nil
		if gui.Parent then
			if win.MobileButton then win.MobileButton.Visible = true end
			local q = win._queued
			win._queued = nil
			win:Toggle(q == nil and true or q)
		end
	end)

	return win
end

local function panelParent()
	return (gethui and gethui()) or (pcall(function() return CoreGui.Name end) and CoreGui) or Players.LocalPlayer:WaitForChild("PlayerGui")
end

local function panelGui()
	local g = CialloHub.PanelGui
	if g and g.Parent then return g end
	g = New("ScreenGui", {
		Name = "CialloHubWindows", ResetOnSpawn = false, DisplayOrder = 2147483646,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling, IgnoreGuiInset = true,
	})
	pcall(function() g.OnTopOfCoreBlur = true end)
	if syn and syn.protect_gui then pcall(syn.protect_gui, g) end
	g.Parent = panelParent()
	CialloHub.PanelGui = g
	return g
end

local function panelText(c)
	if type(c) == "table" then
		local parts = {}
		for i, v in ipairs(c) do parts[i] = tostring(v) end
		return table.concat(parts, "\n")
	end
	return tostring(c == nil and "" or c)
end

local liveFps = 60
local function startLive()
	local c = CialloHub._liveConn
	if c and c.Connected then return end
	local acc, frames = 0, 0
	c = RunService.Heartbeat:Connect(function(dt)
		acc = acc + dt
		frames = frames + 1
		if acc >= 0.5 then
			liveFps = math.floor(frames / acc + 0.5)
			acc, frames = 0, 0
		end
	end)
	CialloHub._liveConn = c
	table.insert(CialloHub._conns, c)
end

local function liveValue(n)
	if n == "fps" then return tostring(liveFps) end
	if n == "ping" then
		local ok, v = pcall(function() return game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() end)
		if ok and v then return math.floor(v + 0.5) .. " ms" end
		return "n/a"
	end
	if n == "players" then return tostring(#Players:GetPlayers()) end
	if n == "maxplayers" then return tostring(Players.MaxPlayers) end
	if n == "time" then return os.date("%H:%M:%S") end
	if n == "date" then return os.date("%d.%m.%Y") end
	if n == "user" then
		local lp = Players.LocalPlayer
		return lp and lp.DisplayName or "Player"
	end
	if n == "username" then
		local lp = Players.LocalPlayer
		return lp and lp.Name or "Player"
	end
	if n == "placeid" then return tostring(game.PlaceId) end
	return nil
end

local function fillLive(str, vars)
	return (str:gsub("{([%w_]+)}", function(name)
		local v = vars[name]
		if v == nil then v = vars[name:lower()] end
		if v ~= nil then
			if type(v) == "function" then
				local ok, r = pcall(v)
				v = ok and r or "?"
			end
			return tostring(v)
		end
		return liveValue(name:lower())
	end))
end

local MarkupColors = {
	red = C(255, 92, 92), green = C(96, 214, 140), blue = C(96, 165, 255), yellow = C(255, 214, 92),
	orange = C(255, 160, 76), purple = C(176, 120, 255), pink = C(255, 120, 170), cyan = C(60, 220, 200),
	white = C(255, 255, 255), gray = C(150, 150, 150), grey = C(150, 150, 150),
}

local function markupColor(t)
	if t == "accent" then return T.Accent end
	if t == "sub" then return T.Sub end
	if t == "text" then return T.Text end
	if MarkupColors[t] then return MarkupColors[t] end
	local hx = t:match("^#(%x%x%x%x%x%x)$")
	if hx then
		local ok, col = pcall(Color3.fromHex, hx)
		if ok then return col end
	end
	return nil
end

local function colorHex(c)
	return string.format("#%02X%02X%02X", math.floor(c.R * 255 + 0.5), math.floor(c.G * 255 + 0.5), math.floor(c.B * 255 + 0.5))
end

local function applyMarkup(str, pass)
	if not pass then
		str = str:gsub("&", "&amp;")
		str = str:gsub("<", "&lt;")
		str = str:gsub(">", "&gt;")
	end
	local open = {}
	str = str:gsub("%[(/?)([%w#]*)%]", function(slash, tag)
		local t = tag:lower()
		if slash == "/" then
			local top = table.remove(open)
			if not top then return "" end
			return "</" .. top .. ">"
		end
		local col = markupColor(t)
		if col then
			table.insert(open, "font")
			return '&lt;font color=&quot;&#39; .. colorHex(col) .. &#39;&quot;&gt;'
		end
		if t == "b" then
			table.insert(open, "b")
			return "&lt;b&gt;"
		end
		if t == "i" then
			table.insert(open, "i")
			return "&lt;i&gt;"
		end
		return nil
	end)
	for i = #open, 1, -1 do str = str .. "</" .. open[i] .. ">" end
	return str
end

function CialloHub:AddWindow(o)
	if type(o) ~= "table" then o = { Title = o } end
	local gui = panelGui()
	self._panels = self._panels or {}
	self._panelZ = (self._panelZ or 0) + 1
	startLive()

	local sz = typeof(o.Size) == "Vector2" and o.Size or nil
	local headerH = 40
	local w = math.max(tonumber(o.Width) or (sz and sz.X) or 300, 140)
	local h = math.max(tonumber(o.Height) or (sz and sz.Y) or 180, 90)
	local resizable = o.Resizable ~= false

	local minV = typeof(o.MinSize) == "Vector2" and o.MinSize or nil
	local maxV = typeof(o.MaxSize) == "Vector2" and o.MaxSize or nil
	local minW = math.max(tonumber(o.MinWidth) or (minV and minV.X) or w, 140)
	local minH = math.max(tonumber(o.MinHeight) or (minV and minV.Y) or h, 90)
	local maxW = math.max(tonumber(o.MaxWidth) or (maxV and maxV.X) or math.max(w * 2.2, 560), minW)
	local maxH = math.max(tonumber(o.MaxHeight) or (maxV and maxV.Y) or math.max(h * 2.6, 460), minH)
	w, h = math.max(w, minW), math.max(h, minH)
	if not resizable then minW, minH, maxW, maxH = 140, 90, math.huge, math.huge end

	local pos = o.Position
	if typeof(pos) == "Vector2" then pos = UDim2.fromOffset(pos.X, pos.Y) end
	if typeof(pos) ~= "UDim2" then
		local n = #self._panels % 6
		pos = UDim2.new(0.5, -w / 2 + n * 26, 0.5, -h / 2 + n * 26)
	end

	local main = New("Frame", {
		Name = "Window", Size = UDim2.fromOffset(w, h), Position = pos,
		BackgroundColor3 = "$Window", ZIndex = self._panelZ, Visible = false, Parent = gui,
	}, { Corner(8), Stroke("Border", 0.1) })
	local scale = New("UIScale", { Scale = 0.94, Parent = main })

	local header = New("Frame", {
		Name = "Header", Size = UDim2.new(1, 0, 0, headerH), BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = main,
	}, { Corner(8) })
	GlassLayer(header, "Side", {
		{ AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 8) },
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 8),
		BackgroundColor3 = "$Side", GlassKey = "Hide", BorderSizePixel = 0, Parent = header,
	})
	New("Frame", {
		AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 0, 1, 0), Size = UDim2.new(1, 0, 0, 1),
		BackgroundColor3 = "$Border", BorderSizePixel = 0, Parent = header,
	})

	local closable = o.Closable ~= false
	local titleX = 14
	if o.Icon then
		local ic = Icon(header, o.Icon, 16, "Accent", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 14, 0.5, 0) })
		if ic.Visible then titleX = 38 else ic:Destroy() end
	end
	local titleLabel = Text(header, TC(o.Title or "Window"), {
		Position = UDim2.fromOffset(titleX, 0), Size = UDim2.new(1, -(titleX + (closable and 44 or 14)), 1, 0),
		Font = T.FontBold, TextSize = 14, TextTruncate = Enum.TextTruncate.AtEnd,
	})

	local body = New("ScrollingFrame", {
		Name = "Body", Position = UDim2.fromOffset(0, headerH), Size = UDim2.new(1, 0, 1, -headerH),
		BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarImageColor3 = "$Sub",
		ScrollingDirection = Enum.ScrollingDirection.Y, CanvasSize = UDim2.fromOffset(0, 0),
		AutomaticCanvasSize = Enum.AutomaticSize.Y, Parent = main,
	})
	Scroller(body)
	local stack = New("Frame", {
		Name = "Stack", Position = UDim2.fromOffset(14, 12), Size = UDim2.new(1, -28, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = body,
	}, { List(8), Pad(0, 0, 0, 16) })

	local panel = { Gui = main, Visible = false }
	local own = {}
	local vars = {}
	local dyn = {}
	local rows = {}
	local rawPass = o.RichText == true

	if type(o.Variables) == "table" then
		for k, v in pairs(o.Variables) do vars[k] = v end
	end

	local function render(raw)
		return applyMarkup(fillLive(raw, vars), rawPass)
	end

	local function bind(inst, get)
		table.insert(dyn, { inst, get })
		inst.Text = render(get())
	end

	local function refresh()
		for i = #dyn, 1, -1 do
			local e = dyn[i]
			if not e[1].Parent then
				table.remove(dyn, i)
			else
				local t = render(e[2]())
				if e[1].Text ~= t then e[1].Text = t end
			end
		end
	end

	local raw = panelText(o.Content)
	local label = Text(stack, "", {
		Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, TextWrapped = true, LayoutOrder = -1000,
		TextYAlignment = Enum.TextYAlignment.Top, TextSize = tonumber(o.TextSize) or 13, RichText = true,
		Visible = raw ~= "",
	})
	bind(label, function() return raw end)

	local build

	local function makeRow(parent, name, value)
		local f = New("Frame", { Size = UDim2.new(1, 0, 0, 22), BackgroundTransparency = 1, Parent = parent })
		local nameL = Text(f, TC(name), {
			Size = UDim2.new(0.5, -6, 1, 0), TextColor3 = "$Sub", TextSize = 12, TextTruncate = Enum.TextTruncate.AtEnd,
		})
		local vraw = panelText(value)
		local valL = Text(f, "", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.fromScale(1, 0), Size = UDim2.new(0.5, 0, 1, 0),
			TextXAlignment = Enum.TextXAlignment.Right, TextTruncate = Enum.TextTruncate.AtEnd,
			Font = T.FontBold, TextSize = 12, RichText = true,
		})
		bind(valL, function() return vraw end)
		local row = {}
		function row:Set(v) vraw = panelText(v) refresh() end
		function row:Get() return vraw end
		function row:SetName(n) nameL.Text = TC(n) end
		function row:SetVisible(v) f.Visible = v and true or false end
		function row:Destroy()
			f:Destroy()
			for k, r in pairs(rows) do if r == row then rows[k] = nil end end
		end
		rows[tostring(name)] = row
		return row
	end

	local function extras(obj, getParent)
		function obj:AddLabel(text)
			local lraw = panelText(text)
			local l = Text(getParent(), "", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, TextWrapped = true,
				TextYAlignment = Enum.TextYAlignment.Top, TextColor3 = "$Sub", TextSize = 12, RichText = true,
			})
			bind(l, function() return lraw end)
			return {
				Set = function(_, t) lraw = panelText(t) refresh() end,
				Get = function() return lraw end,
				SetVisible = function(_, v) l.Visible = v and true or false end,
			}
		end

		function obj:AddRow(name, value)
			return makeRow(getParent(), name, value)
		end

		function obj:AddSection(a, ic)
			local co = type(a) == "table" and a or { Title = a, Icon = ic }
			local sec = {}
			local hasTitle = co.Title ~= nil and co.Title ~= ""
			local collapsible = hasTitle and co.Collapsible == true
			local collapsed = false
			local card = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundColor3 = "$Card", Parent = getParent(),
			}, { Corner(6), Stroke("Border", 0), List(0) })
			local titleLabel, arrow, divider
			if hasTitle then
				local head = New("Frame", { Size = UDim2.new(1, 0, 0, 38), BackgroundTransparency = 1, LayoutOrder = 1, Parent = card })
				local hasIcon = resolveIcon(co.Icon) ~= nil
				local tx = hasIcon and 36 or 12
				if hasIcon then
					Icon(head, co.Icon, 16, "Accent", { AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 12, 0.5, 0) })
				end
				titleLabel = Text(head, TC(co.Title), {
					Position = UDim2.fromOffset(tx, 0), Size = UDim2.new(1, -(tx + (collapsible and 36 or 12)), 1, 0),
					Font = T.FontBold, TextTruncate = Enum.TextTruncate.AtEnd,
				})
				divider = New("Frame", {
					Size = UDim2.new(1, 0, 0, 1), BackgroundColor3 = "$Border", BackgroundTransparency = 0.2,
					BorderSizePixel = 0, LayoutOrder = 2, Parent = card,
				})
				if collapsible then
					arrow = Icon(head, "chevron-down", 14, "Sub", {
						AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -12, 0.5, 0), ZIndex = 2,
					})
					local hit = New("TextButton", {
						Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Parent = head,
					})
					hit.MouseEnter:Connect(function() Tween(arrow, { ImageColor3 = T.Text }) end)
					hit.MouseLeave:Connect(function() Tween(arrow, { ImageColor3 = T.Sub }) end)
					hit.MouseButton1Click:Connect(function() sec:SetCollapsed(not collapsed) end)
				end
			end
			local wrap = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), BackgroundTransparency = 1, ClipsDescendants = collapsible,
				AutomaticSize = collapsible and Enum.AutomaticSize.None or Enum.AutomaticSize.Y, LayoutOrder = 3, Parent = card,
			})
			local sbody = New("Frame", {
				Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1, Parent = wrap,
			}, { List(6), Pad(12, 10, 12, 12) })
			local function bodyH() return sbody.AbsoluteSize.Y / uiScaleOf(sbody) end
			if collapsible then
				local function sync() wrap.Size = UDim2.new(1, 0, 0, collapsed and 0 or bodyH()) end
				sbody:GetPropertyChangedSignal("AbsoluteSize"):Connect(sync)
				function sec:SetCollapsed(v, instant)
					v = v and true or false
					local changed = v ~= collapsed
					collapsed = v
					local h = v and 0 or bodyH()
					local rot = v and -90 or 0
					if instant then
						wrap.Size = UDim2.new(1, 0, 0, h)
						arrow.Rotation = rot
						divider.Visible = not v
					else
						if not v then divider.Visible = true end
						Tween(wrap, { Size = UDim2.new(1, 0, 0, h) }, 0.2)
						Tween(arrow, { Rotation = rot }, 0.2)
						if v then
							task.delay(0.2, function()
								if collapsed then divider.Visible = false end
							end)
						end
						if changed then safe(co.OnCollapse, v) end
					end
				end
				sec:SetCollapsed(co.Collapsed == true, true)
				sync()
			else
				function sec:SetCollapsed() end
			end
			function sec:Collapse() self:SetCollapsed(true) end
			function sec:Expand() self:SetCollapsed(false) end
			function sec:ToggleCollapsed() self:SetCollapsed(not collapsed) end
			function sec:IsCollapsed() return collapsed end
			function sec:SetTitle(t) if titleLabel then titleLabel.Text = TC(t) end end
			function sec:SetVisible(v) card.Visible = v and true or false end
			function sec:Destroy() card:Destroy() end
			build(sec, function() return sbody end)
			return sec
		end

		local baseTabs = obj.AddTabs
		function obj:AddTabs(a)
			local names, opts = {}, {}
			if type(a) == "table" then
				if a.Tabs then
					names = a.Tabs
					for k, v in pairs(a) do if k ~= "Tabs" then opts[k] = v end end
				elseif a[1] then
					names = a
				else
					opts = a
				end
			end
			local tabs = baseTabs(self, opts)
			local baseAdd = tabs.AddTab
			function tabs:AddTab(name)
				local page = baseAdd(tabs, name)
				extras(page, function() return page.Frame end)
				return page
			end
			for _, n in ipairs(names) do tabs:AddTab(n) end
			return tabs
		end
	end

	function build(obj, getParent)
		Elements(obj, getParent)
		extras(obj, getParent)
	end

	build(panel, function() return stack end)

	function panel:AddRow(name, value)
		return makeRow(stack, name, value)
	end

	function panel:SetRow(name, value)
		local r = rows[tostring(name)]
		if r then r:Set(value) return r end
		return self:AddRow(name, value)
	end
	function panel:GetRow(name) return rows[tostring(name)] end
	function panel:RemoveRow(name)
		local r = rows[tostring(name)]
		if r then r:Destroy() end
	end

	if type(o.Rows) == "table" then
		for _, r in ipairs(o.Rows) do
			if type(r) == "table" then panel:AddRow(r[1] or r.Name, r[2] or r.Value) end
		end
	end

	function panel:SetVariable(name, v)
		vars[tostring(name)] = v
		refresh()
	end

	local function on(signal, fn)
		local c = signal:Connect(fn)
		table.insert(own, c)
		table.insert(CialloHub._conns, c)
		return c
	end

	local function raise()
		CialloHub._panelZ = (CialloHub._panelZ or 0) + 1
		main.ZIndex = CialloHub._panelZ
	end

	local function clamp()
		local a, s2, vp = main.AbsolutePosition, main.AbsoluteSize, gui.AbsoluteSize
		local dx = math.clamp(a.X, 80 - s2.X, math.max(vp.X - 80, 80 - s2.X)) - a.X
		local dy = math.clamp(a.Y, 0, math.max(vp.Y - headerH, 0)) - a.Y
		if dx ~= 0 or dy ~= 0 then
			local p0 = main.Position
			main.Position = UDim2.new(p0.X.Scale, p0.X.Offset + dx, p0.Y.Scale, p0.Y.Offset + dy)
		end
	end

	local dragging, dragStart, startPos = false, nil, nil
	header.InputBegan:Connect(function(i)
		if isPress(i) then
			raise()
			if o.Draggable ~= false then
				dragging, dragStart, startPos = true, i.Position, main.Position
			end
		end
	end)
	main.InputBegan:Connect(function(i) if isPress(i) then raise() end end)
	body.InputBegan:Connect(function(i) if isPress(i) then raise() end end)

	local resizing, rStart, rSize = false, nil, nil
	if resizable then
		local grip = New("TextButton", {
			AnchorPoint = Vector2.new(1, 1), Position = UDim2.new(1, -3, 1, -3), Size = UDim2.fromOffset(18, 18),
			BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 25, Parent = main,
		})
		local dots = {}
		for _, p in ipairs({ { 12, 4 }, { 8, 8 }, { 12, 8 }, { 4, 12 }, { 8, 12 }, { 12, 12 } }) do
			table.insert(dots, New("Frame", {
				Position = UDim2.fromOffset(p[1], p[2]), Size = UDim2.fromOffset(2, 2), BackgroundColor3 = "$Sub",
				BorderSizePixel = 0, ZIndex = 26, Parent = grip,
			}, { Corner(1) }))
		end
		grip.InputBegan:Connect(function(i)
			if isPress(i) then
				raise()
				resizing, rStart, rSize = true, i.Position, Vector2.new(main.Size.X.Offset, main.Size.Y.Offset)
			end
		end)
	end

	on(UIS.InputChanged, function(i)
		if dragging and isMove(i) then
			local d = i.Position - dragStart
			main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
			clamp()
		elseif resizing and isMove(i) then
			local d = i.Position - rStart
			local a, vp = main.AbsolutePosition, gui.AbsoluteSize
			local limW = math.max(math.min(maxW, vp.X - a.X), minW)
			local limH = math.max(math.min(maxH, vp.Y - a.Y), minH)
			main.Size = UDim2.fromOffset(math.clamp(rSize.X + d.X, minW, limW), math.clamp(rSize.Y + d.Y, minH, limH))
		end
	end)
	on(UIS.InputEnded, function(i)
		if isPress(i) then dragging, resizing = false, false end
	end)

	function panel:SetTitle(t) titleLabel.Text = TC(t) end
	function panel:SetContent(c)
		raw = panelText(c)
		label.Visible = raw ~= ""
		refresh()
	end
	function panel:Append(c)
		local add = panelText(c)
		raw = raw == "" and add or (raw .. "\n" .. add)
		label.Visible = raw ~= ""
		refresh()
	end
	function panel:GetContent() return raw end
	function panel:SetLimits(minSize, maxSize)
		if typeof(minSize) == "Vector2" then minW, minH = math.max(minSize.X, 140), math.max(minSize.Y, 90) end
		if typeof(maxSize) == "Vector2" then maxW, maxH = math.max(maxSize.X, minW), math.max(maxSize.Y, minH) end
		maxW, maxH = math.max(maxW, minW), math.max(maxH, minH)
		self:SetSize(main.Size.X.Offset, main.Size.Y.Offset)
	end
	function panel:GetSize() return Vector2.new(main.Size.X.Offset, main.Size.Y.Offset) end
	function panel:SetSize(a, b)
		local nw, nh = a, b
		if typeof(a) == "Vector2" then nw, nh = a.X, a.Y end
		nw = math.clamp(tonumber(nw) or main.Size.X.Offset, minW, maxW)
		nh = math.clamp(tonumber(nh) or main.Size.Y.Offset, minH, maxH)
		main.Size = UDim2.fromOffset(nw, nh)
		clamp()
	end
	function panel:SetPosition(p)
		if typeof(p) == "Vector2" then p = UDim2.fromOffset(p.X, p.Y) end
		if typeof(p) == "UDim2" then main.Position = p clamp() end
	end
	function panel:Toggle(state)
		if self._dead then return end
		if state == nil then state = not self.Visible end
		if state == self.Visible then return end
		self.Visible = state
		if state then
			raise()
			refresh()
			main.Visible = true
			scale.Scale = 0.94
			Tween(scale, { Scale = 1 }, 0.18)
		else
			dragging, resizing = false, false
			Tween(scale, { Scale = 0.94 }, 0.12)
			task.delay(0.12, function()
				if not self.Visible and not self._dead then main.Visible = false end
			end)
		end
	end
	function panel:Show() self:Toggle(true) end
	function panel:Hide() self:Toggle(false) end
	function panel:Destroy()
		if self._dead then return end
		self._dead = true
		for _, c in ipairs(own) do pcall(function() c:Disconnect() end) end
		for i, p in ipairs(CialloHub._panels) do
			if p == self then table.remove(CialloHub._panels, i) break end
		end
		main:Destroy()
	end

	if closable then
		local b = New("TextButton", {
			AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0), Size = UDim2.fromOffset(28, 28),
			BackgroundColor3 = "$Hover", BackgroundTransparency = 1, Text = "", AutoButtonColor = false, Parent = header,
		}, { Corner(6) })
		local ic = Icon(b, "x", 16, "Sub", { AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5) })
		b.MouseEnter:Connect(function() Tween(b, { BackgroundTransparency = 0 }) Tween(ic, { ImageColor3 = T.Text }) end)
		b.MouseLeave:Connect(function() Tween(b, { BackgroundTransparency = 1 }) Tween(ic, { ImageColor3 = T.Sub }) end)
		b.MouseButton1Click:Connect(function()
			safe(o.OnClose, panel)
			if o.DestroyOnClose then panel:Destroy() else panel:Hide() end
		end)
	end

	CialloHub:_AddPaint(refresh, main)
	task.spawn(function()
		while not panel._dead and main:IsDescendantOf(game) do
			task.wait(0.25)
			if panel.Visible then refresh() end
		end
	end)

	table.insert(self._panels, panel)
	if o.Visible ~= false then panel:Toggle(true) end
	return panel
end

local function watermarkGui()
	local g = CialloHub.WatermarkGui
	if g and g.Parent then return g end
	g = New("ScreenGui", {
		Name = "CialloHubWatermark", ResetOnSpawn = false, DisplayOrder = 2147483640,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling, IgnoreGuiInset = true,
	})
	pcall(function() g.OnTopOfCoreBlur = true end)
	if syn and syn.protect_gui then pcall(syn.protect_gui, g) end
	g.Parent = panelParent()
	CialloHub.WatermarkGui = g
	return g
end

local function placeWatermark(frame, corner, margin)
	corner = tostring(corner or "TopRight")
	local right = corner:find("Right") ~= nil
	local bottom = corner:find("Bottom") ~= nil
	frame.AnchorPoint = Vector2.new(right and 1 or 0, bottom and 1 or 0)
	frame.Position = UDim2.new(right and 1 or 0, right and -margin or margin, bottom and 1 or 0, bottom and -margin or margin)
end

function CialloHub:CreateWatermark(o)
	if type(o) ~= "table" then o = { Title = o } end
	if self.Watermark and not self.Watermark._dead then self.Watermark:Destroy() end

	local wm = { Visible = o.Visible ~= false }
	local margin = o.Margin or 16
	local interval = o.Interval or 0.5
	local items = {}
	local order = 10

	local root = New("Frame", {
		Name = "Watermark", AutomaticSize = Enum.AutomaticSize.X, Size = UDim2.fromOffset(0, 34),
		BackgroundColor3 = "$Window", BackgroundTransparency = 0.08, BorderSizePixel = 0, Visible = wm.Visible,
		Parent = watermarkGui(),
	}, {
		Corner(8), Stroke("Border", 0.1), Pad(10, 0, 12, 0),
		New("UIListLayout", {
			FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 10), SortOrder = Enum.SortOrder.LayoutOrder,
			VerticalAlignment = Enum.VerticalAlignment.Center,
		}),
	})
	placeWatermark(root, o.Position, margin)
	wm.Gui = root

	local icon = New("ImageLabel", {
		Size = UDim2.fromOffset(20, 20), BackgroundTransparency = 1, ScaleType = Enum.ScaleType.Fit, LayoutOrder = 0, Parent = root,
	})
	local function setIcon(src)
		task.spawn(function()
			if src == false then
				icon.Visible = false
				return
			end
			src = src or INTRO_ICON
			local ok
			if type(src) == "number" or (type(src) == "string" and (src:match("^https?://") or src:match("^%d+$"))) then
				ok = loadIcon(icon, src)
			else
				local img = resolveIcon(src)
				icon.Image = img or ""
				ok = img ~= nil
			end
			icon.Visible = ok and true or false
		end)
	end
	setIcon(o.Icon)

	local titleLabel = Text(root, TC(o.Title or "Ciallo Hub"), {
		AutomaticSize = Enum.AutomaticSize.X, Size = UDim2.new(0, 0, 1, 0), Font = T.FontBold, TextSize = 13, LayoutOrder = 1,
		Visible = o.Title ~= false,
	})

	function wm:Add(name, value)
		order = order + 1
		local it = {}
		local getter
		local holder = New("Frame", {
			AutomaticSize = Enum.AutomaticSize.X, Size = UDim2.new(0, 0, 1, 0), BackgroundTransparency = 1, LayoutOrder = order, Parent = root,
		}, {
			New("UIListLayout", {
				FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder,
				VerticalAlignment = Enum.VerticalAlignment.Center,
			}),
		})
		New("Frame", {
			Size = UDim2.fromOffset(1, 14), BackgroundColor3 = "$Sub", BackgroundTransparency = 0.65, BorderSizePixel = 0,
			LayoutOrder = 1, Parent = holder,
		})
		local nameLabel = Text(holder, TC(name), {
			AutomaticSize = Enum.AutomaticSize.X, Size = UDim2.new(0, 0, 1, 0), TextColor3 = "$Sub", TextSize = 12, LayoutOrder = 2,
			Visible = name ~= nil and name ~= "",
		})
		local valueLabel = Text(holder, "", {
			AutomaticSize = Enum.AutomaticSize.X, Size = UDim2.new(0, 0, 1, 0), Font = T.FontBold, TextSize = 12, LayoutOrder = 3,
		})
		local function show(v) valueLabel.Text = tostring(v == nil and "" or v) end
		function it._refresh()
			if not getter then return end
			local ok, v = pcall(getter)
			if ok then show(v) end
		end
		function it:Set(v)
			if type(v) == "function" then
				getter = v
				it._refresh()
			else
				getter = nil
				show(v)
			end
		end
		function it:SetName(n)
			nameLabel.Text = tostring(n or "")
			nameLabel.Visible = n ~= nil and n ~= ""
		end
		function it:SetVisible(v) holder.Visible = v and true or false end
		function it:Remove()
			for i, x in ipairs(items) do
				if x == it then table.remove(items, i) break end
			end
			holder:Destroy()
		end
		it:Set(value)
		table.insert(items, it)
		return it
	end

	local fps, acc, frames = 60, 0, 0
	local fpsConn = connect(RunService.RenderStepped, function(dt)
		acc = acc + dt
		frames = frames + 1
		if acc >= 0.5 then
			fps = math.floor(frames / acc + 0.5)
			acc, frames = 0, 0
		end
	end)

	local function ping()
		local ok, v = pcall(function() return game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() end)
		if ok and v then return math.floor(v + 0.5) .. " ms" end
		return "n/a"
	end

	function wm:AddFPS() return self:Add("FPS", function() return fps end) end
	function wm:AddPing() return self:Add("延迟", ping) end
	function wm:AddTime() return self:Add(nil, function() return os.date("%H:%M:%S") end) end
	function wm:AddPlayer() return self:Add(nil, function() return Players.LocalPlayer and Players.LocalPlayer.DisplayName or "" end) end
	function wm:AddFlag(name, flag)
		return self:Add(name, function()
			local v = CialloHub.Flags[flag]
			if type(v) == "boolean" then return v and "开启" or "关闭" end
			return v
		end)
	end

	function wm:SetTitle(t)
		titleLabel.Text = TC(t or "")
		titleLabel.Visible = t ~= nil and t ~= false and t ~= ""
	end
	function wm:SetIcon(src) setIcon(src) end
	function wm:SetPosition(corner) placeWatermark(root, corner, margin) end
	function wm:SetVisible(v)
		wm.Visible = v and true or false
		root.Visible = wm.Visible
	end
	function wm:Toggle() wm:SetVisible(not wm.Visible) end
	function wm:Destroy()
		if wm._dead then return end
		wm._dead = true
		pcall(function() fpsConn:Disconnect() end)
		root:Destroy()
		if CialloHub.Watermark == wm then CialloHub.Watermark = nil end
	end

	local builtin = { FPS = wm.AddFPS, Ping = wm.AddPing, Time = wm.AddTime, Player = wm.AddPlayer }
	for _, e in ipairs(o.Items or { "FPS", "Ping" }) do
		if type(e) == "string" and builtin[e] then
			builtin[e](wm)
		elseif type(e) == "table" then
			wm:Add(e.Name or e.Title, e.Value)
		end
	end

	task.spawn(function()
		while not wm._dead do
			for _, it in ipairs(items) do it._refresh() end
			task.wait(interval)
		end
	end)

	self.Watermark = wm
	return wm
end

function CialloHub:CreateKeybindList(o)
	if type(o) ~= "table" then o = {} end
	if self.KeybindList and not self.KeybindList._dead then self.KeybindList:Destroy() end
	self._keybinds = self._keybinds or {}
	local kl = { Visible = o.Visible ~= false }
	local margin = o.Margin or 16
	local onlyActive = o.OnlyActive == true
	local key = o.Key or Enum.KeyCode.K

	local root = New("Frame", {
		Name = "KeybindList", AutomaticSize = Enum.AutomaticSize.Y, Size = UDim2.fromOffset(250, 0),
		BackgroundColor3 = "$Window", BackgroundTransparency = 0.08, BorderSizePixel = 0, Visible = kl.Visible,
		Parent = watermarkGui(),
	}, { Corner(8), Stroke("Border", 0.1), Pad(10, 8, 10, 10), List(4) })
	if o.Position then
		placeWatermark(root, o.Position, margin)
	else
		root.Position = UDim2.new(0, margin, 0.4, 0)
	end
	kl.Gui = root

	Text(root, "快捷键状态", { Size = UDim2.new(1, 0, 0, 18), Font = T.FontBold, TextSize = 13, LayoutOrder = 0 })
	New("Frame", {
		Size = UDim2.new(1, 0, 0, 1), BackgroundColor3 = "$Border", BackgroundTransparency = 0.3, BorderSizePixel = 0,
		LayoutOrder = 1, Parent = root,
	})

	local rows, counter = {}, 0
	local function rowFor(e)
		local r = rows[e]
		if r then return r end
		counter = counter + 1
		local fr = New("Frame", { Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, LayoutOrder = 10 + counter, Parent = root })
		local dot = New("Frame", {
			AnchorPoint = Vector2.new(0, 0.5), Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.fromOffset(6, 6),
			BackgroundColor3 = T.Off, BorderSizePixel = 0, Parent = fr,
		}, { Corner(3) })
		local name = Text(fr, e.name, {
			Position = UDim2.fromOffset(14, 0), Size = UDim2.new(1, -120, 1, 0), TextSize = 12, TextColor3 = T.Sub,
			TextTruncate = Enum.TextTruncate.AtEnd,
		})
		local keyL = Text(fr, "", {
			AnchorPoint = Vector2.new(1, 0), Position = UDim2.fromScale(1, 0), Size = UDim2.new(0, 110, 1, 0), TextSize = 11,
			TextColor3 = T.Sub, TextXAlignment = Enum.TextXAlignment.Right,
		})
		r = { frame = fr, dot = dot, name = name, key = keyL }
		rows[e] = r
		return r
	end

	local function update()
		local list = CialloHub._keybinds
		for i = #list, 1, -1 do
			local e = list[i]
			if not e.frame:IsDescendantOf(game) then
				local r = rows[e]
				if r then r.frame:Destroy() rows[e] = nil end
				table.remove(list, i)
			end
		end
		local shown = 0
		for _, e in ipairs(list) do
			local r = rowFor(e)
			local k = e.key()
			local on = e.state() and true or false
			local show = k ~= nil and (not onlyActive or on)
			r.frame.Visible = show
			if show then
				shown = shown + 1
				local m = tostring(e.mode())
				local mCn = (m == "Toggle" and "切换") or (m == "Hold" and "长按") or (m == "Press" and "触发") or m
				local txt = "[" .. keyName(k) .. "] " .. mCn
				if r.key.Text ~= txt then r.key.Text = txt end
				local dc = on and T.Accent or T.Off
				if r.dc ~= dc then r.dc = dc Tween(r.dot, { BackgroundColor3 = dc }, 0.12) end
				local nc = on and T.Text or T.Sub
				if r.nc ~= nc then r.nc = nc Tween(r.name, { TextColor3 = nc }, 0.12) end
			end
		end
		root.Visible = kl.Visible and (shown > 0 or not onlyActive)
	end

	local dragging, dragStart, startPos
	root.InputBegan:Connect(function(i)
		if isPress(i) then dragging, dragStart, startPos = true, i.Position, root.Position end
	end)
	connect(UIS.InputChanged, function(i)
		if dragging and isMove(i) then
			local d = i.Position - dragStart
			root.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
		end
	end)
	connect(UIS.InputEnded, function(i)
		if dragging and isPress(i) then dragging = false end
	end)

	function kl:SetKey(k) key = k end
	function kl:GetKey() return key end
	function kl:SetVisible(v)
		kl.Visible = v and true or false
		update()
	end
	function kl:Toggle() kl:SetVisible(not kl.Visible) end
	function kl:Destroy()
		if kl._dead then return end
		kl._dead = true
		root:Destroy()
		if CialloHub.KeybindList == kl then CialloHub.KeybindList = nil end
	end

	connect(UIS.InputBegan, function(i, gp)
		if gp or kl._dead or CialloHub._listening or UIS:GetFocusedTextBox() then return end
		if bindMatch(i, key) then kl:Toggle() end
	end)

	task.spawn(function()
		while not kl._dead do
			update()
			task.wait(0.2)
		end
	end)

	self.KeybindList = kl
	update()
	return kl
end

local function encode(v)
	if typeof(v) == "Color3" then return { __t = "Color3", r = v.R, g = v.G, b = v.B } end
	if typeof(v) == "EnumItem" then return { __t = "Enum", e = tostring(v.EnumType), n = v.Name } end
	return v
end

local function decode(v)
	if type(v) == "table" then
		if v.__t == "Color3" then return Color3.new(v.r, v.g, v.b) end
		if v.__t == "Enum" then return Enum[v.e][v.n] end
	end
	return v
end

function CialloHub:SaveConfig(name)
	if not writefile then return false end
	local data = {}
	for k, v in pairs(self.Flags) do
		if type(k) ~= "string" or k:sub(1, 1) ~= "_" then data[k] = encode(v) end
	end
	pcall(function()
		if makefolder and not isfolder(self.ConfigFolder) then makefolder(self.ConfigFolder) end
	end)
	local ok = pcall(writefile, self.ConfigFolder .. "/" .. name .. ".json", HttpService:JSONEncode(data))
	if ok then self:RefreshConfigs(name) end
	return ok
end

function CialloHub:UpdateConfig(name) return self:SaveConfig(name) end

function CialloHub:RefreshConfigs(select)
	local names = self:ListConfigs()
	for _, dd in ipairs(self._configLists) do
		pcall(function()
			local keep = select or dd:Get()
			dd:Refresh(names)
			for _, n in ipairs(names) do
				if n == keep then dd:Set(n, true) break end
			end
		end)
	end
end

function CialloHub:UniqueConfigName(base)
	local existing = {}
	for _, n in ipairs(self:ListConfigs()) do existing[n:lower()] = true end
	if not existing[base:lower()] then return base end
	local i = 1
	while existing[(base .. " (" .. i .. ")"):lower()] do i = i + 1 end
	return base .. " (" .. i .. ")"
end

function CialloHub:CreateConfig(name)
	name = tostring(name or ""):match("^%s*(.-)%s*$")
	name = name:gsub("[^%w _%-]", "")
	if name == "" then name = "Config" end
	name = self:UniqueConfigName(name)
	if self:SaveConfig(name) then return name end
	return nil
end

function CialloHub:ListConfigs()
	local names = {}
	if not (listfiles and isfolder) then return names end
	pcall(function()
		if isfolder(self.ConfigFolder) then
			for _, path in ipairs(listfiles(self.ConfigFolder)) do
				local n = tostring(path):match("([^/\\]+)%.json$")
				if n then table.insert(names, n) end
			end
		end
	end)
	table.sort(names)
	return names
end

function CialloHub:DeleteConfig(name)
	if not (delfile and isfile) then return false end
	local path = self.ConfigFolder .. "/" .. name .. ".json"
	if not isfile(path) then return false end
	local ok = pcall(delfile, path)
	if ok then self:RefreshConfigs() end
	return ok
end

function CialloHub:LoadConfig(name)
	if not (readfile and isfile) then return false end
	local path = self.ConfigFolder .. "/" .. name .. ".json"
	if not isfile(path) then return false end
	local ok, data = pcall(function() return HttpService:JSONDecode(readfile(path)) end)
	if not ok then return false end
	for k, v in pairs(data) do
		local opt = self.Options[k]
		if opt then pcall(opt.Set, opt, decode(v)) end
	end
	return true
end

CialloHub.Favorites = CialloHub.Favorites or {}

function CialloHub:LoadFavorites()
	self.Favorites = {}
	if not (readfile and isfile) then return end
	pcall(function()
		local path = self.ConfigFolder .. "/favorites.json"
		if isfile(path) then
			local data = HttpService:JSONDecode(readfile(path))
			if type(data) == "table" then
				for _, k in ipairs(data) do self.Favorites[k] = true end
			end
		end
	end)
end

function CialloHub:SaveFavorites()
	if not writefile then return end
	local list = {}
	for k in pairs(self.Favorites) do table.insert(list, k) end
	table.sort(list)
	pcall(function()
		if makefolder and isfolder and not isfolder(self.ConfigFolder) then makefolder(self.ConfigFolder) end
	end)
	pcall(writefile, self.ConfigFolder .. "/favorites.json", HttpService:JSONEncode(list))
end

function CialloHub:IsFavorite(key)
	return self.Favorites[key] == true
end

function CialloHub:ToggleFavorite(key)
	if self.Favorites[key] then self.Favorites[key] = nil else self.Favorites[key] = true end
	self:SaveFavorites()
	return self.Favorites[key] == true
end

function CialloHub:GetQuitSkip()
	if not (readfile and isfile) then return false end
	local path = self.ConfigFolder .. "/quit-confirm.json"
	local ok, res = pcall(function()
		if not isfile(path) then return false end
		return tostring(readfile(path)):match("^%s*(.-)%s*$") == "true"
	end)
	return ok and res == true
end

function CialloHub:SetQuitSkip(v)
	if not writefile then return false end
	pcall(function()
		if makefolder and isfolder and not isfolder(self.ConfigFolder) then makefolder(self.ConfigFolder) end
	end)
	return (pcall(writefile, self.ConfigFolder .. "/quit-confirm.json", v and "true" or "false"))
end

function CialloHub:Destroy()
	for _, c in ipairs(self._conns) do pcall(function() c:Disconnect() end) end
	self._glass, self._glassLayers, self.Windows = {}, {}, {}
	updateBlur()
	self._conns, self._binds, self._paints, self._drops, self._configLists, self._tips, self._index = {}, {}, {}, {}, {}, {}, {}
	self._scrollers, self._scrollHooked, self._scrollDrag = {}, nil, nil
	if self._CancelWipes then self:_CancelWipes() end
	if self.Gui then self.Gui:Destroy() end
	if self.PanelGui then self.PanelGui:Destroy() end
	if self.Watermark then self.Watermark._dead = true end
	if self.WatermarkGui then self.WatermarkGui:Destroy() end
	self.WatermarkGui, self.Watermark = nil, nil
	if self.KeybindList then
		self.KeybindList._dead = true
		self.KeybindList = nil
	end
	self._keybinds = {}
	self.PanelGui, self._panels, self._panelZ = nil, {}, 0
	if Notify then pcall(function() Notify:Destroy() end) end
	genv.CialloHub = nil
end

CialloHub:LoadFavorites()
genv.CialloHub = CialloHub

warn("[Ciallo Hub] UI Library Loaded ~ v" .. CialloHub.Version .. " | Ciallo～(∠・ω< )⌒★")

return CialloHub
